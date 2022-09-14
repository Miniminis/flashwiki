# recursion 1

### Recursion 순환

* 보통의 경우, recursion 은 무한루프를 돈다.

```java
package practice.algorithm;

public class Main {

    public static void main(String[] args) {
        func();
    }

    public static void func() {
        System.out.println("Hello ...");
        func();
    }

}

/*
result
Hello ...
Hello ...
Hello ...
Hello ...
...
*/
```

### 하지만, recursion 이 항상 무한루프에 빠지는 것은 아니다.

두 가지의 조건을 충족한다면, recursion 은 무한루프에 빠지지 않는다.

* base case : 적어도 하나의 recursion에 빠지지 않는 경우가 존재해야한다.
* recursive case : recursion 을 반복하다보면 결국 base case 로 수렴해야한다.

```java
package practice.algorithm;

public class Main {

    public static void main(String[] args) {
        int a = 4;
        func(a);
    }

    public static void func(int a) {
        if (a <= 0) {     //base case
            return;
        }

        System.out.println("Hello ...");
        func(a-1);        //recursive case : 결국 0으로 수렴한다. 
    }

}

/*
result
Hello ...
Hello ...
Hello ...
Hello ...

Process finished with exit code 0
*/
```

### recursion 을 이용하여 1부터 N까지의 합을 구하기

```java
package practice.algorithm;

public class Main {

    public static void main(String[] args) {
        int result = sum(4);
        System.out.println("result = " + result);
    }

    public static int sum(int n) {
        if (n == 0) {
            return 0;
        }

        return n + sum(n-1);
    }

}

//result = 10
```

#### 수학적 귀납법으로 위의 코드를 증명하기

* 정리 : func(int n)은 음이 아닌 정수 n에 대해서 0에서 n까지의 합을 올바로 계산한다.
* 증명
  1. n=0인 경우, 0을 반환한다. → 올바르다.
  2. 임의의 양수 k에 대하여 n\<k 라고 할 때, 0에서 n까지의 합을 올바르게 계산하여 반환한다고 가정을 해보자.
  3. n=k인 경우를 고려해보자. func 는 먼저 func(k-1) 호출하는데 2번의 가정에 의해서 0부터 k-1까지의 합을 올바르게 계산하여 반환한다. 매서드 func 는 그 값에다가 n을 더하여 반환한다. 따라서 매서드 func 는 0에서 k까지의 합을 올바로 계산하여 반환한다.

### Factorial : n!

* 0! = 1
* n! = n\*(n-1)! (if n>0)

```java
package practice.algorithm;

public class Main {

    public static void main(String[] args) {
        int result = factorial(5);
        System.out.println("result = " + result);
    }

    public static int factorial(int n) {
        if (n == 0) {
            return 1;
        }

        return n * factorial(n-1);
    }

}

//result = 120
```

#### 수학적 귀납법으로 위의 코드 증명하기

* 정리 : factorial(int n)은 음이 아닌 정수 n에 대하여 n!을 올바로 계산한다.
* 증명
  1. 먼저, n=0인 경우, 1을 반환한다. 올바르다.
  2. 임의의 양의 정수인 k에 대해서 n < k인 경우, n! 를 올바르게 계산한다고 가정해보자.
  3. 만약 n=k인 경우, factorial 은 먼저 factorial(k-1)를 호출하는데 2번의 가정에 의해서 (k-1)!이 올바로 계산되어 반환된다. 매서드 factorial 은 여기에 k 를 곱하여 반환하므로k\*(k-1)! = k! 를 올바로 반환한다고 할 수 있다.

### x^n 도 같은 방식으로 적용될 수 있다.

```java
package practice.algorithm;

public class Main {

    public static void main(String[] args) {
        int result = power(5, 3);
        System.out.println("result = " + result);
    }

    public static int power(int x, int n) {
        if (n == 0) {
            return 1;
        }

        return x * power(x, n-1);
    }

}

//result = 125
```

### Fibonacci Number

```java
package practice.algorithm;

public class Main {

    public static void main(String[] args) {
        int result = fibonacci(5);
        System.out.println("result = " + result);
    }

    public static int fibonacci(int n) {
        if (n < 2) {
            return n;
        }

        return fibonacci(n-1) + fibonacci(n-2);
    }

}

//result = 5
```

### 최대공약수 : Euclid Method

```java
package practice.algorithm;

public class Main {

    public static void main(String[] args) {
        int result = gcd(90, 120);
        System.out.println("result = " + result);
    }

    public static int gcd(int m, int n) {
        if (m < n) {
            int temp = 0;
            temp = m;
            m = n;
            n = temp;
        }

        if (m%n == 0) {
            return n;
        }

        return gcd(n, m%n);
    }

}
```

* m≥n 인 두 양의 정수 m과 n에 대해서 m이 n의 배수이면, gcd(m, n) = n이고
* 그렇지 않으면 gcd(m, n) = gcd(n, m\&n)이다.
