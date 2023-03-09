---
description: 동적계획법
---

# Dynamic Programming

## 개념이해

* Richard Bellman 이 고안한 알고리즘이다.&#x20;
* 복잡한 문제를 간단한 여러개의 문제로 나누어서 푸는 방법을 말한다.&#x20;
* 부분 문제 반복과 최적 부분 구조를 가지고 있는 문제를 일반적인 방법에 비해 적은 시간 안에 풀 때 사용한다.
  * 최적부분구조 : 문제를 풀기 위해서 이전에 했던 계산을 계속 반복해야하는 구조 -> 재귀구조
* 다르게 말하자면 답을 재활용 하는 것이다. 어떤 문제를 풀기 위해 작은 문제를 해결하고 그 답을 큰 문제를 풀기위해 또 다시 활용하는 것이다.&#x20;
* 이전 문제에 대한 답을 다음 문제에서 활용한다고 해서 **기억하면서 풀기**라고도 한다. &#x20;



## 1. 피보나치 수열

피보나치수열은 대표적으로 재귀함수를 이용할 수 있는 문제이다. 하지만 문제를 풀기 위해서 했던 연산을 계속 반복해야한다.&#x20;

* 예를 들면, 피보나치 수열의 7번째 수를 알고 싶다면, f(7) = f(6) + f(5) = f(5)+ f(4) + f(4) + f(3) = ...&#x20;
* 이전에 이미 계산한 결과가 있음에도, 계속 같은 연산을 반복해야하기 때문에 시간이 오래 걸린다. &#x20;

```java
package practice.algorithm.dp;

public class Fibonacci {
    public static void main(String[] args) {
        int n = 7;
        System.out.println(fib(n));
    }

    private static int fib(int n) {
        if (n <=2 ) {
            return 1;
        }

        return fib(n-1) + fib(n-2);
    }

}
```



### 1-1. memoization 으로 caching 하기&#x20;

이를 해결할 수 있는 방법 중 하나는 이전에 계산했던 값을 기억하는 것이다. 배열을 하나 두고, 이전 계산값을 기억하여, 이미 계산한 값이 존재한다면 굳이 다시 계산하지 않도록 로직을 추가하는 것이다.&#x20;

```java
package practice.algorithm.dp;

import java.util.Arrays;

public class FibonacciMemo {

    private static int[] cache;

    public static void main(String[] args) {
        int n = 7;
        cache = new int[n+1];
        Arrays.fill(cache, -1);

        System.out.println(fib(n));
    }

    private static int fib(int n) {
        if (n <=2 ) {
            cache[n] = 1;
            return 1;
        }

        if (cache[n] > -1) {
            return cache[n];
        }

        cache[n] = fib(n-1) + fib(n-2);
        return cache[n];
    }

}
```



### 1-2. bottom-up 방식으로 계산하기

원래 피보나치 수를 구하는 것처럼 bottom-up 방식으로 계산하는 방법이다. 처음부터 차례대로 해를 구해서 이전의 해가 다음 계산에 이용될 수 있도록 한다.&#x20;

```java
package practice.algorithm.dp;

import java.util.Arrays;

public class FibonacciBottomUp {

    private static int[] f;

    public static void main(String[] args) {
        int n = 7;
        f = new int[n+1];

        System.out.println(fib(n));
    }

    private static int fib(int n) {
        f[1] = f[2] = 1;

        for (int i = 3; i <= n; i++) {
            f[i] = f[i-1] + f[i-2];
        }

        return f[n];
    }

}
```

&#x20;

## 2. 이항계수(binomial coefficient) : n개중에서 k개를 뽑는 경우의 수

n개의 수 중에서 k개를 뽑는 경우의 수 역시 DP 를 이용하기 좋은 경우이다. 이 경우, n개 중에서 k개를 뽑는 경우는 아래와 같이 두 가지의 케이스로 나누어서 생각해볼 수 있다.&#x20;

1. n개의 숫자 중에서 a원소를 포함하여 k-1개를 뽑는 경우
2. a를 제외하고 n-1개의 숫자 중에서 k개를 뽑는 경우&#x20;

이 논리로부터 나온 것이 바로 이항계수를 구하는 다음과 같은 식이다.&#x20;

$$n개 중에서 K개를 뽑는 경우의 수 = \frac{n!}{(n-k)!k!}$$



