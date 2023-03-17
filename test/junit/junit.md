# Junit 기본 개념

> 인프런 백기선님 <[더 자바, 애플리케이션을 테스트하는 다양한 방법](https://www.inflearn.com/course/the-java-application-test)> 강의를 듣고 공부한 내용을 정리합니다.&#x20;

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p><a href="https://1.bp.blogspot.com/-GZA-TeXd_Ts/YMb-n4EhCTI/AAAAAAAAlGo/BHZxxOXNt0UkpNhaXlevpdhDZFKnAzyJgCLcBGAsYHQ/s902/junit5.png">https://1.bp.blogspot.com/-GZA-TeXd_Ts/YMb-n4EhCTI/AAAAAAAAlGo/BHZxxOXNt0UkpNhaXlevpdhDZFKnAzyJgCLcBGAsYHQ/s902/junit5.png</a></p></figcaption></figure>

## 1. Junit

* Junit 은 자바 개발자가 가장 많이 사용하는 테스팅 프레임워크이다.&#x20;
* 사실은 Junit 이외에도 Spoke, TestNG 등 다양한 테스트 프레임워크가 존재하는데, [단위테스트를 작성하는 자바 개발자 90%이상은 Junit 을 사용하고 있다](https://www.jetbrains.com/lp/devecosystem-2019/java/)고한다.&#x20;
  * 사실 사용법도 비슷비슷해서 Junit 만 알아두어도 충분하다.&#x20;
* 구조를 보면, 크게 3가지로 구분되어있다.&#x20;
  * Junit Platform : 테스트를 실행해주는 런처를 제공해준다. TestEngine API 를 제공한다.&#x20;
  * Jupiter : TestEngine API 의 구현체이다. Junit5 를 제공해준다.&#x20;
  * Vintage : TestEngine 의 구현체이다. Junit3과 4를 제원한다.&#x20;
* 따라서 요즘에는 Jupiter 기반으로 Junit5 로 테스트 코드를 많이 작성한다.&#x20;
* [Junit user guide](https://junit.org/junit5/docs/current/user-guide/)

## 2. 시작하기&#x20;

* 스프링부트 2.2+버전 프로젝트는 기본적으로 Junit5 의존성이 추가되었다.&#x20;

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

### 2-1. 만약 스프링 부트를 사용하지 않고 테스트를 하고 싶다면&#x20;

* 아래의 의존성을 추가한다.&#x20;

```xml
<dependency> 
    <groupId>org.junit.jupiter</groupId> 
    <artifactId>junit-jupiter-engine</artifactId> 
    <version>5.5.2</version> <scope>test</scope>
</dependency>
```

### 2-2. 기본 어노테이션&#x20;

```java

public class JunitTest {

    @BeforeAll
    static void beforeAll() {
        System.out.println("before all");
        System.out.println();
    }

    @AfterAll
    static void afterAll() {
        System.out.println();
        System.out.println("after all");
    }

    @BeforeEach
    void beforeEach() {
        System.out.println("before each");
    }

    @AfterEach
    void afterEach() {
        System.out.println("after each");
        System.out.println();
    }

    @Test
    void createTest1() {
        System.out.println("create test 1");
    }

    @Test
    void createTest2() {
        System.out.println("create test 2");
    }

    //result : 
    //before all
    //
    //before each
    //create test 1
    //after each
    //
    //before each
    //create test 2
    //after each
    //
    //
    //after all

}
```

## 3. 테스트 이름 표시&#x20;

* @DisplayNameGeneration
  * Method와 Class 레퍼런스를 사용해서 테스트 이름을 표기하는 방법 설정이다.&#x20;
  * 기본 구현체로 ReplaceUnderscores 제공한다.&#x20;
* @DisplayName
  * 어떤 테스트인지 테스트 이름을 보다 쉽게 표현할 수 있는 방법을 제공하는 애노테이션이다.&#x20;
  * @DisplayNameGeneration 보다 우선 순위가 높다. 두 가지를 같이 쓰면, @DisplayName 에서 설정한 이름으로 표시된다.&#x20;
* [참고](https://junit.org/junit5/docs/current/user-guide/#writing-tests-display-names)

```java
// snake_case 의 언더바를 공백 문자열로 바꿔준다.  
@DisplayNameGeneration(DisplayNameGenerator.ReplaceUnderscores.class)
public class DisplayNameTest {

    @Test
    void two_string_are_equal() {
        String hello = "hello";
        String hello2 = "hello";

        assertEquals(hello, hello2);
    }

    @Test
    @DisplayName("두 문자열은 같지 않다.")   //테스트 이름을 명시한다. 
    void two_string_are_not_equal() {
        String hello = "hello";
        String hi = "hi";

        assertNotEquals(hello, hi);
    }
}
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## 4. Junit : Assertion&#x20;

* assertTimeout(duration, executable)
* assertTimeoutPreeptively(duration, executable)
  * 별도의 쓰레드를 이용해서 진행하기 때문에 테스트 시에 적용되는 트랜잭션이 정상적으로 동작하지 않을 수도 있다. 주의해서 사용할 것!  &#x20;
