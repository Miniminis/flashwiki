# JVM 의 구조

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}



<figure><img src="../../.gitbook/assets/image (15) (1) (2).png" alt=""><figcaption><p><a href="https://www.javainterviewpoint.com/java-virtual-machine-architecture-in-java/jvm-architecture/">https://www.javainterviewpoint.com/java-virtual-machine-architecture-in-java/jvm-architecture/</a></p></figcaption></figure>



## 1. Class Loader&#x20;

.class 로 컴파일된 자바 바이트 코드는 메모리에 저장된다.&#x20;

### 1-1. Loading&#x20;

로딩이 시작되면 저장된 클래스 파일을 메모리로부터 읽는다.&#x20;

### 1-2. Linking&#x20;

링킹이 시작되면 클래스 파일 내 객체, 레퍼런스 간 연결을 수행한다.&#x20;

### 1-3. Initialization&#x20;

초기화 과정에서는 모든 static 값들을 초기화하고, 변수에 할당한다.&#x20;



## 2. Memory&#x20;

### 1-1. method&#x20;

* 메모리 영역에는 클래스, 부모클래스, 메소드, 변수 등이 저장된다.&#x20;
* 프로그램 내 어디에서든지 참조할 수 있는 공유자원이다.&#x20;

### 1-2. heap&#x20;

* 힙 영역에는 객체가 저장된다.&#x20;
* 역시 프로그램 내 어디서든 참조할 수 있는 공유 자원이다.&#x20;

### 1-3. stack&#x20;

* 쓰레드가 시작되면 런타임 스택이 생성된다.&#x20;
* 이제 메소드가 호출될 때마다 런타임 스택에 스택 프레임 블럭이 쌓이게 된다.&#x20;
* 쓰레드가 끝나면 런타임 스택도 비워지게 된다.&#x20;

### 1-4. program counter register&#x20;

* 쓰레드 별로 수행할 명령어의 위치를 가리키는 포인터이다.&#x20;

### 1-5. native method stack&#x20;

* 네이티브 메소드를 호출할 때 쌓이는 스택이다.&#x20;
* 역시 쓰레드 단위로 생성되며, 쓰레드가 끝나면 사라진다.&#x20;



## 3. 실행 엔진, execution engine&#x20;

### 3-1. interpreter&#x20;

* 바이트 코드를 한줄씩 읽는다.&#x20;

### 3-2. JIT compiler (Just In Time)&#x20;

* 인터프리터가 바이트 코드를 읽을 때, 반복되는 구절이 있다면 전부 네이티브 코드로 변환해버린다.&#x20;
* 이후 인터프리터가 반복되는 코드를 읽는다면, 그냥 네이티브 코드 자체르 실행하기만 하면 된다.&#x20;
* 효율이 증가한다.&#x20;

### 3-3. GC (Garbage Collecttion)&#x20;

* 더이상 참조되지 않는 객체를 모아서 정리하는 역할을 한다.&#x20;



## 4. JNI, Native method interface&#x20;

* 자바 프로그램에서 C, C++, assembly 언어로 작성된 네이티브 메소드를 실행할 수 있도록 해준다.&#x20;
* 메소드에 native 키워드가 붙은 메소드들을 말한다.&#x20;



## 5. Native method library&#x20;

* C, C++로 작성된 라이브러리를 말한다.&#x20;



## 6. 참고&#x20;

* [https://javapapers.com/core-java/java-jvm-run-time-data-areas/#Program\_Counter\_PC\_Register](https://javapapers.com/core-java/java-jvm-run-time-data-areas/#Program\_Counter\_PC\_Register)
* [https://schlining.medium.com/a-simple-java-native-interface-jni-example-in-java-and-scala-68fdafe76f5f](https://schlining.medium.com/a-simple-java-native-interface-jni-example-in-java-and-scala-68fdafe76f5f)
* [https://www.geeksforgeeks.org/jvm-works-jvm-architecture/](https://www.geeksforgeeks.org/jvm-works-jvm-architecture/)
* [https://dzone.com/articles/jvm-architecture-explained](https://dzone.com/articles/jvm-architecture-explained)
* [https://blog.jamesdbloom.com/JVMInternals.html](https://blog.jamesdbloom.com/JVMInternals.html)

