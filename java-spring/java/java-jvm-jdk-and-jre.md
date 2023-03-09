---
description: 이들은 도대체 무엇이 다른가...!
---

# java, jvm, jdk and jre

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}



java 언어로 개발을 하면서 늘 헷갈리기도 하고, 정확하게 알고 있다고 생각하지 않았던 것이 바로, jvm, jdk, 그리고 jre 에 대한 설명일 것이다. 오늘은 이들이 무엇이고 어떻게 다른지 제대로 정리해보려고 한다.&#x20;

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption><p><a href="https://static.tildacdn.com/tild6563-3739-4565-a361-323339393332/jvm1.png">https://static.tildacdn.com/tild6563-3739-4565-a361-323339393332/jvm1.png</a></p></figcaption></figure>

## 1. JVM, Java Virtual Machine&#x20;

* 자바 가상 머신
* 자바 바이트 코드를 OS에 특화된 코드로 변환하여 실행한다. &#x20;
  * 각 OS 에 특화된 코드로 변환하는 과정에서 interpreter 와 JIT compier(Just In Time Complier) 가 사용된다.&#x20;
  * interpreter 는 자바 바이트 코드를 한줄씩 실행시킨다.&#x20;
  * JIT 컴파일러는 인터프리터의 효율을 높이기 위해서, 인터프리터가 실행하는 코드가 반복되면, 반복되는 코드 전부를 native 코드로 바꾸어버린다. 그러면 인터프리터는 반복된 코드들에 대해서는 이미 바뀌어진 네이티브 코드만 실행하면 되는 것이다.&#x20;
  * 참고로, 여기서 말하는 네이티브 코드는 java 로 쓰여진 코드가 아니라, C나 C++로 쓰여진 코드를 말한다. 대표적으로 Thread 를 호출할 때 쓰이는 코드가 이에 해당한다.&#x20;
    * ```java
      public static void main(String[] args) {
          Thread.currentThread();
      }

      public class Thread implements Runnable {
          @HotSpotIntrinsicCandidate
          public static native Thread currentThread();    //native keyword! 
      }
      ```
* 자바 가상 머신은 바이트 코드를 실행하는 표준이자, 구현체인 셈이다.&#x20;
* 구현체는 벤더사마다 다양하다. 대표적으로는 oracle, amazon, azul 등이 있다.&#x20;
* JVM 은 컴파일된 자바 바이트코드(.class) 를 OS에 맞는 코드로 변환하여 실행하는 역할을 수행하므로, 사실상 자바 언어에 종속적이지는 않다. 즉, 쓰여진 언어가 자바가 아니어도, 자바 바이트코드 형태로 주어진다면, 얼마든지 JVM 환경에서 실행가능하다는 이야기다.&#x20;
  * 이런 맥락에서 JVM 기반 언어로 Closure, Kotlin, Scala, Groovy, Jruby, Jython 등의 언어가 꼽힌다.&#x20;
* JVM 은 결국 java byte code 를 OS 환경에 맞게 변환하여 실행하기 때문에 OS 플랫폼에 종속적이게 된다.&#x20;

<figure><img src="../../.gitbook/assets/image (2) (2) (3).png" alt=""><figcaption><p>how JIT compier work </p></figcaption></figure>



## 2. JRE, Java Runtime Environment&#x20;

* JRE 는 자바 프로그래밍을 실행할 수 있도록 구성된 배포판을 말한다.&#x20;
* JVM + Java 핵심 Library 로 구성되어있고 기타 자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스 파일을 가지고 있다. (위의 그림 참고)
* 위의 그림에서도 볼 수 있듯, 자바 개발에 필요한 도구들은 포함하지 않는다. (JDK 에서 제공된다.)&#x20;
* 과거에는 항상 JDK 와 JRE 가 각각 출시되었지만, 오라클 벤더사에서는 Java 11 버전부터 JRE를 별도로 제공하고 있지 않다.&#x20;



## 3. JDK, Java Development Kit&#x20;

* JRE(JVM + library) + Java Development Tool 을 포함하는 개념이다.&#x20;
* 소스 코드를 작성할 때 사용하는 자바라는 언어 자체는 플랫폼에 독립적이다.&#x20;
  * 즉, 윈도우에서 쓰인 자바 프로그램이 맥에서도 똑같은 코드로 변경 없이 실행될 수 있다는 뜻이다. &#x20;
  * 그러나 JVM 은 각 운영체제에 종속적이기 때문에 윈도우에서는 윈도우의 JVM, 맥에서는 맥의 JVM 을 통해 자바 바이트 코드로 변환되어 실행되는 것이다.&#x20;
  * write once run anywhere
* 오라클 사에서는 Java 11 버전부터 JDK 만 제공하고, JRE 를 별도로 제공하고 있지 않다.&#x20;



## 4. JAVA language&#x20;

* 자바 프로그래밍 언어&#x20;
* JDK 에 들어있는 자바 컴파일러(javac)를 사용하여 바이트코드(.class) 로 컴파일 할 수 있다.&#x20;
* 오라클에서는 Oracle JDK 11 버전부터 상용으로 사용할 때 유료화하는 정책을 발표했다.&#x20;
  * 이와 관련하여 java 11버전부터 유료인가에 대해 국내에서도 크고 작은 오해들이 있었다.&#x20;
  * 정확하게 말하자면, Oracle JDK 11버전부터만 상용 사용시 유료화가 되는 것이므로, openjdk 등을 쓰거나 다른 벤더사에서 제공하는 jdk 를 쓴다면, 여전히 자바는 무료로 사용이 가능하다.&#x20;





## 5. 참고

* [https://docs.oracle.com/javase/specs/jvms/se11/html/index.html](https://docs.oracle.com/javase/specs/jvms/se11/html/index.html)
* [https://medium.com/@javachampions/java-is-still-free-3-0-0-ocrt-2021-bca75c88d23b](https://medium.com/@javachampions/java-is-still-free-3-0-0-ocrt-2021-bca75c88d23b)
* [https://aboullaite.me/understanding-jit-compiler-just-in-time-compiler/](https://aboullaite.me/understanding-jit-compiler-just-in-time-compiler/)
* [https://howtodoinjava.com/java/basics/jdk-jre-jvm/](https://howtodoinjava.com/java/basics/jdk-jre-jvm/)
* [https://en.wikipedia.org/wiki/List\_of\_JVM\_languages](https://en.wikipedia.org/wiki/List\_of\_JVM\_languages)
