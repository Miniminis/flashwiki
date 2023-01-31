# synchronized

{% hint style="info" %}
이 글은 [박철우님의 블로그](https://parkcheolu.tistory.com/15)를 보고 이해한 내용을 정리했습니다.&#x20;
{% endhint %}

멀티 쓰레드 환경에서 프로그래밍을 할 때, 동기화 이슈에 주의해야한다. 민감한 값에 대하여 여러 개의 쓰레드가 동시에 접근하여 생성, 수정, 삭제하게 되면, 데이터가 일정하게 유지되지 않고 오염되기 때문이다.&#x20;



## 자바에서 동기화 방법

* 자바에서는 synchronized 키워드를 이용하여 동기화 블록에 대한 처리를 할 수 있다.&#x20;
* 해당 키워드가 붙은 메소드는 한 시점에 하나의 쓰레드만 접근하도록 하는 것을 보장해주며, 다른 쓰레드들은 block 상태로 기다리게 된다.&#x20;
* synchronized의 위치 및 동기화 범위에 따라서 4가지 다른 유형이 존재할 수 있다. &#x20;

```java
public class Mother2Counter {

    private static int count;

    //1. instance method sync
    public synchronized void add1(int num) {
        this.count += num;
    }

    //2. static method sync
    public static synchronized void add2(int num) {
        count += num;
    }

    //3. instance method code block sync
    public void add3(int num) {
        synchronized (this) {
            this.count += num;
        }
    }

    //4. static method code block sync
    public static void add4(int num) {
        synchronized (Mother2Counter.class) {
            count += num;
        }
    }
}
```

### 1. 인스턴스 메소드 - 인스턴스

* 동기화의 기준이 인스턴스이다. 하나의 인스턴스를 기준으로 한 시점에 오직 하나의 쓰레드만 동기화된 인스턴스 매소드를 실행할 수 있다.&#x20;
* 인스턴스 당 하나의 쓰레드이다. &#x20;

### 2. 스태틱 메소드 - 클래스

* 동기화의 기준이 클래스 객체가 된다. 하나의 클래스 객체를 기준으로 한 시점에 오직 하나의 쓰레드만 접근이 가능하다.&#x20;
* 클래스 당 하나의 쓰레드이다.&#x20;

### 3. 인스턴스 내부의 코드 블록

* 1번의 방법에서 조금 더 개선된 형태이다. 1번의 경우, 매소드 전체가 동기화 대상으로 묶이게 되지만, 3번과 같이 진행하면, count 변수를 증가하는 연산에 대해서만 동기화가 적용된다.
* synchronized(this) 매개변수로 add 를 호출한 인스턴스가 전달되고 있다. 이를 모니터 객체라고 한다. 바로 이 객체를 기준으로 동기화가 진행된다.&#x20;
* 1번과 3번의 코드는 한 시점에 두 코드 중 하나만 실행할 수 있다.&#x20;
* 만약 synchronized(this) 매개변수에 this 가 아니라 다른 인스턴스가 전달된다면, 동기화의 대상이 서로 다르게 되므로, 동시에 실행이 가능하게 된다.  &#x20;

### 4. 스태틱 메소드 내부의 코드 블록

* 2번을 좀 더 개선해보았다. 사실상 2번과 같은 역할을 한다.&#x20;



## 좀 더 공부할 내용들

* 자바의 Concurrency utility&#x20;
  * 위의 내용이 java 에서 시도한 첫번째 동기화 매커니즘이었으나, 완벽하지 않았다.&#x20;
  * 더 나은 동시성 컨트롤을 위해서 Concurrency 가 등장하였다고 한다.&#x20;
