# 웹 성능 진단하기

> [nextstep 의 인프라 공방 과정](https://edu.nextstep.camp/c/VI4PhjPA)에 참여하면서 배운 것들을 정리합니다.&#x20;

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption><p><a href="https://edu.nextstep.camp/c/VI4PhjPA">nextstep 의 인프라 공방</a></p></figcaption></figure>

브라우저로 하나의 페이지를 요청하여 응답을 받기까지 시간이 오래 걸릴 수 있는 요소는 크게 두 가지이다.&#x20;

1. 웹 서버에 영향을 받는 경우&#x20;
2. 정적 컨텐츠와 네트워크에 영향을 받는 경우&#x20;

각 구간별로 시간이 오래 걸리는 지점을 파악하고, 분석하여 해결해보자.&#x20;



## 1. 웹 성능 진단하기&#x20;

한 페이지를 요청하여 응답을 받는 속도가 느리다면, 어디서 문제인지 먼저 측정을 해야하지 않나. 그래서 성능 개선을 위해서는 우선 웹 성능을 진단하는 것부터 시작한다.&#x20;

### 1-1. 웹 성능 분석 사이트&#x20;

웹 성능을 분석할 수 있는 사이트는 다양하다.&#x20;

* [WebPageTest](https://www.webpagetest.org/) : 연결을 시도하는 위치, 환경부터 Repeat View(캐싱 등 테스트), 동일한 조건으로 여러 차례 테스트를 할 수 있다. 테스트한 결과는 영상으로도 볼 수 있다.&#x20;
  *

      <figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

      * First Byte Time : 웹 서버에서 받은 컨텐츠의 첫 번째 바이트가 얼마만에 도착했는가?
      * Keep-Alive Enabled : TCP 연결을 재사용하기 위한 Keep-Alive 설정이 되어 있는가?
      * Compress Transfer : 스크립트 파일이 Content-Encoding으로 압축되어 있는가?
      * Compress Image : 이미지를 압축했는가?
      * Cache Static Content : 정적 파일이 캐싱 설정이 되어 있는가?
* [PageSpeed](https://developers.google.com/speed/pagespeed/insights/) : 웹 성능 분석과 함께 구체적으로 어떤 전략을 취하면 좋은지도 가이드를 해준다.&#x20;
  *

      <figure><img src="../../.gitbook/assets/image (4) (5).png" alt=""><figcaption></figcaption></figure>

### 1-2. 웹 성능 예산&#x20;

* [웹 성능 예산](https://addyosmani.com/blog/performance-budgets/)은 보통 정량, 시간, 규칙을 기반으로 산정한다.&#x20;
* 성능 목표에는 정답이 없고, 정하기 나름이다. 하지만 그럼에도 불구하고 [3초의 법칙](https://www.thinkwithgoogle.com/intl/en-ca/marketing-strategies/app-and-mobile/mobile-page-speed-new-industry-benchmarks/)과 같이 통용적으로 사용되는 최소한의 기준선이 있다. 각자의 서비스 나름대로 성능 목표를 설정하는 것이 필요하다.&#x20;
* 예를 들면 아래와 같은 방식으로 예산을 산정한다.&#x20;
  * 메인 페이지의 모든 오브젝트 파일 크기는 10MB 미만으로 제한한다.
  * 모든 웹 페이지의 각 페이지 내 포함된 자바스크립트 크기는 1MB를 넘지 않아야 한다.
  * 검색 페이지에는 2MB 미만의 이미지가 포함되어야합니다.
  * LTE 환경에서의 모바일 기기의 Time to Interactive는 5초 미만이어야 한다.
  * Dom Content Loaded는 10초, First Meaningful Paint는 15초 미만이어야 한다.
  * Lighthouse 성능 감사에서 80 점 이상이어야한다.



## 2. 예산 설정하기&#x20;

성능을 진단하여 현재 상태를 점검했다면, 이제 얼마나 성능을 향상시킬지 예산을 설정해야한다. 아래의 4가지 단계를 차례대로 거쳐 예산을 산정해보자.&#x20;

### 2-1. 예비분석

* 서비스 내에서 가장 중요한 페이지가 무엇인지 생각해본다. 메인 페이지가 될 수도 있고, 가장 사용자 트래픽이 많은 페이지가 될 수도 있겠다.&#x20;
* PageSpeed를 활용하여 Desktop, Mobile 사이트 등에서 측정된 FCP, TTI 등의 지표를 확인한다.&#x20;
  * 서비스별로 desktop, mobile 중 주력 플랫폼이 있을 것이다. 그것에 맞춰서 측정해보자.&#x20;

### 2-2. 경쟁사 분석&#x20;

* 우리의 서비스의 경쟁사들은 얼마나 좋은 성능을 가지고 있는지 분석해본다. 성능 개선점을 정할 수 있는 좋은 기준점이 될 수 있다. (적어도 경쟁사보다 떨어지면 안되지 않을까...?)&#x20;
* 어떤 사이트를 살펴봐야 할지 모를 때는 [Alexa](https://www.alexa.com/), [similarweb](https://www.similarweb.com/) 등을 활용한다.&#x20;
  * 구글 검색할 때, "related :" 키워드를 붙여서 검색하는 것도 하나의 좋은 방법이 될 수 있다.&#x20;
* [연구](https://www.smashingmagazine.com/2015/09/why-performance-matters-the-perception-of-time/#the-need-for-performance-optimization-the-20-rule)에 따르면, 사용자는 응답시간이 20% 이상일 때 차이를 인식한다고 한다. 경쟁사 대비 20% 이상 성능 차이가 나는지 확인해보자.&#x20;

### 2-3. 성능 기준 설정

* 정량 / 시간 / 규칙 등을 기반으로 성능 기준을 세운다.&#x20;
* 모든 페이지에 대해 성능 기준을 세우는 것이 좋으나, 우선은 서비스에 중요한 페이지부터 작성해본다.&#x20;
* 만약 처음이라서 기준을 세우기 어렵다고 느낀다면, 아래와 같이 예산을 잡아볼 수 있다.&#x20;
  * 페이지로드 3초 미만
  * TTI 5초 미만
  * 압축된 리소스 최대 크기 200KB 미만



1. 정량 기준, Quantity based Metric&#x20;
   1. 동영상 최대 크기
   2. HTML, CSS 최대 크기
   3. 스크립트 최대 갯수
   4. 스크립트 최대 크기
   5. 글꼴 최대 갯수
   6. 웹 글꼴 최대 크기
   7. 이미지 최대 크기
2. 시간 기준, Timing based Metric&#x20;
   1. FCP, LCP, TTI, TBT, CLS 등 pagespeed에서 제공하는 데이터
3. 규칙 기준, Rule based Metric&#x20;
   1. WebPageTest, pagespeed 등 웹 사이트에서 측정한 점수를 지표로 사용
      1. 예) pagespeed 에서 성능점수 50점 이상&#x20;

### 2-4. 우선순위 설정

여러 지표들 중, 우선순위를 정해본다.&#x20;

* 사용자에게 컨텐츠가 바르게 노출되고 렌더링하는 것이 중요하다면, FCP(First Content Paint)를 낮게 유지해야한다.&#x20;
* 사용자가 관련 링크를 빠르게 클릭해야할 경우라면 TTI(Time to Interact)의 우선순위가 높을 것이다.&#x20;



## 3. 우수한 성능의 조건&#x20;

1. CDN 덕분에 가까운데서 서비스를 제공하고&#x20;
2. keep-alive 설정으로 connection 을 재사용하고&#x20;
3. 캐싱으로 요청 수를 최소화하며&#x20;
4. gzip 압축을 통해 각 리소스의 전송 인코딩을 최적화하고&#x20;
5. 이미지를 압축하여 패킷 사이즈를 줄여 네트워크 비용을 줄인다.&#x20;
6. 불필요한 다운로드 제거한다.&#x20;
7. 불필요한 작업을 지연 로딩한다.&#x20;
8. 스크립트는 병합하여 요청수를 최소화한다.&#x20;



### 3-1. 위의 조건을 충족하기 위해 두 가지 차원에서 개선을 시도할 수 있다.&#x20;

1. reverse proxy 를 설정하는 것&#x20;
   1. 이를 통해 캐싱설정, keep-alive 설정, gzip 압축, 이미지 압축 등을 시도할 수 있다.&#x20;
2. Javascript 파일을 수정하는 것&#x20;
   1. 이를 통해 불필요한 다운로드를 제거하고, 불필요한 작업을 지연 로딩하며, 스크립트는 병합하여 요청수를 최소화할 수 있다.&#x20;
