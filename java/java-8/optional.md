# Optional

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. 필요성&#x20;

* 개발자인 우리는 종종 null 체크를 깜빡하게 된다. 그래서 NPE 를 만들어내기도 한다.&#x20;
* java8부터는 이러한 부분을 보완하기 위해 Optional 이라는 타입이 추가되었다.&#x20;



## 2. 적절한 값이 없을 경우, 리턴 방법들 &#x20;

메소드 작업 중에 값을 제대로 리턴하기 힘든 경우, 개발자가 선택할 수 있는 방법은 대략 다음과 같았다.&#x20;

1. null 을 리턴한다.&#x20;
   1. 비용문제는 없지만, 코드를 사용하는 클라이언트에게 null 처리 부담을 전가한다.&#x20;
2. 예외를 던진다.&#x20;
   1. 스택트레이스를 찍게 되므로 비용이 비싸다.&#x20;
3. java 8 부터는 Optional 을 리턴한다.&#x20;
   1. 값이 있을수도 있고, 없을 수도 있음을 명시적으로 알려준다.&#x20;
   2. 빈 값에 대한 처리를 강제한다.&#x20;



## 3. Optional&#x20;

* 오직 값 한개가 들어있을수도 없을수도 있는 "컨테이너"



## 4. 주의할 것&#x20;

### 4-1. 리턴 값으로만 쓰기를 권장한다.&#x20;

* 매소드의 매개변수타입, 맵의 키 타입, 인스턴스 필드 타입으로 쓰지 않는다.&#x20;
  * 매개변수타입 : null 값에 대한 연산이 발생할 가능성이 있기 때문에 오히려 더 번거롭고 위험해진다.
  * 맵의 키 타입 : 맵 인터페이스의 가장 중요한 것은 키는 null 이 아니라는 것이다. 이것에 optional type 이라는 것은 안된다.
  * 인스턴스 필드 타입 : 클래스를 쪼개던지, 다른 방법을 이용한다.&#x20;

```java
public class Food {

    private int id;

    private String name;

    private boolean isLiked;

    private String bestRestaurantName;
    
    // return type 으로만 사용한다. 
    public Optional<String> getBestRestaurantName() {
        return Optional.ofNullable(bestRestaurantName);
    }
}

// 안티 패턴
public class Food {

    private int id;

    private String name;

    private boolean isLiked;

    // 3. 인스턴스 필드 타입으로 쓰지 않는다. 
    private Optional<String> bestRestaurantName;
    
    public void setBestRestaurantName(Optional<String> bestRestaurantName) {
        // null 에 대해서 처리할 때 NPE 발생 우려
        // 오히려 더 복잡해진다.  
        if (baseRestaurantName == null) {
            ...            
        }
    }
}
```

### 4-2. Optional 을 리턴하는 메소드에서 null 을 리턴하지 말자.&#x20;

* 차라리 Optional.empty() 를 이용할 것&#x20;

```java

public class Food {
    private int id;
    
    private String name;
    
    private boolean isLiked;
    
    private String bestRestaurantName;
    

    public Optional<String> getBestRestaurantName() {
        // return null; // 금지!
        return Optional.empty();
    }
}

```

### 4-3. Primitive type 을 위한 Optional 이 별도로 존재한다.&#x20;

* OptionalInt, OptionalLong ...&#x20;
* 박싱/언박싱 비용을 줄일 수 있다.&#x20;

```java
@Test
void primitiveTypeOptionalTest() {
    OptionalInt optionalInt = OptionalInt.of(10);
    optionalInt.ifPresent(System.out::println);

    OptionalLong optionalLong = OptionalLong.of(10);
    if (optionalLong.isPresent()) {
        assertThat(optionalLong.getAsLong()).isEqualTo(10);
    }
}
```

### 4-4. Collection, Map, Stream Array, Optional 등 이미 컨테이너 타입으로 존재하며 비어있는지 알 수 있는 타입들은 Optinal 로 굳이 감싸지 않는다.

* 컨테이너로 두 번 감싸는 꼴이다.&#x20;

```java
@Test
void optional이_필요없을때_두번감싸지_말자() {
    Optional<Optional<String>>  opt = Optional.ofNullable(null);    // not recommended
    Optional<String> s = opt.get(); //NPE 발생
    String s1 = s.get();

    Optional<List<Food>> optionalFoods = Optional.of(asianFoods);   // not recommended
    asianFoods.isEmpty();                                           // recommended
}
```

## 5. 참고

