# 순환 알고리즘의 설계

## 조건

1. 적어도 1개의 base case 가 존재해야한다. 즉, 순환되지 않고 종료되는 케이스가 있어야 한다.&#x20;
2. 모든 케이스는 결국 base case 로 수렴해야한다.&#x20;



## 기억하기

* **암시적 매개변수를 명시적 매개변수로 바꾸어라!**&#x20;



## 1. 순차탐색

* 순차 탐색에서의 미션은 0부터 n-1 배열 사이에서 타겟을 찾는 것이다. 하지만 이 검색 구간의 시작 인덱스 0은 보통 생략한다. 즉, 여기서 0이 암시적 매개변수가 된다.&#x20;
* 이렇게 암시적으로 표현된 변수를 아래와 같이 명시화하는 것이 필요하다. 시작 인덱스를 명확하게 매개변수로 전달한다.&#x20;
* 왜 필요할까?&#x20;
  * 맨 처음 재귀함수를 호출할 때에는 0부터 시작하겠지만, 그 다음번부터 호출할 때에는 시작점이 한칸씩 뒤로 밀리게 된다.&#x20;
  * 따라서 이 함수가 한번만 호출될때를 생각하지 말고, 계속 반복되어 호출될 것을 생각하고 매개변수를 디자인 해야한다.&#x20;
  * 일단 매개변수를 명확하게 만들고 나중에 필요 없을 때, 삭제해주는 방법이 좋다.&#x20;

```java
package practice.algorithm.recursion;

public class SearchFromBegin {
    public static void main(String[] args) {
        String s = "elephant";
        char target = 'p';
        System.out.println("search(s, 0, s.length(), target) = " + search(s, 0, s.length()-1, target));;
    }

    private static int search(String s, int begin, int end, char target) {
        if (begin >= end) {
            return -1;
        }

        if (s.charAt(begin) == target) {
            return begin;
        }

        return search(s, begin+1, end, target);
    }

    /**
     * search(s, 0, s.length(), target) = 3
     * */
}
```



### 뒤에서부터 탐색하는 방법

```java
package practice.algorithm.recursion;

public class SearchFromEnd {
    public static void main(String[] args) {
        String s = "elephant";
        char target = 'p';
        System.out.println("search(s, 0, s.length(), target) = " + search(s, 0, s.length()-1, target));
    }

    private static int search(String s, int begin, int end, char target) {
        if (begin >= end) {
            return -1;
        }

        if (s.charAt(end) == target) {
            return end;
        }

        return search(s, begin, end-1, target);
    }

    /**
     * search(s, 0, s.length(), target) = 3
     * */
}
```



### 가운데부터 탐색하는 방법

```java
package practice.algorithm.recursion;

public class SearchFromMiddle {
    public static void main(String[] args) {
        String s = "elephant";
        char target = 't';
        System.out.println("search(s, 0, s.length(), target) = " + search(s, 0, s.length(), target));
    }

    private static int search(String s, int begin, int end, char target) {
        if (begin > end) {
            return -1;
        }

        int middle = (begin + end) / 2;
        if (s.charAt(middle) == target) {
            return middle;
        }

        int index = search(s, begin, middle-1, target);
        if (index != -1) {
            return index;
        }

        return search(s, middle + 1, end, target);
    }

    /**
     * search(s, 0, s.length(), target) = 7
     * */
}
```

&#x20;



## 2. 최대값 찾기

### 처음부터 탐색하는 방법 - 일반적

```java
package practice.algorithm.recursion;

public class SearchMaxFromBegin {
    public static void main(String[] args) {
        int[] arr = new int[]{1, 100, 55, 4, -100};
        System.out.println("findMax(arr, 0, arr.length-1) = " + findMax(arr, 0, arr.length - 1));;
    }

    private static int findMax(int[] arr, int begin, int end) {
        if (begin == end) {
            return arr[begin];
        }

        return Math.max(arr[begin], findMax(arr, begin+1, end));
    }

    /**
     * findMax(arr, 0, arr.length-1) = 100
     * */
}
```



### 가운데부터 탐색하는 방법

```java
package practice.algorithm.recursion;

public class SearchMaxFromMiddle {
    public static void main(String[] args) {
        int[] arr = new int[]{1, 100, 55, 4, -100};
        System.out.println("findMax(arr, 0, arr.length-1) = " + findMax(arr, 0, arr.length - 1));;
    }

    private static int findMax(int[] arr, int begin, int end) {
        if (begin == end) {
            return arr[begin];
        }

        int middle = (begin + end) / 2;

        int max1 = findMax(arr, begin, middle);
        int max2 = findMax(arr, middle+1, end);

        return Math.max(max1, max2);
    }

    /**
     * findMax(arr, 0, arr.length-1) = 100
     * */
}
```



## 3. 이진 탐색

```java
package practice.algorithm.recursion;

public class BinarySearch {
    public static void main(String[] args) {
        String[] strings = new String[]{"a", "v", "e", "p", "w", "b"};
        String target = "w";
        System.out.println("binarySearch(strings, target, 0, strings.length-1) = " + binarySearch(strings, target, 0, strings.length - 1));;
    }

    private static int binarySearch(String[] strings, String target, int begin, int end) {
        int middle = (begin + end) / 2;
        int result = target.compareTo(strings[middle]);
        if (result == 0) {
            return middle;
        }

        if (result < 0) {
            return binarySearch(strings, target, begin, middle-1);
        }

        return binarySearch(strings, target, middle+1, end);
    }
}
```





