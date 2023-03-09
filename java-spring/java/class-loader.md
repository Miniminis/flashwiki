# Class Loader

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/image (72) (1).png" alt=""><figcaption><p><a href="https://user-images.githubusercontent.com/26548454/95957125-4432bd00-0e3a-11eb-8c45-7f81617e7f79.png">https://user-images.githubusercontent.com/26548454/95957125-4432bd00-0e3a-11eb-8c45-7f81617e7f79.png</a></p></figcaption></figure>



class loader 는 로딩, 링킹, 초기화 순서로 진행된다.&#x20;

## 1. loading&#x20;

* .class 로 컴파일된 자바 바이트 코드를 읽어들여 바이너리 코드로 만든 뒤, 메모리 내의 "메소드" 영역에 이를 저장한다.&#x20;
* 이때, 메소드 영역에는 다음과 같은 것들이 저장된다.&#x20;
  * FQCN, fully qualified class name : 패키지 명을 포함한 클래스의 풀 네임을 말한다.&#x20;
  * 클래스, 부모클래스, 인터페이스, 이늄&#x20;
  * 메소드, 변수&#x20;
* 로딩이 끝나면 해당 클래스 타입의 Class 객체를 생성하여 메모리 내의 힙 영역에 저장한다.&#x20;
  *   ```java
      package me.flash;

      public class Main {

          public static void main(String[] args) {
              System.out.println(Flash.class);    //별도로 객체를 생성하지 않아도 class 객체가 생성됨 
              System.out.println(Flash.class.getSuperclass());
              System.out.println(Flash.class.getSuperclass().getSuperclass());
              /*
              class me.flash.Flash
              class java.lang.Object
              null
              * */
          }
      }
      ```

      &#x20;



## 2. linking&#x20;

* 링크는 3단계로 나뉘어 진행이 된다.&#x20;
  * veryfy : .class 파일 형식이 유효한지 검사한다.&#x20;
  * prepare : 클래스 변수 (static 변수) 와 기본값에 필요한 메모리를 준비한다.&#x20;
  * resolve : 링킹 단계에서 수행되기도 하고, 그 이후에 수행되기도 하므로 optional 하다. 심볼릭 메모리 레퍼런스를 메소드 영역에 있는 실제 레퍼런스로 교체하는 일을 수행한다. &#x20;



## 3. initialization&#x20;

* static 블럭을 실행하고 및 변수에 값을 할당한다.

&#x20;

## 4. class loader&#x20;

* 클래스 로더의 경우는 계층 구조로 이루어져 있고, 기본적으로는 세 가지 클래스 로더가 제공된다.&#x20;

### 4-0. 특징&#x20;

* 계층적
  * 클래스 로더는 계층적인 구조를 가질 수 있다. 상위 클래스 로더 아래에 하위 클래스 로더를 가질 수 있다.&#x20;
  * 이때, 최상위 계층의 클래스 로더가 바로 bootstrap class loader 이다.&#x20;
* 위임형 로드 요청
  * &#x20;제공되는 세 가지의 클래스 로더는 bootstrap > execution(platform) > application 순으로 우선순위를 가진다.
  * 클래스로더가 CL1 - CL2 - CL3 의 구조를 가지고 있을 때,&#x20;
    * CL3 가 특정 클래스를 로드할 때, 해당 클래스 로딩을 CL2 에 요청하고&#x20;
    * CL2는 다시 CL1 에 로딩을 요청한다.
* 한번 로드된 클래스는 다시 unload 할 수 없다.&#x20;
  * 클래스 로더 자체를 삭제하고 새롭게 생성하여 클래스 로더를 로딩하면 재로딩 된 것처럼 보이게 할 수 있다. &#x20;
* 가시적인 규약&#x20;
  * 하위 클래스 로더는 상위 클래스 로더를 위임형 로드 요청을 통해서 클래스 로딩 요청을 할 수 있지만, 그 반대는 불가하다. (상속이랑 비슷한듯)&#x20;
  * 같은 부모 클래스 로더를 가진 하위 클래스 로더들 사이에서도 상호간 클래스 로드 요청이 불가하다.&#x20;

### 4-1. bootstrap class loader&#x20;

* 가장 최상위, 최우선 클래스 로더이다.&#x20;
* JVM 이 실행될 때, 가장 먼저 로드되는 클래스 로더이며&#x20;
* 다른 클래스 로더와 다르게 네이티브 코드로 되어있다는 특징이 있다.&#x20;
* JAVA\_HOME\lib 에 있는 코어 자바 API 를 제공한다. -> 자바 기본 클래스들을 제공한다.&#x20;

### 4-2. platform class loader&#x20;

* JAVA\_HOME\lib\ext 폴더 OR java.ext.dirs 시스템 변수에 해당하는 위치에 있는 클래스들을 읽는다.&#x20;
* java 확장 클래스들이 로드된다.&#x20;
* 별도로 클래스 패스로 설정하지 않아도 읽을 수 있으며&#x20;
* 보안 확장 기능 같은 것들이 여기서 로드된다.&#x20;

### 4-3. application class loader&#x20;

* CLASSPATH 에 정의되거나 JVM 옵션에서 -cp, -classpath 에 지정된 클래스들이 로딩된다.&#x20;
* 사용자가 지정한 $CLASSPATH 내의 클래스를 로드하며 이는 곧 어플리케이션의 클래스들을 로드하는 것임을 뜻한다.&#x20;
* User-Defined Class Loader&#x20;
  * 어플리케이션 클래스가 로드되고 나면, 이제 사용자가 코드 상에서 직접 생성해서 사용하는 클래스가 로드된다.&#x20;



## 5. 참고&#x20;

* [https://blueyikim.tistory.com/37](https://blueyikim.tistory.com/37)
* [https://resian-programming.tistory.com/63](https://resian-programming.tistory.com/63)
