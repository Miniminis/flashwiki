---
description: 유클리드 호제법
---

# 최대공약수, 최소공배수

> 최대공약수를 구하는 다양한 알고리즘이 있지만, 유클리드 호제법이 가장 일반적이고 효율적이다.&#x20;

## 개념정리 - 최대공약수, 최소공배수&#x20;

* 유튜브에 수학채널 쑤튜브라는 친절한 유튜브 채널을 발견했다. 아래의 링크에서 최대공약수와 최소공배수의 개념을 일단 복습하자.&#x20;

{% embed url="https://youtube.com/watch?v=Obs-HC5j5bI&feature=shares" %}

<figure><img src="../../../../.gitbook/assets/image (5) (1) (3).png" alt=""><figcaption><p>수학채널 쑤튜브 </p></figcaption></figure>

* 결국, 최대공약수는 두 수의 약수들 중 공통적으로 가지고 있는 약수 중 최대값을 의미한다.&#x20;
  * 다른 방식으로 말하자면, 두 수가 각각 나눗셈을 했을 때, 공통으로 나누어 떨어지는 수 중에 가장 큰 수
* 최소공배수는 두 수에 공통으로 존재하는 배수 중 가장 작은 수를 의미한다.&#x20;
  * 두 수 A, B에 대하여 A \* B / (A와 B의 최대공약수)&#x20;
  * A = ga, B=gb, 최소공배수 = g\*a\*b 가 되는데, A\*B = g\*g\*a\*b 이므로 최대공약수인 g를 한번 나누어주는 것이다.&#x20;



## 유클리드 호제법

> 출처 : 위키피디아&#x20;

* 2개의 자연수(또는 정식) a, b에 대해서 a를 b로 나눈 나머지를 r이라 하면(단, a>b), a와 b의 최대공약수는 b와 r의 최대공약수와 같다.&#x20;
* 이 방법을 이용하여 이 성질에 따라, b를 r로 나눈 나머지 r'를 구하고, 다시 r을 r'로 나눈 나머지를 구하는 과정을 반복하여 나머지가 0이 되었을 때 나누는 수가 a와 b의 최대공약수이다.



## 최대공약수 구하기 1. 유클리드 호제법 + 재귀

```java
package practice.algorithm.math;

public class GDCRecursion {
    public static void main(String[] args) {

        int a = 24;
        int b = 28;

        //유클리드 호제법은 a > b 를 충족해야한다.
        if (a < b) {
            int temp = a;
            a = b;
            b = temp;
        }

        System.out.println(gdc(a, b));
    }

    private static int gdc(int a, int b) {
        if (b == 0) return a;
        return gdc(b, a % b);
    }
}
```

## 최대공약수 구하기 2. 유클리드 호제법 + 반복문

```java
package practice.algorithm.math;

public class GDCLoop {
    public static void main(String[] args) {

        int a = 24;
        int b = 28;

        //유클리드 호제법은 a > b 를 충족해야한다.
        if (a < b) {
            int temp = a;
            a = b;
            b = temp;
        }

        System.out.println(gdc(a, b));
    }

    private static int gdc(int a, int b) {
        while (b != 0) {
            int r = a % b;
            a = b;
            b = r;
        }

        return a;   //b == 0 이다.
    }
}
```

## 최소공배수 구하기 &#x20;

```java
package practice.algorithm.math;

public class LCM {
    public static void main(String[] args) {

        int a = 8;
        int b = 10;

        //유클리드 호제법은 a > b 를 충족해야한다.
        if (a < b) {
            int temp = a;
            a = b;
            b = temp;
        }

        System.out.println(lcm(a, b));
    }

    private static int lcm(int a, int b) {
        return a * b / gdc(a, b);
    }

    private static int gdc(int a, int b) {
        if (b == 0) return a;
        return gdc(b, a % b);
    }


}
```

