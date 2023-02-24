# java 8 소개

{% hint style="info" %}
백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;
{% endhint %}

## 1. 자바 8 소개&#x20;

* 2014년 3월 출시됨&#x20;
* LTS 버전&#x20;
* 최근 업데이트 히스토리는 [이곳](https://www.oracle.com/java/technologies/javase/8u-relnotes.html) 참고

> [IntelliJ의 2021년 리서치](https://www.jetbrains.com/lp/devecosystem-2021/java/)에 따르면, 현재 한국 개발자의 53%가 자바를 사용중이라고 한다. &#x20;
>
> 그들 중 72%는 자바 8버전을 사용중이다. &#x20;

## 2. LTS vs. non-LTS&#x20;

* 비-LTS 는 업데이트 제공 기간이 짧다.&#x20;
  * 배포 주기가 6개월인데, 지원 기간이 배포 이후 6개월이다.&#x20;
  * 즉, 릴리즈된 뒤, 6개월까지만 지원하고, 다시 새로운 버전이 나온다는 뜻이다....!&#x20;
* LTS 배포 주기는 3년이고 위의 비-LTS 주기와 함께 본다면, 결국 매 6번째 릴리즈가 LTS 버전이 되는 셈이다.&#x20;
* LTS 의 지원기간은 5년이상인데, 이것은 보통 JDK 를 제공하는 벤더와 이용하는 서비스에 따라서 다르다.&#x20;
* 실제 서비스 운영환경(production) 에서는 LTS 버전을 사용할 것을 권장하고 있다.&#x20;
  * [Moving Java Forward Faster](https://mreinhold.org/blog/forward-faster) by Mark Reinhold the Chief Architect of the Java Platform Group&#x20;
* 매년 3월과 9월에 새 버전이 배포된다.&#x20;
* 현재는 [자바 19버전](https://www.oracle.com/java/technologies/javase/19u-relnotes.html)까지 나온상태이다.&#x20;

## 3. 주요 기능&#x20;

* 람다 표현식&#x20;
* 메소드 레퍼런스&#x20;
* 스트림 API&#x20;
* Optional\<T>
* 등&#x20;

## 4. JDK download&#x20;

* [오라클 JDK](https://www.oracle.com/java/technologies/downloads/)&#x20;
* Open JDK
  * [Oracle](https://jdk.java.net/19/)&#x20;
  * [AdoptOpenDJK ](https://adoptium.net)
  * Amazon Corretto
  * Azul Zulu

## 5. 참고  &#x20;

* [지금까지 모든 자바 릴리즈 노트](https://www.oracle.com/java/technologies/javase/jdk-relnotes-index.html)
* [Java SE Support Roadmap](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)
* [What Does Long-Term Support Mean for OpenJDK?](https://www.javacodegeeks.com/2019/07/long-term-support-mean-openjdk.html)
* [Wikipedia - Java Version History](https://en.wikipedia.org/wiki/Java\_version\_history) &#x20;
