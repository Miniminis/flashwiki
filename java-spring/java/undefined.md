# 바이트 코드 조작하기

## 1. 왜 필요한가

* 자바 바이트 코드를 조작하는 기술은 생각보다 우리 주변에서 아주 널리 사용되고 있다. 이를테면 이런 곳에서.&#x20;
  * 프로그램 분석&#x20;
    * 코드에서 버그를 찾는 툴&#x20;
    * 코드 복잡도를 계산하는 툴 등&#x20;
  * 클래스 파일 생성&#x20;
    * 프록시&#x20;
    * 특정 API 의 호출을 제한
    * 스칼라와 같은 언어의 컴파일러&#x20;
  * 자바 소스코드를 건드리지 않고 할 수 있는 이것 저것들 (transparent 하게 무엇인가를 할 수 있는 곳)&#x20;
    * 프로파일러 (spring 에서 나온 newrelic)&#x20;
    * 최적화&#x20;
    * 로깅 등&#x20;
  * 스프링이 컴포넌트 스캔하는 방법&#x20;
    * 내부적으로 asm(자바 바이트 코드 조작 툴)을 이용한다.&#x20;
    * 컴포넌트 스캔으로 빈 후보에 등록할 클래스를 찾는데 이용한다.&#x20;
* 이런 툴들이 어떤 원리로 동작하는지 알아보는 것은 우리가 사용하는 기술을 좀 더 깊게 이해할 수 있다는 점에서 분명 도움이 될 것이다.&#x20;



## 2. 바이트 코드 조작 라이브러리 비교&#x20;

* ASM: [https://asm.ow2.io/](https://asm.ow2.io/)
  * 매우 어렵다고 한다. 러닝 커브가 높고, 자바의 바이트코드 구조를 어느정도 알고 있고 디자인 패턴에 익숙해도 이해하고 사용하기거 어렵다고 한다.&#x20;
  * 스프링 프레임워크를 포함하여 많은 곳에서 쓰이고 있음&#x20;
* Javassist: [https://www.javassist.org/](https://www.javassist.org/)
  * ASM 보다는 아니지면 여전히 사용하기가 어렵다고 한다.&#x20;
* ByteBuddy: [https://bytebuddy.net/#/](https://bytebuddy.net/#/)
  * 비교적 가장 최근에 나온 라이브러리이다.&#x20;
  * 자바 바이트 코드에 대해서 깊게 몰라도 충분히 사용 가능하며, 러닝커브가 낮다.&#x20;
  * 배우기가 쉽다.&#x20;
  * 코드를 작성하기는 편하지만, 체이닝이 심해서 가독성은 좀 떨어지는 편&#x20;

> 따라서 나는 ByteBuddy 를 사용해볼 예정이다.&#x20;



## 3. ByteBuddy 사용하여 모자에서 없는 토끼 꺼내기&#x20;

### 3-1. ByteBuddy gradle 의존성 추가&#x20;

* [https://search.maven.org/artifact/net.bytebuddy/byte-buddy](https://search.maven.org/artifact/net.bytebuddy/byte-buddy)
* ```gradle
  dependencies {

      //ByteBuddy - for bytecode manipulation
      implementation 'net.bytebuddy:byte-buddy:1.14.0';
      
      ...
  }
  ```



### 3-2. 파일 내부에서 호출 전에 변경하기 &#x20;







## 참고&#x20;

* 바이트 코드 조작 라이브러리 모음&#x20;
  * ASM: [https://asm.ow2.io/](https://asm.ow2.io/)
  * Javassist: [https://www.javassist.org/](https://www.javassist.org/)
  * ByteBuddy: [https://bytebuddy.net/#/](https://bytebuddy.net/#/)&#x20;
