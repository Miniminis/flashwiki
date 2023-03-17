# Annotation

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. Annotation 관련한 두 가지 큰 변화&#x20;

### 1-1. 자바 8부터 애노테이션을 타입 선언부에도 사용할 수 있게 되었다.&#x20;

1. 타입 선언부 : 제네릭, 변수, 매개변수, 예외 등등...
2. 타입에 사용할 수 있으려면&#x20;
   1. TYPE\_PARAMETER: 타입 변수에만 사용할 수 있다.
   2. TYPE\_USE: 타입 변수를 포함해서 모든 타입 선언부에 사용할 수 있다.&#x20;

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
public @interface Chicken {
}



@Chicken
public class App {

    public static void main(@Chicken String[] args) throws @Chicken RuntimeException {
        List<@Chicken String> names = List.of("puradak");
    }

    static class Cola<@Chicken T> {
        public  static  <@Chicken C> void print(@Chicken C c) {
            System.out.println(c);
        }
    }
}
```

### 1-2. 자바 8부터 애노테이션을 중복해서 사용할 수 있게 되었다.&#x20;

1. Annotation Container 를 이용한다.&#x20;
   1. 컨테이너 어노테이션은 중복 어노테이션과 @Retention, @Target이 중복어노테이션보다 범위가 같거나 넓어야 한다.

```java

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
@Repeatable(ChickenContainer.class)
public @interface Chicken {

    String value();

}


@Retention(RetentionPolicy.RUNTIME)    //@Retention, @Target이 중복어노테이션보다 범위가 같거나 넓어야 한다. 
@Target(ElementType.TYPE_USE)
public @interface ChickenContainer {

    Chicken[] value();

}



@Chicken("양념")
@Chicken("후라이드")
public class AnnotationTest {

    @Test
    void 컨테이너_어노테이션을_이용하여_중복_어노테이션을_참조한다() {
        ChickenContainer chickenContainer = AnnotationTest.class.getAnnotation(ChickenContainer.class);
        Chicken[] values = chickenContainer.value();

        String[] expected = {"양념", "후라이드"};
        IntStream.range(0, expected.length)
                .forEach(i -> {
                    assertThat(values[i].value()).isEqualTo(expected[i]);
                });
    }

    @Test
    void 어노테이션_타입으로_중복_어노테이션을_참조한다() {
        Chicken[] values = AnnotationTest.class.getAnnotationsByType(Chicken.class);

        String[] expected = {"양념", "후라이드"};
        IntStream.range(0, expected.length)
                .forEach(i -> {
                    assertThat(values[i].value()).isEqualTo(expected[i]);
                });
    }
}
```
