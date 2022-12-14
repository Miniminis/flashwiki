# 소수찾기, 에라토스테네스의 체

## 소수를 찾는 3가지 방법

1. 기본 판별법 : O(N²)
2. 제곱근을 이용한 기본 판별법 : O(N√N)
3. 에라토스테네스의 체 : O(N ㏒ (㏒ N))

> 따라서 에라토스테네스의 체가 가장 효율적이고 일반적인 알고리즘이라고 할 수 있다. \
> [참고](https://st-lab.tistory.com/80)



## N이 소수인지 아닌지 판단하기

* 2부터 n-1까지 순회하며 n 의 약수가 있는지 확인하는 것
* 약수가 만약 하나도 없다면 n 은 소수가 된다.
* 합성수 n = p \* q 로 표현될 떄, 한 수는 항상 n 의 1/2 이하, 다른 한 수는 항상 n 의 1/2 이상이라는 점을 기억하여, n-1까지 순화히지 않고, n의 1/2 까지만 순회하도록 했다.

```java

public class Main {

    public static void main(String[] args) {

        int n = 53;
        for (int i = 2; i*i <= n; i++) {
            if (n % i != 0) {
                System.out.println(false);
            }
        }

        System.out.println(true);
    }
}

```



## 소수 구하기 - 에라토스테니스의 체

* 에라토스테네스의 체 방법
  * 2부터 소수를 구하고자 하는 구간의 모든 수를 나열한다.
  * 소수가 되는 수의 배수를 지우면 남는 것은 소수만 된다.
  * 우선 2는 제외한 2의 배수를 모두 지운다.
  * 다음수인 3도 자기자신을 제외한 3의 배수를 모두 지운다.
  * 4는 이미 2의 배수로 지워졌다.
  * 그 다음수인 5 역시 자기 자신인 5를 제외한 5의 배수를 지운다.
  * 위의 과정을 반복한다.
  * 만약에 n=120 이라면 11\*11 = 121 이므로 11보다 작은 배수들만 지워도 충분히 구할 수 있다.
* 문제유형
  * 특정 범위의 두 수 n, m 사이에 있는 수들 중 소수를 모두 찾으시오
* 특징
  * 소수 구하는 다양한 방법 중에서 가장 대중적이면서 효율이 좋다고 한다.

```java
package practice.algorithm;

import java.util.Arrays;

public class Main {

    public static void main(String[] args) {

        int n = 120;
        boolean[] isPrime = new boolean[n+1];   //0 부터 n 까지의 수에 대한 상태를 저장
        Arrays.fill(isPrime, true);

        isPrime[0] = isPrime[1] = false;
        for (int i = 2; i*i <= n; i++) {
            if (isPrime[i]) {
                for (int j = i*i; j <= n; j += i) {
                    isPrime[j] = false;
                }
            }
        }

        for (int i = 1; i <= n; i++) {
            if (isPrime[i]) System.out.print(i + " ");
            //2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97 101 103 107 109 113 
        }

    }
}
```



## 소인수 구하기

* 소인수 : 곱하여 특정 수가 되는 것들 중, 소수인 것
  * 예를 들면 10의 소인수를 구할 때, 10의 인수 1, 2, 5, 10 중에서 소수인 2, 5를 소인수라고 한다.
* 한 합성수를 소수들의 곱으로 표현하는 방법을 찾는 소인수 분해
* 2부터 시작해 n 이 소인수가 될 수 있는 수들을 하나하나 순회하면서, n 의 약수를 찾을 떄마다 n 을 이 숫자로 나눈다.

```java
package practice.algorithm;

import java.util.ArrayList;
import java.util.List;

public class Main {

    public static void main(String[] args) {

        int n = 120;
        List<Integer> list = new ArrayList<>();

        for (int i = 2; i*i <= n; i++) {
            while (n%i == 0) {
                list.add(n);
                n /= i;
            }
        }

        if (n>1) {
            list.add(n);
        }

        for (int num : list) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
}

```
