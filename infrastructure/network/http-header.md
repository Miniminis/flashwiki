# HTTP header

## 1. 개념&#x20;

* HTTP 통신을 하게되면 클라리언트와 서버는 각각 헤더와 바디로 이루어진 데이터를 주고받게 된다.&#x20;
* 일반적으로는 3가지 헤더가 자동으로 생성된다.&#x20;
  * 일반헤더&#x20;
  * 클라이언트에서 보내는 헤더인 request header&#x20;
  * 서버에서 응답하는 헤더인 response header&#x20;
* 콜론으로 구분되며, key - value 형태를 띈다. &#x20;
* HTTP header 는 굉장히 유연한 구조로 이루어져있어서 쉽게 커스텀한 데이터를 추가하기가 쉽다.&#x20;

## 2. 실습해보기&#x20;

* 네이버의 예시를 봐보자. 네이버 메인화면 > 네트워크 > Fetch/XHR 탭으로 이동하면 count.nhn api 를 볼 수 있다.&#x20;
* general 이 일반헤더, response headers 가 서버에서 설정한 헤더, request headers 가 바로 클라이언트에서 설정한 헤더이다.&#x20;
* response 탭에는 주로 컨텐츠의 본문이 담기고, json, html, image 등이 담긴다.&#x20;
* 일반헤더&#x20;
  * 요청한 url&#x20;
  * 요청 매소드&#x20;
  * 응답상태&#x20;
  * 자원의 출처를 노출할지 말지 결정하는 referer policy 등을 포함한다.&#x20;
* 응답헤더&#x20;
  * 서버가 클라이언트에게 응답할 때 자동으로 설정하거나 커스텀하게 세팅하는 헤더&#x20;
  * 주로 서버의 소프트웨어 정보 등이 담기는데, 대부분의 경우, 보안상 노출되지 않도록 처리한다.&#x20;
    * naver 의 경우, NWS(naver web server) 라는 것 이외에, gzip 압축 알고리즘을 쓰는 것 이외에 다른 정보는 노출되어있지 않다.&#x20;
* 요청헤더&#x20;
  * 요청헤더에는 다음과 같은 정보들이 담긴다.&#x20;
  * 요청 매소드, 클라이언트 OS, 브라우저 정보 등&#x20;

<figure><img src="../../.gitbook/assets/image (1) (9).png" alt=""><figcaption></figcaption></figure>



비록 유연하게 커스텀할 수 있지만, 보통은 일반적으로 통용되는 키에 데이터를 넣어서 통신하는 것이 일반적이다.&#x20;

* 예를 들어 Cookie 값의 경우
  * request에서 Cookie : value -> response에서는 Set-Cookie : value&#x20;

