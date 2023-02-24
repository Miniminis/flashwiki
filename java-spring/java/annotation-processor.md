# Annotation Processor 로 없는 소스코드 생성하기

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

> Lombok 이 어노테이션 프로세서를 이용하여 소스코드가 컴파일 되기 전에 소스코드를 변경하여 getter 와 setter등을 생성해주는 것처럼, 우리도 소스코드는 건드리지 않고, 소스코드가 컴파일 되는 시점에 새로운 코드를 추가시켜 컴파일 된 코드는 소스코드와 전혀 다른 코드가 되도록 실습을 해보자...!&#x20;

## 1. 다시 돌아온 마법모자&#x20;

> 우리는 다음과 같은 것들을 해보려고 한다.&#x20;

* 어노테이션 프로세서를 만든다. (annotation-processor-moja)
  * 이곳에서는 어노테이션을 새롭게 정의한다.&#x20;
  * 인터페이스에만 어노테이션을 추가할 수 있도록 규제한다. -> 클래스나 enum 에는 추가할 수 없도록
  * 어노테이션을 붙인 인터페이스가 있고 구현체가 없는 상황에서 pullOut() 매소드를 호출할 경우, 자동적으로 Rabbit 문자열이 반환되도록 조작된 결과를 반환한다.&#x20;
* 어노테이션을 사용할 프로젝트를 만든다. (annotation-moja)
  * 어노테이션 프로세서를 의존성 추가한다.&#x20;
  * Moja interface 를 정의한 뒤, @Moja 어노테이션을 추가한다.&#x20;
  * 어플리케이션에서 pullOut 하여 생성한적 없는 결과값인 Rabbit 이 반환됨을 확인한다.&#x20;
  * 놀란다...!&#x20;







## 참고&#x20;

* gradle clean build 오류 해결&#x20;
  * [https://docs.gradle.org/current/userguide/building\_java\_projects.html](https://docs.gradle.org/current/userguide/building\_java\_projects.html)
*
