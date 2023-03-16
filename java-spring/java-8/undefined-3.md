# 함수형 인터페이스

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. 개념 소개&#x20;

### 1-1. 함수형 인터페이스 (Functional Interface)&#x20;

* 추상 메소드를 딱 하나만 가진 인터페이스&#x20;
* [@FunctionalInterface](https://docs.oracle.com/javase/8/docs/api/java/lang/FunctionalInterface.html) 어노테이션을 가지고 있는 인터페이스&#x20;
* SAM (Single Abstrct Method) 인터페이스&#x20;

```java
package me.whiteship.java8to11._01_fi;

@FunctionalInterface    // 어노테이션을 달아서 명시해준다.  
public interface RunSomething {

    int plusTen(int num);

    // java8부터 interface 에 쓰일 수 있는 함수의 형태가 다양해졌다.
    // static method, default method 는 functional interface의 메소드로 카운트하지 않아서 
    //     아래와 같이 함수형 인터페이스에도 같이 사용 가능!
    static void printName(String name) {
        System.out.println(name);
    }

    default void printAge(int age) {
        System.out.println(age);
    }
}
```

```java
@Test
void 익명내부클래스로_표현한다() {
    RunSomething runSomething = new RunSomething() {
        @Override
        public int plusTen(int num) {
            return num + 10;
        }
    };

    assertThat(runSomething.plusTen(10)).isEqualTo(20);
}

@Test
void 람다로_표현한다() {
    RunSomething runSomething = num -> num + 10;
    assertThat(runSomething.plusTen(10)).isEqualTo(20);
}
```

### 1-2. 람다 표현식 (Lambda Expression)&#x20;

* 함수형 인터페이스의 인스턴스를 만드는 방법으로 쓰일 수 있다.&#x20;
* 코드를 줄일 수 있다.&#x20;
* 메소드 매개변수, 리턴타입, 변수로 만들어 사용할 수 있다.&#x20;

### 1-3. 자바에서 함수형 프로그래밍시 주의할 점

1. 함수를 [First class object](https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B8%89\_%EA%B0%9D%EC%B2%B4) 로 사용할 수 있다.&#x20;
   1. [1급 함수 예시](https://victorydntmd.tistory.com/46)&#x20;
2. 순수함수(pure function)이다.&#x20;
   1. 사이드 이펙트가 없다. 함수 밖에 있는 값을 변경하지 않는다.&#x20;
   2. 상태가 없다. 함수 밖에 있는 값을 사용하지 않는다.&#x20;
3. 고차함수(higher-order function)
   1. 함수가 함수를 매개변수로 받을 수 있고, 함수를 리턴할 수도 있다.&#x20;
4. 불변성 : 같은 매개변수를 넣으면 항상 같은 값이 나온다.&#x20;

```java
@Test
void 일급함수로사용할수있다() {
    RunSomething runSomething = num -> num + 10;
    assertThat(runSomething.plusTen(10)).isEqualTo(20);
}

@Test
void 순수함수이다() {
    int baseNum = 10;
    RunSomething runSomething = num -> baseNum += num;   // 컴파일 에러
    runSomething.plusTen(10);
}

@Test
void 고차함수이다() {
    RunFactory runFactory = new RunFactory();
    RunSomething runSomething = runFactory.runSomething(num -> num + 100);    //매개변수로 전달 
    assertThat(runSomething.plusTen(10)).isEqualTo(110);
}

@Test
void 상태가없어야한다() {
    int baseNum = 15;
    RunSomething runSomething = num -> baseNum + num;    // 외부 상태값을 사용함 
    assertThat(runSomething.plusTen(10)).isEqualTo(25);
}
```



## 2. 자바에서 제공하는 함수형 인터페이스&#x20;

Java가 기본으로 제공하는 함수형 인터페이스가 존재한다.&#x20;

* java.lan.function package 에 속해있다.&#x20;
* 자바 차원에서 미리 자주 사용할만한 함수 인터페이스를 만들어둔 것이다.&#x20;



### 2-1. Function\<T, R>

* T 타입을 받아서 R타입을 리턴하는 인터페이스&#x20;
  * apply
  * andThen
  * compose&#x20;

```java
public class Plus10 implements Function<Integer, Integer> {

    @Override
    public Integer apply(Integer integer) {
        return integer + 10;
    }
}
```

```java
public class FunctionInterface {

    @Test
    void applyTest() {
        //구현체
        Plus10 plus10 = new Plus10();
        assertThat(plus10.apply(10)).isEqualTo(20);

        //lambda
        Function<Integer, Integer> plus11 = num -> num + 11;
        assertThat(plus11.apply(10)).isEqualTo(21);
    }

    @Test
    void composeTest() {
        Function<Integer, Integer> plus10 = num -> num + 10;
        Function<Integer, Integer> multiply5 = num -> num * 5;
        Function<Integer, Integer> both = plus10.compose(multiply5);

        assertThat(both.apply(2)).isEqualTo(20);

    }

    @Test
    void andThenTest() {
        Function<Integer, Integer> plus10 = num -> num + 10;
        Function<Integer, Integer> multiply5 = num -> num * 5;
        Function<Integer, Integer> both = plus10.andThen(multiply5);

        assertThat(both.apply(2)).isEqualTo(60);
    }
}
```

### 2-2. BiFunction\<T, U, R>

```java
public class BiFunctionTest {

    @Test
    void applyTest() {
        BiFunction<Integer, Boolean, String> isTrue = (num, bool) -> bool ? "true" : "false";
        assertThat(isTrue.apply(10, false)).isEqualTo("false");
    }
}
```

### 2-3. Consumer\<T>

```java
public class ConsumerSupplierTest {

    @Test
    void consumerAccept() {
        Consumer<Integer> consumer = num -> System.out.println(num);
        consumer.accept(10);
    }

    @Test
    void andThenTest() {
        Consumer<Integer> consumer = num -> System.out.println(num + 10);
        Consumer<Integer> consumer2 = num -> System.out.println(num + 20);
        Consumer<Integer> both = consumer.andThen(consumer2);
        both.accept(10);
    }

}
```

### 2-4. Supplier\<T>

```java
public class ConsumerSupplierTest {

    @Test
    void supplierGet() {
        Supplier<Integer> supplier = () -> 10;
        assertThat(supplier.get()).isEqualTo(10);
    }

}
```

### 2-5. Predicate\<T>

```java

public class PredicateTest {

    @Test
    void test() {
        Predicate<Integer> isEven = num -> num % 2 == 0;
        assertThat(isEven.test(11)).isFalse();
        assertThat(isEven.test(10)).isTrue();
    }

    @Test
    void combineTest() {
        Predicate<String> length6 = str -> str.length() == 6;
        Predicate<String> startsWithM = str -> str.startsWith("M");
        Predicate<String> containsT = str -> str.contains("T");

        Predicate<String> triple = length6.and(startsWithM).or(containsT);

        assertThat(triple.test("Mountain")).isFalse();
        assertThat(triple.test("moTher")).isTrue();
    }

    @Test
    void negateTest() {
        Predicate<String> length6 = str -> str.length() == 6;
        assertThat(length6.negate().test("hello")).isTrue();
    }
}
```

### 2-6. UnaryOperator\<T>

```java
public class OperatorTest {

    @Test
    void unaryOperatorTest() {
        Function<Integer, Integer> function = (i1) -> i1 + 10;
        UnaryOperator<Integer> unaryOperator = i1 -> i1 + 10;
        assertThat(function.apply(10)).isEqualTo(unaryOperator.apply(10));
    }
}
```

### 2-7. BinaryOperator\<T>

```java
public class OperatorTest {

    @Test
    void binaryOperatorTest() {
        BiFunction<Integer, Integer, Integer> biFunction = (i1, i2) -> i1 * i2;
        BinaryOperator<Integer> binaryOperator = (i1, i2) -> i1 * i2;
        assertThat(biFunction.apply(10, 12)).isEqualTo(binaryOperator.apply(10, 12));
    }
}
```



## 3. 람다 표현식&#x20;

### 3-1. 람다

* (인자 리스트) -> {바디}

### 3-2. 인자 리스트

* 인자가 없을 때:()
* 인자가 한개일 때: (one) 또는 one
* 인자가 여러개 일 때: (one, two)
* 인자의 타입은 생략 가능하다.&#x20;
  * 컴파일러가 추론(infer)할 수 있기 때문!&#x20;
  * 하지만 명시할 수도 있다. (Integer one, Integer two)

### 3-3. 바디

* 화살표 오른쪽에 함수 본문을 정의한다.
* 여러 줄인 경우에 {} 를 사용해서 묶는다.
* 한 줄인 경우에 생략 가능, return도 생략 가능하다.&#x20;

### 3-4. 변수 캡처 (Variable Capture)

* 로컬변수캡처
  * final이거나 effective final 인 경우에만 참조할 수 있다.
  * 그렇지 않을 경우 concurrency 문제가 생길 수 있어서 컴파일가 방지한다.
* effective final&#x20;
  * 자바 8부터 지원하는 기능으로 “사실상" final인 변수.
  * final 키워드 사용하지 않은 변수를 익명 클래스 구현체 또는 람다에서 참조할 수 있다.
* 익명 클래스 구현체와 달리 ‘쉐도윙’하지 않는다.
  * 익명 클래스는 새로 스콥을 만들지만, 람다는 람다를 감싸고 있는 스콥과 같다.

```java
public class LambdaTest {

    @Test
    void variableCapture() {
        int baseNum = 100;

        class NestedInnerClassTest {
            int baseNum = 1;    // 참조하는 변수
            @Test
            void plus10() {
                System.out.println(baseNum + 10);
            }
        }

        Consumer<Integer> consumer = new Consumer<Integer>() {
            int baseNum = 5;    // 참조하는 변수 
            @Override
            public void accept(Integer integer) {
                System.out.println(baseNum + integer);
            }
        };

        // lambda
        Function<Integer, Integer> function = num -> baseNum + num;

        // baseNum++;      //compile error : not final!
    }
}
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>람다는 별도의 스콥으로 설정되지 않는다. 따라서 부모 스콥의 범위와 같기 때문에 같은 이름의 변수를 사용할 수 없다. </p></figcaption></figure>

### 3-5. 참고&#x20;

* [https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html#shadowing](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html#shadowing)
* [https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)



## 4. 메소드 레퍼런스&#x20;

* 만약에 람다가 하는 일이 기존 메소드나 생성자를 호출하는 것뿐이라면, 메소드 레퍼런스를 사용해서 매우 간결하게 표현이 가능하다!&#x20;
* [참고](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)

```java

public class MethodReferenceTest {

    @Test
    void 스태틱_메소드_참조() {
        Function<String, String> function = Greeting::hi;

        assertThat(function.apply("flash")).isEqualTo("hi, flash");
    }

    @Test
    void 특정_객체의_인스턴스_메소드_참조() {
        Greeting greeting = new Greeting();
        Function<String, String> function = greeting::hello;

        assertThat(function.apply("flash")).isEqualTo("hello, flash");
    }

    @Test
    void 임의_객체의_인스턴스_메소드_참조() {
        String[] names = {"kildong", "michle", "doolly"};
        Arrays.sort(names, String::compareToIgnoreCase);

        assertThat(Arrays.toString(names))
                .isEqualTo("[doolly, kildong, michle]");
    }

    @Test
    void 생성자_참조() {
        Function<String , Greeting> greetingFunction = Greeting::new;   //with name
        Supplier<Greeting> greetingSupplier = Greeting::new;        //default

        assertThat(greetingFunction.apply("flash")).isEqualTo(new Greeting("flash"));
        assertThat(greetingSupplier.get()).isEqualTo(new Greeting());

    }
}
```

