# Stream

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. 개념&#x20;

* 데이터를 담고 있는 저장소(컬렉션)이 아니다. 주의할것!&#x20;
* Functional in nature - 스트림이 처리하는 데이터소스를 변경하지 않는다.&#x20;
* 스트림으로 처리하는 데이터는 오직 한번만 처리한다.&#x20;
* 무제한일수도 있다. - short circuit 메소드를 사용해서 제한할 수 있다.&#x20;
* 중개 오퍼레이션은 근본적으로 lazy 하다.&#x20;
  * 중개 오퍼레이션 : 중간에서 이어준다. &#x20;
  * 종료(터미널) 오퍼레이션 : 종료
* 손쉽게 병렬처리를 할 수 있다.&#x20;
  * parallelStream() 이후에 주는 오퍼레이터들은 모두 병렬적으로 처리된다.&#x20;
  * 샛길
    * 병렬처리가 항상 빠른 것은 아니다.&#x20;
    * 데이터가 엄청나게 많은 경우, 유용하게 사용될 수 있다.&#x20;
    * 어찌되었든 병렬처리는 쓰레드를 하나 더 만드는 비용이 들기 때문에 주의해야한다.&#x20;

## 2. stream pipeline&#x20;

* 0 또는 다수의 중개 오퍼레이션 (intermediate operation)과 한 개의 종룔 오퍼레이션(terminal operation) 으로 구성된다.&#x20;
* 스트림의 데이터 소스는 오직 터미널 오퍼레이션을 실행할 때에만 처리한다.&#x20;

## 3. 중개 오퍼레이션&#x20;

* Stream 을 리턴한다.&#x20;
* Stateless / Stateful 오퍼레이션으로 상세하게 구분할 수도 있다.&#x20;
  * 대부분은 stateless&#x20;
  * distinct, sorted 처럼 이전 소스 데이터를 참조해야할 경우 stateful&#x20;
* 예시&#x20;
  * filter, map, limit, skip, sorted ...&#x20;

## 4. 종료 오퍼레이션

* Stream 을 리턴하지 않는다.&#x20;
* 다양한 종류&#x20;
  * collect, allMatch, count, forEach, min, max .. &#x20;

## 5. 참고&#x20;

* [https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)
* [https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)

## 6. 코드로 확인하기&#x20;

### 6-1. 걸러내기&#x20;

* Filter(Predicate)

### 6-2. 변경하기&#x20;

* Map(Function) 또는 FlatMap(Function)
* 예) 각각의 Post 인스턴스에서 String title만 새로운 스트림으로
* 예) List\<Stream\<String>>을 String의 스트림으로

### 6-3. 생성하기&#x20;

* generate(Supplier) 또는 Iterate(T seed, UnaryOperator)
* 예) 10부터 1씩 증가하는 무제한 숫자 스트림
* 예) 랜덤 int 무제한 스트림

### 6-4. 제한하기&#x20;

* limit(long) 또는 skip(long)
* 예) 최대 5개의 요소가 담긴 스트림을 리턴한다.
* 예) 앞에서 3개를 뺀 나머지 스트림을 리턴한다.

### 6-5. 스트림에 있는 데이터가 특정 조건을 만족하는지 확인

* anyMatch(), allMatch(), nonMatch()
* 예) k로 시작하는 문자열이 있는지 확인한다. (true 또는 false를 리턴한다.)
* 예) 스트림에 있는 모든 값이 10보다 작은지 확인한다.

### 6-6. 개수 세기&#x20;

* count()
* 예)10보다 큰 수의 개수를 센다.

### 6-7. 스트림을 데이터 하나로 뭉치기&#x20;

* reduce(identity, BiFunction), collect(), sum(), max()
* 예)모든 숫자 합 구하기
* 예)모든 데이터를 하나의 List 또는 Set에 옮겨담기

```java

public class StreamTest {

    private List<Food> asianFoods;
    private List<Food> westernFoods;

    @BeforeEach
    void init() {
        asianFoods = new ArrayList<>(){{
            add(new Food(1, "비빔밥", true));
            add(new Food(2, "설렁탕", false));
            add(new Food(3, "불닭볶음면", true));
            add(new Food(4, "떡볶이", true));
            add(new Food(5, "궁중떡볶이", false));
            add(new Food(6, "순대국", false));
        }};

        westernFoods = new ArrayList<>(){{
            add(new Food(1, "토마토 파스타", true));
            add(new Food(2, "목살 스테이크", true));
            add(new Food(3, "티본 스테이크", false));
            add(new Food(4, "미국식 짠 피자", false));
        }};
    }

    @Test
    void 떡볶이_메뉴만() {
        List<Food> filtered = asianFoods.stream().filter(s -> s.getName().contains("떡볶이")).collect(Collectors.toList());

        assertThat(filtered).hasSize(2);
        assertThat(filtered).containsAll(
                List.of(new Food(4, "떡볶이", true),
                        new Food(5, "궁중떡볶이", false)));
    }

    @Test
    void 좋아하지_않는_음식만() {
        List<Food> filtered = asianFoods.stream().filter(f -> !f.isLiked()).collect(Collectors.toList());

        assertThat(filtered).hasSize(3);
        assertThat(filtered).containsAll(List.of(
                new Food(2, "설렁탕", false),
                new Food(5, "궁중떡볶이", false),
                new Food(6, "순대국", false)
        ));
    }

    @Test
    void 음식이름만_모아서_스트림_만들기() {
        List<String> foodNames = asianFoods.stream().map(Food::getName).collect(Collectors.toList());

        assertThat(foodNames).hasSize(6);
        assertThat(foodNames).containsAll(List.of("비빔밥", "설렁탕", "불닭볶음면", "떡볶이", "궁중떡볶이", "순대국"));
    }

    @Test
    void 아시아와_서양음식의_모든_이름만_출력하기() {
        List<List<Food>> globalFoods = new ArrayList<>(){{
            add(asianFoods);
            add(westernFoods);
        }};


        // with lambda : globalFoods.stream().flatMap(Collection::stream).map(Food::getName).collect(Collectors.toList());
        List<String> foodNames = globalFoods.stream()
                .flatMap(foods -> foods.stream())
                .map(food -> food.getName())
                .collect(Collectors.toList());

        assertThat(foodNames).isEqualTo(List.of(
                "비빔밥", "설렁탕", "불닭볶음면", "떡볶이", "궁중떡볶이", "순대국", "토마토 파스타", "목살 스테이크", "티본 스테이크", "미국식 짠 피자"
        ));
    }

    @Test
    void _10부터_1씩_증가하는_무제한스트림에서_처음의_10개_제외하고_최대_10개까지만_출력() {
        List<Integer> ints = Stream.iterate(10, i -> i + 1)
                .skip(10)
                .limit(10)
                .collect(Collectors.toList());

        assertThat(ints).isEqualTo(List.of(20, 21, 22, 23, 24, 25, 26, 27, 28, 29));
    }

    @Test
    void 서양음식중에서_피자종류가_있는지_확인() {
        boolean hasPizza = westernFoods.stream()
                .anyMatch(f -> f.getName().contains("피자"));

        assertThat(hasPizza).isTrue();
    }
}
```
