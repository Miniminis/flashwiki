# interface의 default 메소드와 static 메소드

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

자바 8버전부터 추가된 기본 메소드로 인한 API 의 변화를 살펴보겠다.&#x20;

## 1. Iterable 의 기본메소드&#x20;

* forEach()
* spliterator()

```java
@Test
void forEachTest() {
    List<String> names = List.of("flash", "doolly", "kildong", "ttochi");
    names.forEach(System.out::println);
}

@Test
void spliterator() {
    List<String> names = List.of("flash", "doolly", "kildong", "ttochi");
    Spliterator<String> spliterator = names.spliterator();
    Spliterator<String> spliterator2 = spliterator.trySplit();
    while (spliterator.tryAdvance(System.out::println));
    System.out.println("==================");
    while (spliterator2.tryAdvance(System.out::println));
    /**
     * kildong
     * ttochi
     * ==================
     * flash
     * doolly
     * */
}
```

## 2. Collection 의 기본메소드&#x20;

* stream() / parallelStream()
* removeIf(Predicate)
* spliterator()

```java
@Test
void streamTest() {
    List<String> names = List.of("flash", "doolly", "kildong", "ttochi");
    long count = names.stream()
            .filter(n -> n.contains("l"))
            .count();
    assertThat(count).isEqualTo(3);

    Set<String> set = names.stream()
            .filter(n -> n.contains("l"))
            .collect(Collectors.toSet());
    assertThat(set).isEqualTo(Set.of("flash", "doolly", "kildong"));
}

@Test
void removeIfTest() {
    List<String> names = new ArrayList<>() {{
        add("flash");
        add("doolly");
        add("kildong");
        add("ttochi");
    }};
    names.removeIf(n -> n.startsWith("f"));

    assertThat(names).doesNotContain("flash");
}
```

## 3. Comparator 의 기본메소드 및 스태틱 메소드&#x20;

* reversed()
* thenComparing()
* static reverseOrder() / naturalOrder()
* static nullsFirst() / nullsLast()
* static comparing()

```java
@Test
void reversedSort() {
    List<String> names = new ArrayList<>() {{
        add("flash");
        add("doolly");
        add("kildong");
        add("ttochi");
    }};
    Comparator<String> compareToIgnoreCase = String::compareToIgnoreCase;
    names.sort(compareToIgnoreCase.reversed());
    // names.sort(Comparator.reverseOrder());

    names.forEach(System.out::println);
    /**
     * ttochi
     * kildong
     * flash
     * doolly
     * */
}
```

## 4. interface 로 인한 구조의 변화&#x20;

<figure><img src="../../.gitbook/assets/image (3) (9).png" alt=""><figcaption><p>인프런 백기선 님의 &#x3C;<a href="https://www.inflearn.com/course/the-java-java8/dashboard">더 자바, Java8</a>></p></figcaption></figure>

* 자바 8버전 이전까지는 왼쪽과 같은 구조로 설계를 하는 경우가 많았다.
  * 인터페이스에 메소드 a, b, c가 존재하고&#x20;
  * 그 메소드들 모두를 구현한 추상 클래스를 중간에 둔 뒤&#x20;
  * 해당 추상 클래스를 직접 상속받아 구현하는 방식으로 원하는 메소드만 선택적으로 사용했었다.  &#x20;
* 하지만 자바 8 버전 이후, 인터페이스에서 default method, static method 를 지원하게 되면서, 오른쪽과 같은 구조로 변경되어갔다.&#x20;
* 여기서 중요한 점은 상속과 구현이다. 상속과 구현은 천지차이인데, &#x20;
  * 왼쪽 구조처럼 추상클래스를 상속받는 구조라면, 해당 클래스는 이제 더이상 다른 클래스로부터 상속 받지 못한다.&#x20;
  * 하지만 오른쪽 구조처럼 인터페이스를 구현한 형태는 얼마든지 상속에 확장성이 있다.&#x20;
* 이처럼 상속을 강제하지 않는 구조를 비침투적 설계라고 하며, 자바와 스프링에서 매우 좋아하는 방법이다.&#x20;
  * 실제로 Spring 의 WebMvcConfigurerAdapter 라는 추상클래스는 스프링 5버전부터는 Deprecated 되었는데, 그 이유는 원래 인터페이스인 WebMvcConfigurer 에서 default 메소드로 이미 원하는 메소드만 구현하여 사용할 수 있도록 모든 것을 지원해주고 있기 때문이다.&#x20;

<figure><img src="../../.gitbook/assets/image (6) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (4) (2).png" alt=""><figcaption></figcaption></figure>

## 5. 참고&#x20;

* https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html
* https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html
* https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html
* https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html

