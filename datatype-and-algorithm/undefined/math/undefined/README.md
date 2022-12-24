# 순열과 조합

## 순열

* 서로 다른 n개의 원소에서 r개를 중복없이 순서에 상관있게 선택하는 혹은 나열하는 것을 순열(permutation) 이라고 한다.
* 중복순열 : 중복 순열은 순열과 마찬가지로 n개 중에 r개를 순서에 상관 있게 뽑는데, **중복을 허락하여** 뽑는 것을 말한다.
* 참고
  * [https://bcp0109.tistory.com/14](https://bcp0109.tistory.com/14)

<figure><img src="../../../../.gitbook/assets/image (6) (5).png" alt=""><figcaption><p>출처) <a href="https://bcp0109.tistory.com/14">https://bcp0109.tistory.com/14</a></p></figcaption></figure>

```java
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

## 조합

* 서로 다른 n개의 원소에서 r개를 중복없이 순서에 상관없이 뽑는 것을 말한다.&#x20;
* 중복을 허용하면 중복조합이라고 한다.&#x20;





