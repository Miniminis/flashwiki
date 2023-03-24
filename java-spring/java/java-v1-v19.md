# Java 버전별 특징 (v1-v19)

> 중요한 버전만 기록해두고, 버전별 세부 내용은 아래의 링크를 참고하자. &#x20;

## 참고&#x20;

* [https://hajoung56.tistory.com/35](https://hajoung56.tistory.com/35)
* [https://en.wikipedia.org/wiki/Java\_version\_history](https://en.wikipedia.org/wiki/Java\_version\_history)

## java 5

* generics&#x20;
* annotation&#x20;
* concurrency api&#x20;
* enumeration&#x20;
* auto boxing and unboxing&#x20;

## java 8

* lambda expression&#x20;
* method reference&#x20;
* interface default methods&#x20;
* optional api&#x20;
* date time api&#x20;
* stream api&#x20;

## java 9

* jigsaw : 모듈을 만들고 모듈에 명시적으로 외부에서 호출할 수 있는 api 를 선언한다.&#x20;
  * 선언한 api 이외에는 외부에서 접근할 수 없다.&#x20;
  * 기존의 gradle, maven 패키징에서는 서로 다른 패키지 간의 캡슐화가 지원되지 않았음&#x20;
  * java platform 을 모듈화하는 것이 편리해짐&#x20;
* new http client&#x20;
* try-with-resources&#x20;
* diamond operator&#x20;
* interface private method&#x20;
* optional to stream&#x20;

## java 11

* new string method&#x20;
  * strip()&#x20;
  * stripLeading()
  * stripTrailing()
  * isBlank()
  * lines()
  * repeat(n) : 지정된 수 만큼 문자열을 반복+붙여서 반환&#x20;
* lambda var 지원&#x20;

## java 14&#x20;

* switch 문 개선&#x20;
* record&#x20;
* 좀 더 자세한 null pointer exception&#x20;

## java 15&#x20;

* multiline strings, text-blocks : 문자열을 개행문자 없이 여러줄에 걸쳐서 쓸 수 있게 됨&#x20;

## java 16&#x20;

* openJDK 의 소스코드 버전 관리 시스템이 Mercurial -> Git 으로 변경됨. 이제 Git 에서 JDK 소스코드를 볼 수 있음!&#x20;

## java 17 &#x20;

* RandomGenerator : 의사난수 생성기를 통해서 어려운 난수를 생성할 수 있는 API 추가됨&#x20;
