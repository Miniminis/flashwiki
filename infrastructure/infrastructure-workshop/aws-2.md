---
description: 2. 부하테스트
---

# aws로 안정적인 인프라 만들기 2

* 부하 테스트
  * 테스트 전제조건 정리
    * 대상 시스템 범위
    * 목푯값 설정 (latency, throughput, 부하 유지기간)
    * 부하 테스트 시 저장될 데이터 건수 및 크기
  * 아래 시나리오 중 하나를 선택하여 스크립트 작성
    * 접속 빈도가 높은 페이지
    * 데이터를 갱신하는 페이지
    * 데이터를 조회하는데 여러 데이터를 참조하는 페이지
  * Smoke, Load, Stress 테스트 후 결과를 기록



장애의 가장 큰 원인은 바로 단일 장애 포인트&#x20;

로드 밸런서, 웹서버, 어플리케이션 서버, 데이터베이스까지 모든 요소를 다중화해야한다.&#x20;



고려할 것&#x20;

1. 사용자&#x20;
2. 처리량&#x20;
3. 시간&#x20;



## 테스트 전제조건&#x20;

1. 대상 시스템 범위&#x20;
2. 목표값 설정&#x20;
   1. latency
   2. throughput&#x20;
   3. 부하 유지기간&#x20;
3. 부하 테스트시 저장될 데이터 건수 및 크기&#x20;
4. 스크립트 작성 : 데이터를 조회하는데 여러 데이터를 참조하는 페이지



## 목푯값 설정

