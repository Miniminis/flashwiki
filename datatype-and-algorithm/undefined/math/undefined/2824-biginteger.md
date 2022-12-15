# 백준 2824 최대공약수, BigInteger

## BigInteger

* 거의 무한대에 가까운 수로, 엄청나게 큰 수를 계산해야할 때 사용할 수 있다.&#x20;
* 아래의 글을 참고하면 연산방법 등 다양한 정보를 얻을 수 있다.&#x20;

{% embed url="https://thsd-stjd.tistory.com/104?category=1007780" %}

{% embed url="https://kkh0977.tistory.com/161" %}

```java
//연산하기
BigInteger num1 = new BigInteger("1234");
BigInteger num2 = new BigInteger("10000000000000000000000000");

System.out.println("num1.add(num2) = " + num1.add(num2));
System.out.println("num2.subtract(num1) = " + num2.subtract(num1));
System.out.println("num1.subtract(num2) = " + num1.subtract(num2));
System.out.println("num1.multiply(num2) = " + num1.multiply(num2));
System.out.println("num1.divide(num2) = " + num1.divide(num2));
System.out.println("num2.divide(num1) = " + num2.divide(num1));
System.out.println("num2.remainer(num1) = " + num2.remainer(num1));  // 나머지
System.out.println("num2.byteValue() = " + num2.byteValue());  // one byte 값


//형변환
BigInteger num3 = BigInteger.valueOf(10000000);

int intNum3 = num3.intValue();
long longNum3 = num3.longValue();
float floatNum3 = num3.floatValue();
double doubleNum3 = num3.doubleValue();
String strNum3 = num3.toString();

System.out.println("intNum3 = " + intNum3);
System.out.println("longNum3 = " + longNum3);
System.out.println("floatNum3 = " + floatNum3);
System.out.println("doubleNum3 = " + doubleNum3);
System.out.println("strNum3 = " + strNum3);


//비교 : 서로 같으면 0, num4 가 더 크면 1, num5가 더 크면 -1
BigInteger num4 = new BigInteger("100001");
BigInteger num5 = new BigInteger("100000");

int compare = num4.compareTo(num5);
System.out.println("compare = " + compare);


//심지어는 최대공약수 구하는 함수도 있다. 
BigInteger num6 = new BigInteger("1278916849368");
BigInteger num7 = new BigInteger("8124682192");
BigInteger gcd = num6.gcd(num7);

```



## Solution 1. BigInteger 이용

* 113808 kb / 816 ms
* BigInteger 값을 이용하면, 수의 범위가 거의 무한대까지 가능하기 때문에 아무리 큰수라도 계산해서 가지고 있을 수 있다.&#x20;
* 게다가 BigInteger 의 경우는 이미 표준 라이브러리로 제공해주는 연산 함수들이 존재하며, 심지어는 최대공약수를 구하는 함수도 포함되어있다.&#x20;
* 문제의 의도가 이 자료형을 써서 하라는 것은 아닌 것 같지만, BigInteger 도 처음 접한 개념인만큼, 우선은 이것을 이용해서 단순하게 유클리드 호제법을 써봤다.&#x20;

```java
package practice.algorithm.math;

import java.math.BigInteger;
import java.util.Scanner;

public class Bj2824_1 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        BigInteger a = new BigInteger("1");
        while ( n > 0 ) {
            a = BigInteger.valueOf(sc.nextInt()).multiply(a);
            n--;
        }

        int m = sc.nextInt();
        BigInteger b = new BigInteger("1");
        while (m > 0) {
            b = BigInteger.valueOf(sc.nextInt()).multiply(b);
            m--;
        }

        if (a.compareTo(b) < 0) {
            BigInteger temp = new BigInteger("0");
            temp = a;
            a = b;
            b = temp;
        }

        String strGcd = gcd(a, b).toString();
        System.out.println(strGcd.length() > 9 ? strGcd.substring(strGcd.length()-9, strGcd.length()) : strGcd);

    }

    private static BigInteger gcd(BigInteger a, BigInteger b) {
        if (b.compareTo(new BigInteger("0")) == 0) return a;
        return gcd(b, a.remainder(b));
    }
}
```

### BigInteger 내장함수를 쓰면 다음과 같이 알고리즘 구현 없이도 가능하다.&#x20;

