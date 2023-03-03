# 순열과 조합

## 1. 순열

* 서로 다른 n개의 원소에서 r개를 중복없이 순서에 상관있게 선택하는 혹은 나열하는 것을 순열(permutation) 이라고 한다.
* 중복을 허용하면 중복 순열이라고 한다.&#x20;

<figure><img src="../../../../.gitbook/assets/image (6) (5) (1).png" alt=""><figcaption><p>출처) <a href="https://bcp0109.tistory.com/14">https://bcp0109.tistory.com/14</a></p></figcaption></figure>

```java
//4개의 원소 중에서 2개를 뽑는 순열 
package practice.algorithm.math;

public class Permutation {
    public static void main(String[] args) {
        int[] input = {1, 2, 3, 4};
        int r = 2;

        int[] output = new int[r];
        boolean[] visited = new boolean[input.length];

        permutation(input, visited, output, 0, input.length, r);

    }

    private static void permutation(int[] input, boolean[] visited, int[] output, int depth, int inputLength, int r) {
        if (depth == r) {
            printOutput(output, r);
            return;
        }

        for (int i = 0; i < inputLength; i++) {
            if (!visited[i]) {
                visited[i] = true;
                output[depth] = input[i];
                permutation(input, visited, output, depth+1, inputLength, r);
                visited[i] = false;
            }
        }
    }

    private static void printOutput(int[] output, int r) {
        for (int num : output) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
}
```

### 1-1. 중복순열

* 중복을 제거하기 위한 visited 배열 제외 &#x20;

```java
package practice.algorithm.math;

public class PermutationRepeated {

    private static int[] input;
    private static int[] output;
    private static int r;

    public static void main(String[] args) {
        input = new int[]{1, 2, 3, 4};

        r = 2;
        output = new int[r];

        permutation(0);
    }

    private static void permutation(int depth) {
        if (depth == r) {
            printOutput(output);
            return;
        }

        for (int i = 0; i < input.length; i++) {
            output[depth] = input[i];
            permutation(depth+1);
        }
    }

    private static void printOutput(int[] output) {
        for (int num : output) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
}
```



## 2. 조합

* 서로 다른 n개의 원소에서 r개를 중복없이 순서에 상관없이 뽑는 것을 말한다.&#x20;
* 중복을 허용하면 중복조합이라고 한다.&#x20;

```java
package practice.algorithm.math;

import java.util.Arrays;

public class Combination {

    private static int[] input;
    private static int[] output;
    private static int r;

    public static void main(String[] args) {
        input = new int[]{1, 2, 3, 4};
        r = 2;
        output = new int[r];

        combination(0, 0);
    }

    private static void combination(int depth, int start) {
        if (depth == r) {
            System.out.println(Arrays.toString(output));
            return;
        }

        for (int i = start; i < input.length; i++) {
            output[depth] = input[i];
            combination(depth+1, i+1);    //오름차순으로 구하기 때문에 중복체크를 안해도 된다. 
        }
    }
}
```

### 2-1. 중복조합&#x20;

```java
package practice.algorithm.math;

import java.util.Arrays;

public class CombinationRepeated {

    private static int[] input;
    private static int[] output;
    private static int r;

    public static void main(String[] args) {
        input = new int[]{1, 2, 3, 4};
        r = 2;
        output = new int[r];

        combination(0, 0);
    }

    private static void combination(int depth, int start) {
        if (depth == r) {
            System.out.println(Arrays.toString(output));
            return;
        }

        for (int i = start; i < input.length; i++) {
            output[depth] = input[i];
            combination(depth+1, i);    //중복을 허용하므로, 같은 수 반복
        }
    }
}
```



## 3. 참고 &#x20;

* [https://bcp0109.tistory.com/14](https://bcp0109.tistory.com/14)
* [https://velog.io/@yul\_00/AlgorithmJava-순열과-조합](https://velog.io/@yul\_00/AlgorithmJava-%EC%88%9C%EC%97%B4%EA%B3%BC-%EC%A1%B0%ED%95%A9)
