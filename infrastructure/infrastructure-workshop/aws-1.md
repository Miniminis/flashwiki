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

<table><thead><tr><th width="180">항목</th><th>런닝맵</th><th>카카오</th><th>네이버</th><th>서울교통공사</th></tr></thead><tbody><tr><td>리소스 크기(MB)</td><td>3.1</td><td>4.5</td><td>2.5</td><td>1.4</td></tr><tr><td>Dom Content Loaded(ms)</td><td>878</td><td>138</td><td>200</td><td>1270</td></tr><tr><td>페이지 로드(ms)</td><td>890</td><td>172</td><td>237</td><td>1400</td></tr><tr><td>성능 종합 점수</td><td>34</td><td>70</td><td>54</td><td>47</td></tr><tr><td>FCP(초)</td><td>14.8</td><td>1.7</td><td>2.2</td><td>6.7</td></tr><tr><td>TTI(초)</td><td>15.3</td><td>4.8</td><td>6.2</td><td>8.3</td></tr><tr><td>Speed Index</td><td>14.8</td><td>6.6</td><td>6.4</td><td>9.4</td></tr><tr><td>TBT(밀리초)</td><td>480</td><td>90</td><td>410</td><td>240</td></tr><tr><td>LCP(초)</td><td>15.3</td><td>5.5</td><td>7.6</td><td>7.3</td></tr><tr><td>CLS</td><td>0.042</td><td>0.005</td><td>0.03</td><td>0</td></tr><tr><td>Security Score</td><td>E</td><td>F</td><td>F</td><td>F</td></tr><tr><td>First Byte</td><td>A</td><td>A</td><td>A</td><td>F</td></tr><tr><td>Keep-alive</td><td>A</td><td>B</td><td>A</td><td>A</td></tr><tr><td>Compress Transfer</td><td>F</td><td>A</td><td>A</td><td>F</td></tr><tr><td>Compress Images</td><td>A</td><td>A</td><td>A</td><td>A</td></tr><tr><td>Cache static content</td><td>D</td><td>F</td><td>F</td><td>F</td></tr><tr><td>CDN</td><td>X</td><td>X</td><td>X</td><td>X</td></tr></tbody></table>



### [WebPageTest](https://www.webpagetest.org/) 결과&#x20;

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption><p><a href="https://infra.subway-mhson.kro.kr/">https://infra.subway-mhson.kro.kr/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (9).png" alt=""><figcaption><p><a href="https://m.map.kakao.com/">https://m.map.kakao.com/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption><p><a href="https://m.map.naver.com/subway/subwayLine.naver?region=1000">https://m.map.naver.com/subway/subwayLine.naver?region=1000</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (46) (3).png" alt=""><figcaption><p><a href="http://www.seoulmetro.co.kr/kr/cyberStation.do">http://www.seoulmetro.co.kr/kr/cyberStation.do</a></p></figcaption></figure>



### [PageSpeed](https://developers.google.com/speed/pagespeed/insights/) 결과&#x20;

<figure><img src="../../.gitbook/assets/image (1) (4).png" alt=""><figcaption><p><a href="https://infra.subway-mhson.kro.kr/">https://infra.subway-mhson.kro.kr/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption><p><a href="https://m.map.kakao.com/">https://m.map.kakao.com/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (10).png" alt=""><figcaption><p><a href="https://m.map.naver.com/subway/subwayLine.naver?region=1000">https://m.map.naver.com/subway/subwayLine.naver?region=1000</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption><p><a href="http://www.seoulmetro.co.kr/kr/cyberStation.do">http://www.seoulmetro.co.kr/kr/cyberStation.do</a></p></figcaption></figure>





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

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

### 적용 후&#x20;

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

### 성능 개선 결과

* Compress Transfer 지표가 F -> A 로 눈에 띄게 향상되었다.&#x20;
* 성능 종합지수가 34 -> 46으로 크게 향상되었다.&#x20;
* FCP, TTI, Speed Index, TBT, LCP, CLS 등 모든 지표가 눈에 띄게 향상되었다.  &#x20;

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

### 적용 후&#x20;

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>





## 정적 파일을 경량화하여 패킷의 크기 자체를 줄임

### 최초 상태&#x20;

<figure><img src="../../.gitbook/assets/image (43) (2).png" alt=""><figcaption><p>최초 상태 </p></figcaption></figure>

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