```java
package practice.algorithm.math;

import java.math.BigInteger;
import java.util.Scanner;

public class Bj2824_1_1 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        int n = sc.nextInt();
        BigInteger a = new BigInteger("1");
        while ( n > 0 ) {
            a = BigInteger.valueOf(sc.nextInt()).multiply(a);
            n--;
        }

        int m = sc.nextInt();
        BigInteger b = new BigInteger("1");
        while (m > 0) {
            b = BigInteger.valueOf(sc.nextInt()).multiply(b);
            m--;
        }

        if (a.compareTo(b) < 0) {
            BigInteger temp = new BigInteger("0");
            temp = a;
            a = b;
            b = temp;
        }

        String strGcd = a.gcd(b).toString();
        System.out.println(strGcd.length() > 9 ? strGcd.substring(strGcd.length()-9, strGcd.length()) : strGcd);
    }
}
```



## Solution 2.&#x20;

* 22632 kb / 328 ms
* 엄청난 분의 블로그를 발견했다. 문제의 의도를 정확히 파악하시고 적절하게 매소드 분리도 잘 되어있어서 해당 코드를 이해하고 혼자서 그대로 쳐보는 연습을 했다. 자세한 내용은 아래의 블로그를 참고! (친절하고 읽기 좋은 코드 감사드립니다 🙏)&#x20;
  * [https://sumin-kim-dev.github.io/boj/boj-gold-2824/](https://sumin-kim-dev.github.io/boj/boj-gold-2824/)
* 서로 다른 두 수 A와 B의 최대 공약수는 결국 두 수를 각각 소인수분해하여 나타낸 값들 중, 가장 작은 것만 택해서 곱한 값이 된다.&#x20;
  * 예를 들어, A = $$2^2$$ \* $$3^3$$, B = $$2^3$$ \* $$3^2$$ 라고 한다면,  gcd(A, B) =  $$2^2$$ \* $$3^2$$ 가 된다.&#x20;
* 따라서 아래의 답안은 입력된 몇 개의 숫자들 각각을 소인수분해하여, Map 에 밑과 지수를 각각 저장하여, 최종적으로는 밑과 가장 작은 지수를 곱한 값을 구할 수 있도록 하였다.&#x20;
* 처음에 이해가 잘 안되었던 부분은 소인수분해 하는 로직 중, 제곱근 이하의 숫자까지만 체크하는 부분이었는데, 곰곰히 생각해보니, 제곱근 이상의 숫자의 경우, 이미 그 이전 숫자에서 체크가 되었을 것이기 때문에, 굳이 체크할 필요가 없다는 것을 이해할 수 있었다.&#x20;

```java
package practice.algorithm.math;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Bj2824_2 {

    static final int MAX = 1000000000;
    static int[] a, b;
    static boolean isGcdBig = false;

    public static void main(String[] args) throws IOException {

        input();
        Map<Integer, Integer> aMap = factorization(a);
        Map<Integer, Integer> bMap = factorization(b);

        long gcd = gcd(aMap, bMap);
        if (isGcdBig) {
            System.out.printf("%09d", gcd);
            return;
        }

        System.out.println(gcd);
    }

    private static long gcd(Map<Integer, Integer> aMap, Map<Integer, Integer> bMap) {
        long gcd = 1;
        for (Integer primeA : aMap.keySet()) {
            int overA = aMap.get(primeA);
            int overB = bMap.getOrDefault(primeA, 0);

            gcd *= pow(primeA, Math.min(overA, overB));
            if (gcd >= MAX) {
                isGcdBig = true;
                gcd %= MAX;
            }
        }

        return gcd;
    }

    private static long pow(Integer primeA, int over) {
        long result = 1;
        while (over --> 0) {
            result *= primeA;
            if (result > MAX) {
                isGcdBig = true;
                result %= MAX;
            }
        }

        return result;
    }

    private static Map<Integer, Integer> factorization(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();

        for (int n : nums) {
            map = factorization(n, map);
        }

        return map;
    }

    private static Map<Integer, Integer> factorization(int n, Map<Integer, Integer> map) {
        if (n == 1) return map;
        for (int i = 2; i * i <= n; i++) {
            if (n % i == 0) {
                map.put(i, map.getOrDefault(i, 0) + 1);
                return factorization(n / i, map);
            }
        }
        map.put(n, map.getOrDefault(n, 0) + 1);
        return map;
    }

    private static void input() throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        a = new int[n];

        StringTokenizer st = new StringTokenizer(br.readLine(), " ");
        for (int i = 0; i < a.length; i++) {
            a[i] = Integer.parseInt(st.nextToken());
        }

        n = Integer.parseInt(br.readLine());
        b = new int[n];
        st = new StringTokenizer(br.readLine(), " ");
        for (int i = 0; i < b.length; i++) {
            b[i] = Integer.parseInt(st.nextToken());
        }
    }

}
```

