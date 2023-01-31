# 싱글톤 패턴

## 개념

* 인스턴스가 단 1개만 존재해야할 때 사용되는 패턴이다.
* 시스템 런타임이나 환경변수 등 인스턴스가 여러 개가 되면 문제가 발생하는 경우, 이러한 패턴을 이용하여 인스턴스가 단 1개만 생성되어 해당 인스턴스가 모든 곳에서 공유되어 사용되도록 한다.&#x20;
* 구현 방법은 다양하지만, 추천되는 방법은 6번과 7번이다. &#x20;
  * 실제로 이펙티브 자바를 쓴 조슈아 블로크는 7번 방법을 가장 추천했다고 한다.&#x20;

## 방법 1. 가장 기본적인 private + static

```java
public class Mother {

    private static Mother self;

    private Mother() {}

    public static Mother getInstance() {
        if (self == null) {
            self = new Mother();
        }
        return self;
    }

}
```

### 자세히&#x20;

* 생성자를 private 으로 만든 이유는?&#x20;
  * Mother class 의 인스턴스를 아무 곳에서나 마음대로 생성하지 못하도록 private 을 통해 접근 제어를 해주었다.&#x20;
* **getInstance() 를 static 으로 선언한 이유는?**&#x20;
  * static, non-static 과 상관없이 모든 곳에서 인스턴스에 접근하여 사용할 수 있도록 하기 위해서
* &#x20;getInstance() 가 멀티 쓰레드 환경에서 안전하지 않은 이유는 무엇인가
  * 쓰레드 1번이 getInstance() 에 최초로 접근하여 최초의 인스턴스가 생성되는 중에 쓰레드 2번이 같은 매소드가 동시에 접근하여 인스턴스를 다시 생성할 수 있으므로.&#x20;
  * 즉, 쓰레드 1번에 getInstance() 에 접근하는 동안, 다른 쓰레드들이 접근할 수 있기 때문이다.&#x20;

### 문제점

* 웹 어플리케이션은 대부분 멀티 쓰레드 환경을 기반으로 한다. 이때, 하나의 쓰레드가 getInstance() 에 접근하는 동안 다른 쓰레드들 역시 동시에 접근할 수 있기 때문에, 자칫하면 인스턴스가 하나 이상 생성될 가능성이 있다.&#x20;



## 방법 2. synchronized

* synchronized 키워드를 붙여서 문제가 되고 있는 getInstance() 매소드를 접근하고 있는 쓰레드가 존재하는 동안에는 lock 을 걸어 접근 제어를 해버린다.&#x20;

```java

public class Mother2 {

    private static Mother2 self;

    private Mother2() {}

    public static synchronized Mother2 getInstance() {
        if (self == null) {
            self = new Mother2();
        }
        return self;
    }
}
```

### 자세히

* 자바에서 동기화 블록의 처리 방법은?
  * 자바에서는 synchronized 키워드를 이용하여 동기화 블록을 처리한다.&#x20;
  * 해당 키워드가 붙은 영역은 한 시점에 한 쓰레드만 접근 가능하도록 보장해주며, 이미 한 쓰레드가 점유하고 있는 동안 다른 쓰레드는 모두 blocked 상태가 된다.&#x20;
  * synchronized 키워드를 어디에 배치시키느냐에 따라서 4가지 유형이 존재한다.&#x20;
    * [synchronized.md](../../java-spring/java/synchronized.md "mention")
* getInstance() 메소드 시 동기화 하는 lock 은 인스턴스 lock 인가, 클래스 lock 인가?&#x20;
  * 클래스 lock 이다. Mother class 전체에 대해서 접근 제한을 하고 있으므로 동기화의 대상이 클래스 전체이다. 따라서 이 메소드는 클래스 lock을 통해 동기화를 수행한다.&#x20;

### 문제점

* synchronized 로 정의된 매소드는 매번 locking 이 걸리기 때문에 성능상 이슈가 존재한다.&#x20;
  * 사실 최초로 생성되는 때에만 동시성이 문제가 되고, 그 이후로는 굳이 동시성 이슈를 신경쓰지 않아도 되는데, 이 문제를 어떻게 해결할 수 있을까?

## 방법 3. eager initialization

* synchronized 로 인한 locking 문제를 해결하기 위해 여러가지 방법이 등장했다. 그 중 하나가 바로 이른 초기화 방법이다.&#x20;

```java
public class Mother3 {
    private static final Mother3 SELF = new Mother3();

    private Mother3() {}

    public static Mother3 getInstance() {
        return SELF;
    }
}

//혹은 정적 블록을 사용하는 방법도 있다. 
public class Mother3 {
    private static Mother3 self;
    
    static {
        self = new Mother3();
    }

    private Mother3() {}

    public static Mother3 getInstance() {
        return self;
    }
}
```

