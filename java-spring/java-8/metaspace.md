# Metaspace

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. java8 이전, PermGen&#x20;

* JVM 의 힙 메모리 영역에는 원래 Eden generation, Old generation, Permanent generation 메모리 영역이 존재했었다.&#x20;
* PermGen 은 Permanent Generation 의 약자로, 클래스의 메타데이터를 모으는 곳이다.&#x20;
* heap 영역에 속하며,&#x20;
* 기본값으로 제한된 (고정된) 크기를 가지고 있다.&#x20;
* 동적으로 클래스를 많이 생성하거나 너무 많은 클래스가 생성되면 PermGen 영역이 꽉차서 OutofMemory Exception 이 발생할 수 있다.&#x20;
  * 이때는 클래스를 계속 생성하는 것이 문제이므로, 원인이 되는 쪽 코드를 찾아 개선하는 수 밖에 없다.&#x20;
* PermGen 초기 사이즈 설정 : -XX:PermSize=N
* PermGen 최대 사이즈 설정 : -XX:MaxPermSize=N &#x20;

## 2. java8 이후, Metaspace&#x20;

* java8 이후로는 PermGen 메모이 영역이 사라지고, metaspace 라는 영역이 새로 생겼다.&#x20;
* 역시 클래스의 메타데이터를 담는 곳으로
* Heap 영역이 아니라 Native memory 영역에 존재한다.&#x20;
* 자바 8부터는 PermGen 관련 java 옵션은 무시된다.&#x20;
* Metaspace 초기 사이즈 설정 : -XX:MetaspaceSize=N
* Metaspace 최대 사이즈 설정 : -XX:MaxMetaspaceSize=N

## 3. 참고

* http://mail.openjdk.java.net/pipermail/hotspot-dev/2012-September/006679.html
*   https://m.post.naver.com/viewer/postView.nhn?volumeNo=23726161\&memberNo=3673

    3075
*   https://m.post.naver.com/viewer/postView.nhn?volumeNo=24042502\&memberNo=3673

    3075
* https://dzone.com/articles/java-8-permgen-metaspace