1. 우선 예상 1일 사용자 수(DAU)를 정해봅니다.
   1. naver : [https://www.similarweb.com/website/map.naver.com/#overview](https://www.similarweb.com/website/map.naver.com/#overview)
      1. 월 방문자수 = 48.8M
      2. 방문자수 당 방문 페이지수 = 11.56
      3. Avg Visit Duration = 06:40
   2. 우리의 DAU = 48.8M / 30 = 1.62M = 162만명&#x20;
2. 피크 시간대의 집중률을 예상해봅니다. (최대 트래픽 / 평소 트래픽)
   1. **방문 수 \* 평균 페이지뷰 \* 1페이지당 평균 바이트**
   2. &#x20;**** 최대 트래픽 / 평소 트래픽 = 대략 10배 &#x20;
3. 1명당 1일 평균 접속 혹은 요청수를 예상해봅니다.
   1. 20번&#x20;
4. 이를 바탕으로 Throughput을 계산합니다.

* **Throughput : 1일 평균 rps \~ 1일 최대 rps**
  * 1일 사용자 수(DAU) x 1명당 1일 평균 접속 수 = 1일 총 접속 수
    * 162만명 \* 20번 = 1일 3240만번&#x20;
  * 1일 총 접속 수 / 86,400 (초/일) = 1일 평균 rps
    * 3240만번  / 86,400 초 = **1일 평균 375 rps**&#x20;
  * 1일 평균 rps x (최대 트래픽 / 평소 트래픽) = 1일 최대 rps
    * 375 rps \* 10배 = **1일 최대 3750 rps =** 1초당 3750번 요청
* **Latency : 일반적으로 50\~100ms이하**로 잡는 것이 좋습니다.
  * 50ms&#x20;
* 사용자가 검색하는 데이터의 양, 갱신하는 데이터의 양 등을 파악해둡니다.



## VUser 구하기&#x20;

* Request Rate: measured by the number of requests per second (RPS)
  * 평균 375&#x20;
  * **최대 3750**
* R: the number of requests per VU iteration
  * 1인당 요청수 20 번&#x20;
* T: a value larger than the time needed to complete a VU iteration
  * 20번 \* 0.3 ms + 1000ms(1s) = 1006 ms = 1.006s
* VU: the number of virtual users
* 1일 평균 = 375 rps \* 1.006s / 20 = 18.86 명  = 약 18명&#x20;
* 1일 최대 = 3750 rps \* 1.006s / 20 = 188.625 명 = 약 188명&#x20;

```plaintext
T = (R * http_req_duration) (+ 1s) ; 내부망에서 테스트할 경우 예상 latency를 추가한다

VUser = (목표 rps * T) / R
```

* 가령, 두개의 요청 (**R=2**)이 있고, 왕복시간이 0.5s, 지연시간이 1초라고 가정할 때 (**T=2**), 계산식은 아래와 같다.

> VU = (300 \* 2) / 2 = 300



## 테스트 계획&#x20;

1. smoke test&#x20;
   1. vuser = 1
   2. duration = 1m&#x20;
   3. p(99) < 50ms&#x20;
2. load test 18&#x20;
   1. vuser = 18&#x20;
   2. duration 1m &#x20;
3. load test 200&#x20;
   1. vuser = 200&#x20;
   2. duration 1m&#x20;
4. stress test&#x20;
   1. vuser&#x20;
   2. duration 1m &#x20;



## SmokeTest 결과&#x20;

```javascript
// smoke.js
import http from 'k6/http';
import { check, group, sleep, fail } from 'k6';

export let options = {
    stages: [
        {duration: '1m', target: 1}     //duration 동안 현재 사용자를 target 명으로 유지
    ],
    thresholds: {
        http_req_duration: ['p(99)<1000'], // 99% of requests must complete below 1s
    },
};

const BASE_URL = 'https://infra.subway-mhson.kro.kr';

function getPage(APIS) {
    for (const api of APIS) {
        const response = http.get(api);
        check(response, {
            'response code was 200': (response) => response.status == 200,
        });
        sleep(4);
    }
}

export default function () {
    //출발역 조회
    //도착역 조회
    //경로 검색
    const GET_PAGE_APIS = [
        `${BASE_URL}/stations`,
        `${BASE_URL}/stations`,
        `${BASE_URL}/paths?source=1&target=365`
    ];

    getPage(GET_PAGE_APIS);
};
```

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>



### 테스트 계획 &#x20;

1. 30초 동안 VUser 0명 → 1명 증가.
2. 1분 동안 VUser 1명 유지.
3. 30초 동안 VUser 1명 → 0명 감소.
4. 99%의 요청이 각각 50ms 미만으로 완료되어야 한다.

****

### **테스트 결과**&#x20;

* 모든 요청 성공.
* 95%의 요청 응답시간 `1.11s` 으로 예상 latency 인 50ms 보다 한참 큰 값



## LoadTest 결과&#x20;

보통 30분 - 2시간을 기준으로 테스트하지만, 나는 돈이 없는 가난한 개발자놈... 1분으로 족친다&#x20;

```javascript
// load.js
import http from 'k6/http';
import { check, group, sleep, fail } from 'k6';

export let options = {
    stages: [
        {duration: '1m', target: 100}     //duration 동안 현재 사용자를 target 명으로 유지
    ],
    thresholds: {
        http_req_duration: ['p(99)<50'], // 99% of requests must complete below 50ms
    },
};

const BASE_URL = 'https://infra.subway-mhson.kro.kr';

function getPage(APIS) {
    for (const api of APIS) {
        const response = http.get(api);
        check(response, {
            'response code was 200': (response) => response.status == 200,
        });
        sleep(4);
    }
}

export default function () {
    //출발역 조회
    //도착역 조회
    //경로 검색
    const GET_PAGE_APIS = [
        `${BASE_URL}/stations`,
        `${BASE_URL}/stations`,
        `${BASE_URL}/paths?source=1&target=365`
    ];

    getPage(GET_PAGE_APIS);
};
```

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

결과&#x20;

* 응답은 잘 받았으나&#x20;
* http\_req\_duration 값은 기대치였던 50ms 보다 한참 못미치는 9s 를 기록하였다.&#x20;



CPU 사용 결과

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>



## StressTest 결과&#x20;

500명까지 올려보자! 어떻게 되나 보자\~!\~!!!!

```javascript
// stress.js
import http from 'k6/http';
import { check, group, sleep, fail } from 'k6';

export let options = {
    stages: [
        {duration: '10s', target: 0},
        {duration: '1m', target: 150},     //duration 동안 현재 사용자를 target 명으로 유지
        {duration: '1m', target: 300},
        {duration: '1m', target: 500},
        {duration: '10s', target: 0}
    ],
    thresholds: {
        http_req_duration: ['p(99)<50'], // 99% of requests must complete below 50ms
    },
};

const BASE_URL = 'https://infra.subway-mhson.kro.kr';

function getPage(APIS) {
    for (const api of APIS) {
        const response = http.get(api);
        check(response, {
            'response code was 200': (response) => response.status == 200,
        });
        sleep(4);
    }
}

export default function () {
    //출발역 조회
    //도착역 조회
    //경로 검색
    const GET_PAGE_APIS = [
        `${BASE_URL}/stations`,
        `${BASE_URL}/stations`,
        `${BASE_URL}/paths?source=1&target=365`
    ];

    getPage(GET_PAGE_APIS);
};
```



### 결과&#x20;

좆망했다.&#x20;

테스트 중 체크한 cpu 사용량&#x20;

<figure><img src="../../.gitbook/assets/image (55) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

60% 가량의 패킷이 손실되었음을 확인함&#x20;

실제 응답률은 40%도 안됨&#x20;

중간부터 계속 connection peer 에러 발생&#x20;



## Grafana 대시보드 구성하기&#x20;

### influx DB&#x20;

* [https://datamoney.tistory.com/287](https://datamoney.tistory.com/287)
  * docker 이용하여 설치&#x20;

### grafana 설치&#x20;

* [https://pbj0812.tistory.com/270](https://pbj0812.tistory.com/270)

![](<../../.gitbook/assets/image (1) (5).png>)



```
// Some code

docker run -p 8086:8086 \
-v influxdb:/var/lib/influxdb \
-e DOCKER_INFLUXDB_INIT_MODE=upgrade \
-e DOCKER_INFLUXDB_INIT_USERNAME=admin \ 
-e DOCKER_INFLUXDB_INIT_PASSWORD=admin \
-e DOCKER_INFLUXDB_INIT_ORG=subway \
-e DOCKER_INFLUXDB_INIT_BUCKET=myk6db \
influxdb
```





## 참고&#x20;

* [https://taehee-kim-dev.github.io/backend-performance-improvement/](https://taehee-kim-dev.github.io/backend-performance-improvement/)
* 트래픽, 방문자수 대략 확인&#x20;
  * [https://www.similarweb.com](https://www.similarweb.com)
* [https://velog.io/@max9106/nGrinderPinpoint-test1](https://velog.io/@max9106/nGrinderPinpoint-test1)
* [https://taehee-kim-dev.github.io/backend-performance-improvement/](https://taehee-kim-dev.github.io/backend-performance-improvement/)
