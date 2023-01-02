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
* 이전 문제에 대한 답을 다음 문제에서 활용한다고 해서 **기억하면서 풀기**라고도 한다.&#x20;



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

    <figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4125?tab=curriculum">https://www.inflearn.com/course/알고리즘-강좌/unit/4125?tab=curriculum</a></p></figcaption></figure>

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
* memoization 은 top-down 방식이며, 실제로 필요한 sub problem 만들 푼다.&#x20;
* dynamic programming 은 bottom-up 방식이며, recursion 에 수반되는 오버헤드가 없다.&#x20;







&#x20;&#x20;



