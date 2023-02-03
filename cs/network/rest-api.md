# REST API

## 1. 개념&#x20;

* API : Application Programming Interface&#x20;
* 소프트웨어와 소프트웨어 사이에서 데이터 전송을 가능하게 하는 프로그램이다.&#x20;

## 2. REST API

* API 중에서도 RESTful 한 것
* 웹의 장점을 잘 살린 아키텍처이며&#x20;
* 이 아키텍쳐는 Uniform-Interface 를 갖는 것이 특징이다.&#x20;
* REST 등장 배경 : How do i improve HTTP without breaking the web
  * 웹 페이지를 변경했다고 해서 브라우저를 업데이트할 필요가 없다.&#x20;
  * 웹 브라우저를 업데이트 했다고 웹 페이지를 변경할 필요가 없다.&#x20;
  * HTTP 명세가 변경되어도, 웹은 잘 동작한다.&#x20;
  * HTML 명세가 변경되어도 웹은 잘 동작한다. &#x20;

### 2-1. Uniform-Interface&#x20;

* 자원들이 각각의 독립적인(=일관된) 인터페이스를 가져야 한다.&#x20;
  * 서버와 클라이언트가 독립적으로 진화한다.&#x20;
  * 서버의 기능이 변경되어도 클라이언트를 업데이트할 필요가 없다.&#x20;
* identification of resources&#x20;
  * 리소스가 URI 로 식별되면 된다.&#x20;
* manipulation resources throgh representations&#x20;
  * HTTP 메소드의 GET, POST 등의 representation 들을 통해서 리소스가 조작되어야 한다.&#x20;

### 2-2. 규칙&#x20;

1. Self-descriptive messages&#x20;
   1. 메시지만 보고도 무슨뜻인지 모두가 알 수 있어야 한다. 메시지가 스스로 설명해야한다. &#x20;
   2. HTTP header 에 타입을 명시하고 각 메시지(자원)들은 MIME types 에 맞춰 표현되어야 한다.&#x20;
   3. 예를 들어서 .json 을 변환한다면, application/json 으로 명시해주어야 한다. MIME types 는 문서, 파일 등의 특성과 형식을 나타내는 표준이다. IETF 의 RFC6838에 정의 및 표준화가 되어있다.&#x20;
      1. font/ttf, text/plain, text/csv 등이 존재 &#x20;
2. HATEOAS 구조&#x20;
   1. 하이퍼링크에 따라서 다른 페이지를 보여줘야 하고&#x20;
   2. 데이터마다 어떤 URL 에서 원했는지 명시해주어야 한다.&#x20;
3. Stateless&#x20;
   1. HTTP 자체가 원래 Stateless 하기 때문에 HTTP만 써도 충족이 된다.&#x20;
   2. REST API 를 제공해주는 서버는 세션을 해당 서버쪽에 유지하지 않는다.&#x20;
      1. 쿠키와 세션을 통해서 State 하게 바꾸지 않는다.&#x20;
4. Cacheable&#x20;
   1. HTTP 는 원래 캐싱이 된다. 새로고침을 하면 304 가 뜨면서 원래있던 js 와 css 를 불러오는 것을 볼 수 있다.&#x20;
      1. 최초 통신 300 이후 -> 304
   2. 네트워크 요청을 줄여주어 속도가 빨라지므로 UX 향상에 기여한다.&#x20;
   3. 네트워크 요청시 해당되는 자원들을 복사해서 메모리에 저장해두었다가, 같은 요청시에 네트워크 요청을 하지 않고 브라우저 메모리에 있던 자원을 다시 반환한다.&#x20;
   4. HTTP method 중 GET 에 한정되고, Cache-Control:max-age=100 (100초) 처럼 한정된 시간을 정할 수 있다.&#x20;
   5. 캐싱 데이터가 유효한지 점검하기 위해 Last-modified 와 Etag 를 사용한다.&#x20;
   6. Etag : 전달되는 값에 태그를 붙여서 캐싱되는 자원인지를 확인해주는 것 &#x20;
5. Client-Server 구조&#x20;
   1. client 와 server 가 서로 독립적인 구조를 가져야 한다.&#x20;
   2. 이 역시 HTTP 를 통해 가능한 구조이다.&#x20;
   3. 서버에서 HTTP 표준만 지킨다면, 웹에서는 그에 따른 화면이 잘 나타나게 된다.
   4. 서버는 그냥 API 를 제공하고, 그에 맞는 비즈니스 로직을 처리하면 된다.&#x20;
   5. 클라이언트는 HTTP 로 받는 로직만 잘 처리하면 된다. &#x20;
6. Layered System&#x20;
   1. 계층구조로 아키텍처를 만들 수 있어야 한다.&#x20;
      1. 서버에서 proxy, gateway, loadbalancing, encryption 의 계층을 추가해도 클라이언트는 몰라야 한다.&#x20;

### 2-3. URI 규칙&#x20;

1. 동작은 HTTP 메소드로 해야한다.&#x20;
   1. 수정이면 PUT, 추가이면 POST 등 규칙을 따라야 한다.&#x20;
   2. URI 에 동작을 담지 않는다. 예를 들면 books/delete/1 는 잘못된 URI 이다.&#x20;
2. 확장자는 표기하지 않는다.&#x20;
3. 동사가 아닌 명사로만 표기한다.&#x20;
   1. user/1/inclusion/book/2
4. URI 는 계층적인 내용을 담고 있다.&#x20;
5. 소문자로 쓰며 너무 길 경우에는 **-** 를 쓴다.&#x20;
6. HTTP 응답 상태코드를 활용한다.&#x20;

## 3. 도서관 시스템 URI 만들기&#x20;

* 모든 책을 조회 : GET /books&#x20;
* 책을 생성 : POST /books
* 책을 수정 : PATCH or PUT /books/bookid
* 특정 책을 조회 : GET /books/bookid
* 어떤 유저가 특정 책을 빌린다. : POST /users/userid/books/bookid
* 어떤 유저가 특정 책을 수정한다. : PATCH /users/userid/books/bookid

## 4. 더 알아보기&#x20;

{% embed url="https://youtube.com/watch?v=RP_f5dMoHFc&feature=shares" %}
