# Octoparse

## 참고

* [\[Youtube\] 일잘러 장피엠](https://www.youtube.com/@jangpm)&#x20;

## 목록 크롤링 (feat. 쿠팡 상품리스트)  &#x20;

1. octoparse 앱 다운로드 후 크롤링할 페이지의 URL 붙여넣기&#x20;
2. Pagination 설정&#x20;
   1. 다음 페이지로 넘어가는 버튼 찾아서 loop click single element 클릭 &#x20;
   2. 다음 페이지로 넘어가는 조건을 확인하는 것이 중요!&#x20;
3. 추출할 데이터 선택&#x20;
4. 데이터 다듬기&#x20;
5. 약간의 대기시간 설정&#x20;
6. export&#x20;
   1. local excel file&#x20;
   2. google spread sheet&#x20;



## Pagination 다양한 케이스들&#x20;

### 1. next 버튼이 바로 존재하는 경우&#x20;

* 클릭하여 페이지네이션 선택&#x20;

### 2. 무한 스크롤 형태 (ex. facebook, wanted 등)&#x20;

1. URL 붙여넣기&#x20;
2. 빈 곳 클릭 > loop pagination 클릭&#x20;
3. Pagination > Sctoll down the page after if is loaded 설정&#x20;
   1. 5번 정도 scroll down 하도록 설정
      1. 이 동작의 의미 : 페이지를 크롤링 한 뒤, 5번 스크롤 다운하고 다시 데이터를 가져오겠다.&#x20;
4. Pagination > Exit Loop&#x20;
   1. 설정 안하면 새로운 정보 없을 때까지 계속 크롤링&#x20;
   2. 정확하게 횟수 명시하면 3번 액션에 대한 횟수가 지정됨&#x20;

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

### 3. 바로 다음페이지로 가는 버튼이 없거나 페이지 구조가 복잡한 경우&#x20;

> 이런 경우는 URL을 통해서 크롤링해야하는 페이지를 알 수 있다.&#x20;

* URL 상단에서 page 에 대한 힌트를 얻고 그에 대한 규칙만 자동으로 동작하고록 규칙을 설정하면 된다.&#x20;

<figure><img src="../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (157).png" alt=""><figcaption><p>바 ㅇㅇㄴ</p></figcaption></figure>

* octoparse 유료 기능을 사용하게 되면 방금 전에 수행한 task 의 결과물로부터 값을 받아와 새로운 크롤링 동작을 하도록 설정할수도 있다.&#x20;



## 상세페이지 크롤링하기&#x20;

### 방법 1. 목록에서 하나씩 상세페이지 접근하며 크롤링하기&#x20;

1. Pagination, Loop item 등 목록에 대한 크롤링 설정하기&#x20;

<figure><img src="../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

2. 상세페이지 URL 내부 접근 후 다시 목록으로 돌아가게 설정&#x20;
   1. click item -> open in an new tab 체크&#x20;
   2. 세부페이지에서 다시 목록으로 돌아가도록 다음 액션 추가&#x20;

<figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

알아두기 &#x20;

* 항상 위와 같이 목록으로 돌아가는 설정을 해줘야하는 것은 아니다. 웹 사이트의 구조에 따라서 다르니 직접 테스트해보면서 방법을 찾아볼 것!&#x20;

### 방법 2. 상세페이지 URL 을 한번에 리스트로 나열하고 한번에 크롤링하기&#x20;

* 미리 URL 목록이 담긴 엑셀 시트를 준비하거나 &#x20;
* 데이터 설정으로 URL 규칙을 미리 설정하여 진행&#x20;



## Page Level Data&#x20;

<figure><img src="../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>



## 이미지 URL 크롤링하기&#x20;

1. A 태그에서 URL 추출&#x20;
2. 필드 설정 > Custom field > set attribute(image url)&#x20;
3. 2번이 안되면 inner html or outer html 클릭하여 데이터 정리하는 방법을 사용
   1. 이 과정에서 정규식을 이용할 수 있음&#x20;

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>



## 크롤링한 이미지 URL 대용량 파일로 다운받는 방법&#x20;

* google 확장 프로그램 : Tap Save&#x20;



## 게시글 작성일, 작성날짜, 작성시간 일정한 형태로 크롤링하기&#x20;

크롤링시 작성일이나 날짜가 일정한 포멧이 아닌 경우, customize xpath 라는 기능을 통해 데이터를 일정한 형태로 크롤링할 수 있다.&#x20;

<figure><img src="../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

* 개발자 도구 진입 후 내가 추출하고자 하는 데이터의 포멧이 담긴 위치를 찾는다.&#x20;
* 우클릭 > copy full xpath&#x20;
* customize xpath 메뉴 > relative xpath



## 사이트 모니터링으로 신규 데이터 자동 크롤링하기 &#x20;

<figure><img src="../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

* 기존에 크롤링 된 데이터 이후에 신규로 업데이트 된 데이터만 크롤링하기 위해서 remove duplicate 설정&#x20;
  * article url  중복제거 기준으로 설정&#x20;

<figure><img src="../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

* 클라우드 run & schedule 설정 모두 유료기능 ㅠㅠ&#x20;



### Zapier 를 이용하여 구글 스프레드 시트에 신규 데이터 자동 업데이트 & notification&#x20;

1. task 이름 설정&#x20;
2. trigger : octoparse 검색 후 클릭&#x20;
3. app & event : new data processed in octoparse&#x20;
4. account : account login&#x20;
5. set up trigger : task 선택&#x20;
   1. status : completed 상태만 수집&#x20;
6. Google Spread Sheet Action&#x20;
   1. event : new row&#x20;
   2. account login&#x20;
   3. choose file&#x20;
   4. column 별로 데이터 매핑&#x20;
   5. 테스트&#x20;
7. 만약 크롤링 된 데이터 형식이 마음에 안든다면 zapier 내부의 formatter 이용하기 &#x20;
8. Email Notification Action&#x20;
   1. action : send email&#x20;

<figure><img src="../../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

### 하지만 만약 신규 글이 1분 내에 100개가 한꺼번에 쌓인다면?&#x20;

* Zapier 내부에서 Increment Value 를 설정해주어 한 동작에 한번씩만 notification 을 보내질 수 있도록 한다.&#x20;

<figure><img src="../../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>
