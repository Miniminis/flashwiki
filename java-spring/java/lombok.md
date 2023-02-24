# lombok 은 어떻게 동작하는 것일까?

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

> 지금까지 살펴본 것은 모두 소스코드에서 컴파일 완료된 `.class` 파일을 변경하여 사용하는 방법을 알아봤었다. 그렇다면, 컴파일 되기 전에 자바 소스코드 -> 컴파일 사이에 아예 조작하는 방법은 없을까?&#x20;
>
> \-> lombok 이 그 대표적인 예시가 된다.&#x20;



## 1. Lombok?&#x20;

* lombok 라이브러리는 보통 객체를 만들고 나서 자주 구현하게 되는 getters, setters, equals, hashCodes 와 같은 매소드들을 @Getter, @Setter, @EqualsAndHashCodes 과 같은 어노테이션과 어노테이션 프로세스를 제공하여 표준적으로 작성해야할 코드를 개발자 대신 생성해준다.&#x20;

## 2. 살펴보기&#x20;

### 2-1. 라이브러리 추가하기&#x20;

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```gradle

dependencies {
    compileOnly 'org.projectlombok:lombok'

    annotationProcessor 'org.projectlombok:lombok'
}
```

### 2-2. annotation-processor 체크 (in IntelliJ)&#x20;

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### 2-3. 코드 작성

```java
package me.flash.annotationprocessor;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Entity
@Table(name = "customer")
@Getter
@Setter
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long userId;

    private String userName;

    public User() {
    }

    public User(String name) {
        this.userName = name;
    }
}
```

```java
class UserTest {

    @Test
    void getUserNameTest() {
        User user = new User();
        user.setUserName("flash");

        assertThat(user.getUserName()).isEqualTo("flash");
    }
}
```

```java
//User.class file : annotation 에 의해서 자동으로 생성된 getters and setters 
public class User {
    @Id
    @GeneratedValue(
        strategy = GenerationType.IDENTITY
    )
    private Long userId;
    private String userName;

    public User() {
    }

    public User(String name) {
        this.userName = name;
    }

    public Long getUserId() {
        return this.userId;
    }

    public String getUserName() {
        return this.userName;
    }

    public void setUserId(final Long userId) {
        this.userId = userId;
    }

    public void setUserName(final String userName) {
        this.userName = userName;
    }
}
```



## 3. 원리&#x20;

* 컴파일 시점에 어노테이션 프로세서를 사용하여 소스코드의 AST 를 조작한다.&#x20;
  * [https://docs.oracle.com/javase/8/docs/api/javax/annotation/processing/Processor.html](https://docs.oracle.com/javase/8/docs/api/javax/annotation/processing/Processor.html)
  * AST
    * abstract syntax tree&#x20;
    * [https://javaparser.org/inspecting-an-ast/](https://javaparser.org/inspecting-an-ast/)&#x20;



## 4. 논란&#x20;

* 공개된 API 가 아니라 컴파일러 내부 클래스를 사용하여 기존 소스코드를 조작하고 있다.&#x20;
* 특히 이클립스의 경우는 javaagent 를 사용하여 컴파일러 클래스까지 조작하여 사용한다. 해당 클래스들 역시 공개된 API 가 아니다보니, 버전 호환성에 문제가 생길 수 있고, 언제라도 그런 문제가 발생해도 이상하지 않다.&#x20;
* 이렇게 논란이 많지만, 롬복 자체가 너무 편하고, 이를 대체할 수 있을 만큼 편리한 것이 없기 때문에, 널리 사용되고 있는 상황이다.&#x20;
  * [AutoValue](https://github.com/google/auto/blob/main/value/userguide/index.md)
  * [Immutables](https://immutables.github.io)&#x20;



## 5. 참고&#x20;

* [https://projectlombok.org/features/](https://projectlombok.org/features/)
* [https://docs.oracle.com/javase/8/docs/api/javax/annotation/processing/Processor.html](https://docs.oracle.com/javase/8/docs/api/javax/annotation/processing/Processor.html)
* [https://projectlombok.org/contributing/lombok-execution-path](https://projectlombok.org/contributing/lombok-execution-path)
* [https://stackoverflow.com/questions/36563807/can-i-add-a-method-to-a-class-from-a-compile-time-annotation](https://stackoverflow.com/questions/36563807/can-i-add-a-method-to-a-class-from-a-compile-time-annotation)
* [http://jnb.ociweb.com/jnb/jnbJan2010.html#controversy](http://jnb.ociweb.com/jnb/jnbJan2010.html#controversy)
* [https://www.oracle.com/technical-resources/articles/java/java-5-features.html](https://www.oracle.com/technical-resources/articles/java/java-5-features.html)