### 자세히

* &#x20;이른 초기화의 단점은 무엇인가?
  * 하단 참고&#x20;
* <mark style="background-color:yellow;">**만약 생성자에서 checked 예외를 던진다면, 코드를 어떻게 수정할 수 있겠는가**</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;"></mark>&#x20;

### 문제점

* 어플리케이션이 구동될 때 즉시 만들어지므로, 해당 인스턴스가 생성되기 전까지는 오랜 시간동안 쓸모없는 자원이 낭비되는 셈이다.&#x20;

## 방법 4. static block 사용하기&#x20;

```java
//혹은 정적 블록을 사용하는 방법도 있다. 
public class Mother3 { 
    private static Mother3 self;

    static {
        self = new Mother3();
    }
    
    private Mother3() {}
    
    public static Mother3 getInstance() {
        return self;
    }
}
```



## 방법 5. double-checked-locking

```java
public class Mother4 {
    private static volatile Mother4 self;

    private Mother4 () {}

    public static Mother4 getInstance() {
        if (self == null) {
            synchronized (Mother4.class) {
                if (self == null) {
                    self = new Mother4();
                }
            }
        }
        return self;
    }
}
```

### 자세히

* double-checked locking 이라고 불리는 이유는?&#x20;
  * getInstance() 매소드를 살펴보면, 인스턴스가 생성되었는지 여부를 두번에 걸쳐서 확인하고 있다. 그 때문에 위와 같은 이름이 붙었다.&#x20;
* instance 를 필요한 시점에 만들 수 있다.&#x20;
* instance 변수는 어떻게 정의해야하는가?&#x20;
  * **volatile 키워드를 추가해서 정의해야한다.**&#x20;
  * 메모리 구조는 메인 메모리 위에 CPU 캐시 메모리라고 불리는 L3, L2,L1 캐시가 존재한다.
  * java 에서는 스레드가 2개 열리면 변수를 메인메모리인 RAM 에서 불러오는 것이 아니라, 캐시 메모리에서 각각의 캐시 메모리를 기반으로 가져오게 된다.&#x20;
  * 때문에 쓰레드에서 동시에 접근하는 변수의 값은 서로 불일치할 수 있다.&#x20;
  * 이때, volatile 키워드를 사용하게 되면, 캐시메모리가 아니라 메인 메모리를 기반으로 저장하고 읽어오게 되고, 이러한 값 불일치 문제를 해결할 수 있게 된다.&#x20;

### 문제점

* volatile 키워드 추가 및 두번이나 인스턴스 생성 여부 체크 등 코드가 전체적으로 장황해진다.&#x20;

## 방법 6. static inner class (✨recommended✨)

```java
public class Mother5 {

    private Mother5() {}

    public static class Mother5Holder {
        private static final Mother5 SELF = new Mother5();

    }

    public static Mother5 getInstance() {
        return Mother5Holder.SELF;
    }
}
```

### 자세히&#x20;

* 실제로 권장되는 방법 중 하나
* 이 방법으로는 멀티 쓰레드 환경에서도 동기화 문제도 해결이 가능하다.&#x20;
* static final 을 썼는데도 왜 지연 초기화 (lazy loading)이라고 볼 수 있는가?&#x20;
  * 실제로 getInstance() 가 호출되는 시점에 Mother5Holder 내부 클래스가 로딩이 되고, SELF 인스턴스가 초기화 되기 때문에 지연 로딩이 가능해진다.&#x20;

## 방법 7.enum class (✨recommended✨)

```java
public enum Mother6 {
    SELF;
}
```

### 자세히

* 리플렉션에 안전하다.&#x20;
* 직렬화, 역직렬화에 안전하다.&#x20;
* **enum type 의 인스턴스를 리팩토링을 만들 수 있는가**&#x20;
* enum type 으로 싱글톤을 구현할 때의 문제점은 무엇인가&#x20;
  * 클래스 로드시에 미리 만들어지기 때문에 사용 시점 이전까지는 필요없는 자원이 낭비되는 꼴이다.&#x20;
  * 상속을 쓰지 못한다.&#x20;
* 직렬화와 역직렬화 시에도 별도로 구현해야하는 매소드가 존재하는가
  * 없다. Enum 클래스 자체가 이미 serializable 을 구현하고 있기 때문에 신경쓰지 않아도 된다.&#x20;

## 권장하는 패턴 2가지&#x20;

* static inner class 방식의 6번과 enum 을 이용한 7번방법이 권장된다.&#x20;

## 싱글톤을 깨는 방법들