이에 따라 아래와 같이 재귀함수로 구현해볼 수 있지만, 역시 n! 같은 수를 연산하는 것은 자칫하면 정수 범위를 넘어설 수 있고, 또 아래의 k!와 어쩌면 연산 범위가 크게 겹침에도 반복적으로 계산을 해야하기 때문에 매우 비효율적이라는 것을 알 수 있다.&#x20;

```java
package practice.algorithm.dp;

public class Binomial {
    public static void main(String[] args) {
        int n = 6;
        int k = 3;

        System.out.println(bionomial(n, k));
    }

    private static int bionomial(int n, int k) {
        if (n == k || k == 0) {
            return 1;
        }

        return bionomial(n-1, k-1) + bionomial(n-1, k);
    }
}
```

&#x20;

### 2-1. memoization 으로 caching 하기

이 경우, 이전 피보나치 수열과는 다르게 n과 k, 두 개의 수 조합에 대하여 기억을 해야하므로, 2차원 배열을 이용한다.&#x20;

```java
package practice.algorithm.dp;

import java.util.Arrays;

public class BinomialMemo {
    private static int[][] cache;

    public static void main(String[] args) {
        int n = 6;
        int k = 3;
        cache = new int[n+1][k+1];
        initialize();

        System.out.println(bionomial(n, k));
    }

    private static void initialize() {
        for (int i = 0; i < cache.length; i++) {
            Arrays.fill(cache[i], -1);
        }
    }

    private static int bionomial(int n, int k) {
        if (n == k || k == 0) {
            return 1;
        }

        if (cache[n][k] > -1) {
            return cache[n][k];
        }

        cache[n][k] = bionomial(n-1, k-1) + bionomial(n-1, k);
        return cache[n][k];
    }
}
```

&#x20;

### 2-2. bottom-up 방식으로 계산하기

* 이 경우, 어떤 순서로 계산을 해야할지 결정해야한다. 즉, 어디가 bottom 의 기준점이 될 것인지를 알아야 한다.&#x20;
* bottom은 현재 계산의 해가 다음 계산의 재료로 이용되는 것이 목적이다.&#x20;
*   현재 방식의 경우, 아래와 같이, n이 행, k가 열에 존재하고, n은 k보다 같거나 큰 것을 전제로 하므로, 아래와 같이 가로 행을 위에서부터 아래까지 왼쪽에서 오른쪽으로 훑어가는 방식으로 진행할 수 있을 것이다.&#x20;

    <figure><img src="../../../.gitbook/assets/image (6) (7).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4125?tab=curriculum">https://www.inflearn.com/course/알고리즘-강좌/unit/4125?tab=curriculum</a></p></figcaption></figure>

```java
package practice.algorithm.dp;

public class BinomialBottomUp {
    private static int[][] binom;

    public static void main(String[] args) {
        int n = 6;
        int k = 3;
        binom = new int[n+1][k+1];

        System.out.println(bionomial(n, k));
    }

    private static int bionomial(int n, int k) {
        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= k && j <= i; j++) {
                if (i == j || j == 0) {
                    binom[i][j] = 1;
                    continue;
                }

                binom[i][j] = binom[i-1][j-1] + binom[i-1][j];
            }
        }
        return binom[n][k];
    }
}
```

&#x20;



## 정리

* 순환식의 값을 계산하는 기법들이다.&#x20;
* memoization, dynamic programming 모두 동적 계획법의 일종이다.&#x20;
* memoization 은 top-down 방식이며, 실제로 필요한 sub problem 만들어 푼다.&#x20;
* dynamic programming 은 bottom-up 방식이며, recursion 에 수반되는 오버헤드가 없다.&#x20;



## 동적 계획법의 핵심

* 동적계획법은 일반적으로 최적화문제 혹은 카운팅 문제에 적용된다.&#x20;
  * 최적화 문제 : 최소값이나 최대값 문제를 구하는 유형의 문제&#x20;
    * 예) 경로의 최소 길이 등&#x20;
  * 카운팅 : 특정한 조건을 만족하는 해의 개수를 구하는 유형의 문제&#x20;
* 주어진 문제에 대한 순환식을 정의한 뒤, 이 순환식을 memoization 혹은 bottom-up 방식으로 푼다.&#x20;
  * 결국, 동적계획법의 핵심은 순환식! 이 순환식을 먼저 세우고, memoization or bottom-up 방식으로 풀도록 하면 된다.&#x20;
