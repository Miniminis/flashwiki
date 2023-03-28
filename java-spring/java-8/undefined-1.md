# 인터페이스의 변화

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. 인터페이스의 변화&#x20;

java8부터 발생한 인터페이스의 변화 덕분에 자바 라이브러리도 많은 변화가 있었다.&#x20;



## 2. default method

* 인터페이스에 메소드 선언이 아니라 구현체를 제공하는 방법&#x20;

### 2-1. 해당 인터페이스를 구현한 클래스를 깨트리지 않고 새로운 기능을 추가할 수 있다.&#x20;

```java
public interface Foo {

    String getName();

    default String getUpperCaseName() {
        return getName().toUpperCase(Locale.ROOT);
    }
}

//DefaultFoo 에서 getUpperCaseName() 구현하지 않아도 기본으로 제공된다. 
public class DefaultFoo implements Foo {

    private String name;

    private DefaultFoo() {
    }

    public DefaultFoo(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return this.name;
    }
}

```

### 2-2. 기본 메소드는 구현체가 모르게 추가된 기능이니 그만큼 위험성이 있다.&#x20;

* 컴파일 에러는 아니지만 구현체에 따라서 런타임 에러가 발생할 수 있다.&#x20;
* 때문에 반드시 문서화하는 것이 필요하다. (@implSpec 자바 독 태그를 사용한다)

```java
/**
 * @implSpec
 * getName() 을 호출하여 대문자로 바꿔주는 기능을 한다.
 * */
default String getUpperCaseName() {
    return getName().toUpperCase(Locale.ROOT);
}
```

### 2-3. Object 가 제공하는 기능 (equals, hashCode)는 기본 메소드로 제공할 수 없다.&#x20;

* 구현체가 재정의해야한다.&#x20;

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

### 2-4. 본인이 수정할 수 있는 인터페이스에만 기본 메소드를 제공할 수 있다.&#x20;

* 본인이 수정할 수 있는 범위가 아닌 인터페이스에는 기본 메소드를 제공할 수 없다.&#x20;

### 2-5. 인터페이스를 상속받는 인터페이스에서 다시 추상메소드로 변경할 수 있다.&#x20;

```java
public interface Foo {

    String getName();

    String greeting();
}

public interface Boo extends Foo {

    @Override
    default String greeting() {
        return "hello from Boo!";
    }
}


public class DefaultBoo implements Boo {

    private String name;

    private DefaultBoo() {
    }

    public DefaultBoo(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }
}


@Test
void 인터페이스를_상속받은_인터페이스에서_다시추상메소드로_변경가능() {
    DefaultBoo defaultBoo = new DefaultBoo("kildong");
    assertThat(defaultBoo.greeting()).isEqualTo("hello from Boo!");
}
```

### 2-6. 인터페이스 구현체가 재정의할 수도 있다. &#x20;

```java
public class DefaultBoo implements Boo {

    private String name;

    private DefaultBoo() {
    }

    public DefaultBoo(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public String getUpperCaseName() {
        return getName().toUpperCase(Locale.ROOT) + "!!";
    }
}

@Test
void 인터페이스_구현체가_재정의할수도있다() {
    DefaultBoo defaultBoo = new DefaultBoo("kildong");
    assertThat(defaultBoo.getUpperCaseName()).isEqualTo("KILDONG!!");
}
```

## 3. static method&#x20;

* 해당 타입 관련 헬퍼 혹은 유틸리티 메소드를 제공할 때, 인터페이스에 스태틱 메소드를 제공할 수 있다.&#x20;

```java
public interface Foo {
    ...

    static String koreanGreeting() {
        return "안녕하세요!";
    }
}

@Test
void 스태틱메소드를_제공할수있다() {
    assertThat(Foo.koreanGreeting()).isEqualTo("안녕하세요!");
}
```

## 4. 참고&#x20;

* https://docs.oracle.com/javase/tutorial/java/IandI/nogrow.html
* https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html
