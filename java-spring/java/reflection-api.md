---
description: 언제 사용하고, 어디서 사용되는가
---

# reflection api 정리

지금까지 reflection api 를 사용하여 바이트코드에 직접 접근하여 수정도 해보고, 외부 컨테이너를 만들어 의존성 주입을 시켜주기도 하였다. 이 모든 것이 reflection api 가 제공되었기에 쉽게 진행할 수 있었다. 그렇다면, 이러한 reflection 은 우리 주변 어디에서 사용되는가?&#x20;

## 1. 사용 예시&#x20;

### 1-1. 스프링&#x20;

* 실습도 했다시피, 의존성 주입시에 컨테이너 내부에서 사용된다.&#x20;
* MVC 뷰에서 넘어온 데이터를 객체에 바인딩 할 때 사용된다.&#x20;

### 1-2. Junit&#x20;

* 문서를 참고해보자.&#x20;
  * [https://junit.org/junit5/docs/5.0.3/api/org/junit/platform/commons/util/ReflectionUtils.html](https://junit.org/junit5/docs/5.0.3/api/org/junit/platform/commons/util/ReflectionUtils.html)

### 1-3. hibernate

* @Entity 클래스에 setter 가 없다면 리플렉션을 사용한다.&#x20;

## &#x20;2. 주의할 점&#x20;

* 지나친 사용은 성능을 저하시킬 수 있다. 반드시 필요한 경우에만 사용한다.&#x20;
* 컴파일 타임에 확인되지 않은 이슈를 런타임 때에 만들어낼 수 있다.&#x20;
* 접근제어자를 무시하고 접근할 수 있다.&#x20;

## 3. 참고&#x20;

* [https://docs.oracle.com/javase/tutorial/reflect/index.html](https://docs.oracle.com/javase/tutorial/reflect/index.html)