* [https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)
* [https://www.oracle.com/technical-resources/articles/java/java8-optional.html](https://www.oracle.com/technical-resources/articles/java/java8-optional.html)
* **이팩티브 자바 3판, 아이템 55 적절한 경우 Optional을 리턴하라.**

## 6. Optional API Practice &#x20;

```java
public class OptionalApiTest {

    private List<Food> asianFoods = new ArrayList<>(){{
        add(new Food(1, "비빔밥", true));
        add(new Food(2, "설렁탕", false));
        add(new Food(3, "불닭볶음면", true));
        add(new Food(4, "떡볶이", true));
        add(new Food(5, "궁중떡볶이", false));
        add(new Food(6, "순대국", false));
    }};


    @Test
    void optionalOf를이용하여_Optional을_만들수있다() {
        Optional<Food> optionalOf = Optional.of(new Food(1, "라면", true));
        Optional<Food> optionalNullable = Optional.ofNullable(null);
        Optional<Food> optionalEmpty = Optional.empty();
    }

    @Test
    void optionalIsPresent를_이용하여_값의_존재여부를_확인한다() {
        Optional<Food> optionalOf = Optional.of(new Food(1, "라면", true));
        Optional<Food> optionalNullable = Optional.ofNullable(null);
        Optional<Food> optionalEmpty = Optional.empty();

        assertThat(optionalOf.isPresent()).isTrue();
        assertThat(optionalNullable.isPresent()).isFalse();
        assertThat(optionalEmpty.isEmpty()).isTrue();
    }

    @Test
    void optionalGet을_이용하여_값을_꺼낸다() {
        Optional<Food> optionalRamen = Optional.of(new Food(1, "라면", true));
        if (optionalRamen.isPresent()) {
            Food ramen = optionalRamen.get();
            assertThat(ramen).isEqualTo(new Food(1, "라면", true));
        }
    }

    @Test
    void null값을_Get하려고하면_예외가_발생한다() {
        Optional<Food> optionalNullable = Optional.ofNullable(null);
        assertThatThrownBy(() -> optionalNullable.get())
                .isInstanceOf(NoSuchElementException.class);
    }

    @Test
    void empty값을_Get하려고하면_예외가_발생한다() {
        Optional<Food> optionalEmpty = Optional.empty();
        assertThatThrownBy(() -> optionalEmpty.get())
                .isInstanceOf(NoSuchElementException.class);
    }

    @Test
    void ifPresentConsumer로_반환값_없이_행위를_수행한다() {
        Optional<Food> optionalRamen = Optional.of(new Food(1, "라면", true));
        optionalRamen.ifPresent(f -> System.out.println(f.getName()));
        //라면
    }

    @Test
    void orElse는_무조건_실행되므로_이미_반환할_값이_있을떄_사용하면_좋다() {
        Optional<Food> optionalEmpty = Optional.empty();
        Food food = optionalEmpty.orElse(createNewFood());
        assertThat(food).isEqualTo(new Food(1, "라면", true));

        System.out.println("=====");

        Optional<Food> optionalOf = Optional.of(new Food(1, "라면", true));
        Food food2 = optionalOf.orElse(createNewFood());
        assertThat(food2).isEqualTo(new Food(1, "라면", true));

        //new food created
        //=====
        //new food created
    }

    @Test
    void orElseGet은_없을때만_실행되므로_새롭게_만들어줄떄_사용하면_좋다() {
        Optional<Food> optionalEmpty = Optional.empty();
        Food food = optionalEmpty.orElseGet(() -> createNewFood()); //supplier
        assertThat(food).isEqualTo(new Food(1, "라면", true));

        System.out.println("=====");

        Optional<Food> optionalOf = Optional.of(new Food(1, "라면", true));
        Food food2 = optionalOf.orElseGet(OptionalApiTest::createNewFood);  //supplier
        assertThat(food2).isEqualTo(new Food(1, "라면", true));

        //new food created
        //=====
    }

    @Test
    void orElseThrow는_없는경우_에러를_던진다() {
        Optional<Food> optionalEmpty = Optional.empty();
        assertThatThrownBy(() -> optionalEmpty.orElseThrow(() -> new NullPointerException()))
                .isInstanceOf(NullPointerException.class);

        Optional<Food> optionalEmpty2 = Optional.empty();
        assertThatThrownBy(() -> optionalEmpty2.orElseThrow(NullPointerException::new))
                .isInstanceOf(NullPointerException.class);
    }

    @Test
    void filter를_이용하여_optional_값을_걸러낼수있다() {
        Optional<Food> first = asianFoods.stream()
                .filter(f -> f.getId() > 2)
                .findFirst();

        Food food = first.filter(f -> f.getId() == 3).orElseThrow();
        assertThat(food.getName()).isEqualTo("불닭볶음면");
    }

    @Test
    void flatMap으로_Optional에_들어있는값을_변환할수있다() {
        Optional<Food> first = asianFoods.stream()
                .filter(f -> f.getId() > 2)
                .findFirst();

        Optional<String> filtered = first.map(f -> f.getBestRestaurantName()).orElse(Optional.empty());
        assertThat(filtered).isEmpty();

        Optional<String> filtered2 = first.flatMap(f -> f.getBestRestaurantName());
        assertThat(filtered2).isEmpty();
    }


    private static Food createNewFood() {
        System.out.println("new food created");
        return new Food(1, "라면", true);
    }
}
```
