# Parallel 정렬

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. Arrays.ParallelSort()

* Fork/Join 프레임워크를 사용해서 배열을 병렬로 정렬하는 기능을 제공한다.&#x20;

## 2. 병렬 정렬 알고리즘&#x20;

기본적으로 자바의 Arrays.sort() 는 싱글 쓰레드에서 동작한다. 따라서 배열의 크기가 커진다면, 아무래도 점점 느려질 수 밖에 없다. 하지만 병렬 정렬 알고리즘은 쓰레드를 여러 개 쓰면서 아래와 같이 동작한다. 동시다발적이기 때문에 수가 크다면 더 유리할수도 있다.&#x20;

* 배열을 둘로 계속 쪼개고&#x20;
* 적절하게 쪼개졌을 때, 합치면서 정렬한다.&#x20;



### 2-1. sort() vs. parallelSort()&#x20;

* 결과 : 3번 이상 돌려본 결과, parallelSort() 가 압도적으로 속도가 빨랐다.&#x20;
* 하지만 알고리즘의 효율성은 같다고 한다.&#x20;
  * 시간 : O(nlogN)
  * 공간 : O(n)&#x20;

```java

public class ParallelSortTest {

    @Test
    void sort와_parallelSort를_비교한다() {
        int size = 2000;
        int[] numbers = new int[size];
        Random random = new Random();
        IntStream.range(0, size).forEach(i -> numbers[i] = random.nextInt());

        //sort
        long start = System.nanoTime();
        Arrays.sort(numbers);
        System.out.println("sort() : " + (System.nanoTime() - start));

        //parallelSort
        IntStream.range(0, size).forEach(i -> numbers[i] = random.nextInt());
        start = System.nanoTime();
        Arrays.sort(numbers);
        System.out.println("parallelSort() : " + (System.nanoTime() - start));

        //result : 3번 이상 돌려본 결과, parallelSort() 가 압도적으로 속도가 빨랐다. 
        //sort() : 795048
        //parallelSort() : 163185

        //sort() : 728581
        //parallelSort() : 137470

        //sort() : 1070314
        //parallelSort() : 142133
    }
}
```