### 1. 리플렉션 이용하기&#x20;

```java
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class ReflectionApp {
    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Mother1 mother1 = Mother1.getInstance();

        Constructor<Mother1> declaredConstructor = Mother1.class.getDeclaredConstructor();
        declaredConstructor.setAccessible(true);
        Mother1 mother2 = declaredConstructor.newInstance();

        System.out.println(mother1 == mother2);
    }
}
```

* 리플렉션이란
  * 구체적인 클래스 타입을 알지 못해도 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바의 API 이다.&#x20;
  * [참고](https://dublin-java.tistory.com/53)
* setAccessible(true) 를 사용하는 이유는?

### 2. 직렬화, 역직렬화 이용하기&#x20;

```java
public class Mother2 implements Serializable {
...
}

public class SerializableApp {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Mother2 mother1 = Mother2.getInstance();
        Mother2 mother2 = null;

        try (ObjectOutput out = new ObjectOutputStream(new FileOutputStream("settings.obj"))) {
            out.writeObject(mother1);
        }

        try (ObjectInput in = new ObjectInputStream(new FileInputStream("settings.obj"))) {
            mother2 = (Mother2) in.readObject();
        }

        System.out.println(mother1 == mother2);
    }
}
```

* 자바의 직렬화롸 역직렬화에 대해서 설명해보기&#x20;
* SerializableId 란 무엇인가&#x20;
* try-resource 블럭에 대해 설명해보자.&#x20;



{% hint style="info" %}
그렇다면, 실제로 싱글톤 패턴은 어떻게 사용되는가...? 그것이 알고싶다...!!!!&#x20;
{% endhint %}

## Java 에서의 사용 예시&#x20;

* Java 의 java.lang.Runtime&#x20;

```java
public class JavaSingletonApp {
    public static void main(String[] args) {
        Runtime runtime = Runtime.getRuntime();
        System.out.println(runtime.maxMemory());
        System.out.println(runtime.freeMemory());
    }
}
```

## Spring 에서의 사용 예시

* Spring 에서 bean 의 스코프 중에서 싱글톤 스코프
* 스프링 빈으로 등록되면, ApplicationContext 내부에서 유일한 인스턴스로 존재하게 된다.&#x20;

```java
//SpringConfig
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {

    @Bean
    public String info() {
        return "this is info";
    }
}


//SpringSingletonApp
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class SpringSingletonApp {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
        String info = applicationContext.getBean("info", String.class);
        String info2 = applicationContext.getBean("info", String.class);

        System.out.println(info == info2);
    }
}


/**
 * 16:14:39.571 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@1d7acb34
 * 16:14:39.589 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.annotation.internalConfigurationAnnotationProcessor'
 * 16:14:39.689 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.event.internalEventListenerProcessor'
 * 16:14:39.690 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.event.internalEventListenerFactory'
 * 16:14:39.692 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.annotation.internalAutowiredAnnotationProcessor'
 * 16:14:39.693 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.annotation.internalCommonAnnotationProcessor'
 * 16:14:39.697 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'org.springframework.context.annotation.internalPersistenceAnnotationProcessor'
 * 16:14:39.702 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'springConfig'
 * 16:14:39.709 [main] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - Creating shared instance of singleton bean 'info'
 * */
```

## 다른 예시&#x20;

* 다른 디자인 패턴(빌더, 퍼사드, 추상 팩토리 등) 구현체의 일부로 쓰이기도 한다.&#x20;
* 데이터베이스 연결모듈에도 많이 쓰인다.&#x20;



## 복습하기&#x20;

* java 에서 enum 을 사용하지 않고 싱글톤 패턴을 구현하는 방법은 무엇인가&#x20;
  * private constructor + public static method &#x20;
  * synchronized&#x20;
  * eagar initialization&#x20;
  * static inner class&#x20;
* private 생성자와 static 매소드를 사용하는 방법의 단점은&#x20;
  * 멀티 쓰레드 환경에서 하나의 인스턴스만을 보장하지 못한다.&#x20;
  * 동시에 static method 에 접근할 경우, 인스턴스가 하나 이상 생성될 위험이 존재한다.&#x20;
* enum 을 사용해 싱글톤 패턴을 구현하는 방법의 장점과 단점은&#x20;
  * 우선 리플렉션이나 직렬화 등의 방법으로도 절대 깨지지 않는 싱글톤을 만들 수 있고&#x20;
  * 코드도 간결하다는 장점이 있다.&#x20;
  * 하지만 상속을 사용하지 못한다는 점과&#x20;
  * 클래스 로드시에 미리 생성된다는 점은 여전히 단점으로 존재한다.&#x20;
