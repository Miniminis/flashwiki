---
description: 1. 화면 응답 개선하기
---

# aws로 안정적인 인프라 만들기 1

대상 사이트 &#x20;

* [https://infra.subway-mhson.kro.kr/](https://infra.subway-mhson.kro.kr/)



해야하는 일&#x20;

1. ~~repository 변경하여 서버 띄우기 - 완료~~&#x20;
2. 화면 응답 개선하기
3. 부하 테스트 진행하기
4. 스케일 아웃 진행하기



## 웹 성능 예산 짜기 + 서버의 목표 응답시간&#x20;

### 1. 웹 성능 예산 작성하기

* [WebPageTest](https://www.webpagetest.org/), [PageSpeed](https://developers.google.com/speed/pagespeed/insights/) 등에서 테스트를 진행한 후, 웹 성능 예산을 작성합니다.

#### \* 경쟁사 관련 자료

* 아래 자료를 참고하여 웹 성능 예산, 부하테스트 목푯값 등을 설계해보세요.

**\* 경쟁사**

* [서울교통공사](http://www.seoulmetro.co.kr/kr/cyberStation.do)
* [네이버지도](https://m.map.naver.com/subway/subwayLine.naver?region=1000)
* [카카오맵](https://m.map.kakao.com/)

**\* 언론보도**

* [데이터로보는 서울시 대중교통 이용](https://www.bigdata-map.kr/datastory/traffic/seoul)
  * 일 평균 440만 지하철 탑승인구 / 참고) 서울시 주민등록인구 950만
  * 가장 많이 이용하는 역 : 잠실, 강남, 고터 등 -> 상당수가 강남구, 서초구, 관악구, 마포구, 중구 등
* [카카오 모바일 APP 현황](https://ko.lab.appa.pe/2016-09/kakao-korea.html)
  * MAU : 729 만명
  * 일 평균 실행횟수 : 3.4회
* [길찾기만 하루 1억건](https://news.mt.co.kr/mtview.php?no=2021090916014079809)
  * 하루 1억 건 도보 길찾기 / 전국민이 하루에 2회씩 길찾기 기능을 조회하는 셈
* [네이버 지도 MAU](https://blog.naver.com/rkwkrhspm/222515422896)
  * MAU : 1392 만명



## 경쟁사와 비교하기 - 모바일 기준

| 항목                     | 런닝맵   | 카카오   | 네이버  | 서울교통공사 |
| ---------------------- | ----- | ----- | ---- | ------ |
| 리소스 크기(MB)             | 3.1   | 4.5   | 2.5  | 1.4    |
| Dom Content Loaded(ms) | 878   | 138   | 200  | 1270   |
| 페이지 로드(ms)             | 890   | 172   | 237  | 1400   |
| 성능 종합 점수               | 34    | 70    | 54   | 47     |
| FCP(초)                 | 14.8  | 1.7   | 2.2  | 6.7    |
| TTI(초)                 | 15.3  | 4.8   | 6.2  | 8.3    |
| Speed Index            | 14.8  | 6.6   | 6.4  | 9.4    |
| TBT(밀리초)               | 480   | 90    | 410  | 240    |
| LCP(초)                 | 15.3  | 5.5   | 7.6  | 7.3    |
| CLS                    | 0.042 | 0.005 | 0.03 | 0      |
| Security Score         | E     | F     | F    | F      |
| First Byte             | A     | A     | A    | F      |
| Keep-alive             | A     | B     | A    | A      |
| Compress Transfer      | F     | A     | A    | F      |
| Compress Images        | A     | A     | A    | A      |
| Cache static content   | D     | F     | F    | F      |
| CDN                    | X     | X     | X    | X      |



### [WebPageTest](https://www.webpagetest.org/) 결과&#x20;

<figure><img src="../../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption><p><a href="https://infra.subway-mhson.kro.kr/">https://infra.subway-mhson.kro.kr/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (9).png" alt=""><figcaption><p><a href="https://m.map.kakao.com/">https://m.map.kakao.com/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21) (4).png" alt=""><figcaption><p><a href="https://m.map.naver.com/subway/subwayLine.naver?region=1000">https://m.map.naver.com/subway/subwayLine.naver?region=1000</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption><p><a href="http://www.seoulmetro.co.kr/kr/cyberStation.do">http://www.seoulmetro.co.kr/kr/cyberStation.do</a></p></figcaption></figure>



### [PageSpeed](https://developers.google.com/speed/pagespeed/insights/) 결과&#x20;

<figure><img src="../../.gitbook/assets/image (1) (4).png" alt=""><figcaption><p><a href="https://infra.subway-mhson.kro.kr/">https://infra.subway-mhson.kro.kr/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption><p><a href="https://m.map.kakao.com/">https://m.map.kakao.com/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p><a href="https://m.map.naver.com/subway/subwayLine.naver?region=1000">https://m.map.naver.com/subway/subwayLine.naver?region=1000</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption><p><a href="http://www.seoulmetro.co.kr/kr/cyberStation.do">http://www.seoulmetro.co.kr/kr/cyberStation.do</a></p></figcaption></figure>





## 항목별 설명&#x20;

* First Byte Time : 웹 서버에서 받은 컨텐츠의 첫 번째 바이트가 얼마만에 도착했는가?
* Keep-Alive Enabled : TCP 연결을 재사용하기 위한 Keep-Alive 설정이 되어 있는가?
* Compress Transfer : 스크립트 파일이 Content-Encoding으로 압축되어 있는가?
* Compress Image : 이미지를 압축했는가?
* Cache Static Content : 정적 파일이 캐싱 설정이 되어 있는가?



## 개선 체크리스트&#x20;

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption><p><a href="https://infra.subway-mhson.kro.kr/">https://infra.subway-mhson.kro.kr/</a></p></figcaption></figure>



## 웹 성능 예산 설정하기&#x20;

1. pagespeed 테스트 결과 Lighthouse 종합 성능 점수를 50점 이상으로 한다. 현재는 34점이다.&#x20;
2. LTE 환경에서의 모바일 기기의 First Contentful Paint(FCP)는 3초 이하이어야 한다. 현재는 14.8초이다.&#x20;
3. LTE 환경에서의 모바일 기기의 Time to Interactive(TTI)는 7.3초 이하이어야 한다. 현재는 15.3초이다.&#x20;
4. 메인 페이지의 리소스 크기는 2.8MB 이하으로 제한한다. 현재는 3.1MB이다.&#x20;



## gzip 압축을 이용하여 이미지 및 텍스트 압축

### 적용 전&#x20;

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

### 적용 후&#x20;

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

### 성능 개선 결과

* Compress Transfer 지표가 F -> A 로 눈에 띄게 향상되었다.&#x20;
* 성능 종합지수가 34 -> 46으로 크게 향상되었다.&#x20;
* FCP, TTI, Speed Index, TBT, LCP, CLS 등 모든 지표가 눈에 띄게 향상되었다.  &#x20;

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>



### 참고&#x20;

* [https://www.lesstif.com/system-admin/nginx-gzip-59343019.html](https://www.lesstif.com/system-admin/nginx-gzip-59343019.html)
*



## 정적 파일들에 대해 cache 적용

### 적용 전&#x20;

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

### 적용 후 최초 로드 : Cache MISS&#x20;

<figure><img src="../../.gitbook/assets/image (9) (10).png" alt=""><figcaption></figcaption></figure>

### 적용 후 2번째 이후 로드 : HIT

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>



## TLS, HTTP 2.0 설정하여 웹 프로토콜 최적화

### 적용 전&#x20;

<figure><img src="../../.gitbook/assets/image (17) (2).png" alt=""><figcaption></figcaption></figure>

### 적용 후&#x20;

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>





## 정적 파일을 경량화하여 패킷의 크기 자체를 줄임

### 최초 상태&#x20;

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption><p>최초 상태 </p></figcaption></figure>

### gzip + cache + HTTP2.0 까지 적용한 뒤&#x20;

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption><p>gzip + cache + HTTP2.0 까지 적용한 뒤</p></figcaption></figure>

### 정적파일 경량화 이후 - javascript async 속성 추가

<figure><img src="../../.gitbook/assets/image (15) (6).png" alt=""><figcaption></figcaption></figure>

### 성능개선결과&#x20;

* DOM content 로딩 시간이 거의 10분의 1 수준으로 줄어들었다.&#x20;



## 성능 개선 종합 결과

| 내용\항목          | 성능 점수(점) | FCP(초) | TTI(초) | 리소스 크기(MB) |
| -------------- | -------- | ------ | ------ | ---------- |
| 목표치            | 50 이상    | 3 이하   | 7.3 이하 | 2.8 이하     |
| 개선 전           | 34       | 14.8   | 15.3   | 3.1        |
| gzip 압축 이후     | 46       | 5.4    | 6.0    | 3.1        |
| cache 적용 이후    | 43       | 5.4    | 6.1    | 3.1        |
| HTTP 2.0 적용 이후 | 43       | 5.5    | 6.2    | 2.8        |
| 정적파일 경량화 이후    | 47       | 2.5    | 5.9    | 2.8        |
| 예산 내 목표 달성 여부  | 미달성      | 달성     | 달성     | 달성         |