* subproblem 들을 풀어서 원래 문제를 푸는 방식. 그런 의미에서 **분할정복법**과 공통성이 있다고 할 수 있다.&#x20;
  * 예를 들면, 행렬경로문제에서 L(i, j) 를 i, j 까지의 최단경로라고 했을 때, L(i-1, j) 와 L(i, j-1) 까지의 최단경로를 비교해서 풀었던 것처럼 하나의 거대한 문제를 subproblem 으로 나누어 풀면서 결국 원래 문제에 대한 답을 구하는 것이다.&#x20;
  * 피보나치 수열에서 f(n) = f(n-1) + f(n-2) 방식으로 푸는 것 역시 마찬가지이다. n까지의 피보나치 수열을 이전 두 단계의 수를 통해 구하는 방식인 것이다.&#x20;
* 분할정복법에서는 분할된 문제들이 서로 disjoint 하다.&#x20;
  * quicksort 의 경우, pivot 아이템을 하나 정하고 그보다 작은 그룹과 큰 그룹을 나누어 각각 정렬한 뒤, 더한다는 개념
  * 두 개의 subproblem 을 해결함으로써, 원래의 문제를 해결한다.&#x20;
  * 이때, 두 개의 subproblem 은 서로 disjoint 하고 서로 관련이 전혀 없다.&#x20;
* 하지만 동적 계획법에서는 그렇지 않다.&#x20;
  * 행렬 경로 문제를 예로 들면, 마지막 (n, n) 까지의 최단 경로를 구하기 위해서 (n-1, n), (n, n-1) 까지의 최단 경로를 먼저 계산한 뒤, 그로부터 원래 문제인 (n,n) 까지의 최단 경로를 구했었다.&#x20;
  * 이때, 각각의 subproblem 들은  서로 disjoint 하지 않았다.&#x20;
  * 즉, 서로 overlapping 하는 subproblem 들을 해결함으로써, 원래 문제를 해결한다.&#x20;

### Optimal Substructure&#x20;

* 어떤 문제의 최적해가 그것의 subproblem 들의 최적해로부터 효율적으로 구해질 수 있을 때, 그 문제는 optimal substructure 를 가진다고 말한다.&#x20;
* 분할정복법, 탐욕적 기법, 동적계획법은 모두 문제가 가진 이런 특성을 이용한다.&#x20;

### 그렇다면 도대체 순환식은 어떻게 세워야하는가&#x20;

* 최적해의 일부분이 그 부분에 대한 최적해인가를 고려해보면 된다.&#x20;
* 최단경로 문제의 예를 보면, s에서 u까지 가는 최단경로를 구한다면, u와 인접한 v라는 지점까지 s \~ v 가 최단경로가 되어야 s에서 u까지의 최단경로임이 보장된다. 따라서 s에서 u까지의 최단경로를 구하기 위해서는 s에서 v까지의 최단경로를 구해야하는 것이다 (subproblem)&#x20;
* 이 개념을 이용해서 아래와 같이 순환식을 세워볼 수 있다. &#x20;
  *

      <figure><img src="../../../.gitbook/assets/image (38) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 당연한거 아닌가요

어떻게 보면, 1에서 10까지 가는데 10과 인접한 9까지의 최단 경로를 구하면, 1에서 10까지의 최단경로를 구할 수 있다는 말은 너무 당연하게 들린다. 하지만 조금만 더 생각해보면 그렇지 않다는 것을 알 수 있다.&#x20;

<figure><img src="../../../.gitbook/assets/image (15) (3).png" alt=""><figcaption></figcaption></figure>

예를 들면, 최장경로를 구하는 문제를 생각해보자. 1에서 4까지 가는데 최장경로는 1 - 2- 3- 4 일것이다. 이것에는 의심의 여지가 없다.&#x20;

그렇다면, 1에서 4까지 가는 최장 경로는 1에서 3까지 가는 최장경로를 통해 구할 수 있는가? 이건 그렇지 않다. 1에서 3까지가는 최장경로는 1 - 4- 2- 3 이기 때문이다. 이 경로는 1에서 4까지가는 경로의 sub 해가 될 수 없다.&#x20;

_물론 문제의 조건에서 특정 노드 집합 A와 목적지인 v를 지나지 않고 s부터 v까지 가는 최장경로를 구하라고 한다면, 이 최장경로 문제 역시 DP 로 충분히 풀어낼 수 있다. 즉, 좀 더 복잡한 구조의 optimal substructure 를 가진다고 말할 수 있는 것이지, optimal substructure 자체를 가지지 않는다고 말할 수는 없다는 것이다._&#x20;







