# 순환적으로 사고하기

## 반복문 - 순환

* 반복문으로 표현할 수 있는 것은 순환으로 표현가능하다.&#x20;
* 그 반대의 경우도 성립한다.&#x20;
* 순환함수는 복잡한 알고리즘을 단순하고 알기 쉽게 표현할 수 있게 해준다.&#x20;
* 하지만 순환함수 호출에 따른 오버헤드가 있다. - 매개변수 전달, 엑티베이션 프레임 생성 등&#x20;



## 1. 문자열의 길이계산

```java
package practice.algorithm.recursion;

public class StringLength {
    public static void main(String[] args) {
        String s = "elephant";
        System.out.println("stringLengthCalculator(s) = " + stringLengthCalculator(s));
    }

    private static int stringLengthCalculator(String s) {
        if (s.length() == 0) {
            return 0;
        }

        return 1 + stringLengthCalculator(s.substring(1));
    }
}
```

## 2. 문자열을 프린트

```java
package practice.algorithm.recursion;

public class PrintString {
    public static void main(String[] args) {
        String s = "elephant";
        printString(s);
    }

    private static void printString(String s) {
        if (s.length() == 0) {
            return;
        }

        System.out.println(s.charAt(0));
        printString(s.substring(1));
    }

    /**
     * e
     * l
     * e
     * p
     * h
     * a
     * n
     * t
     * */
}
```

&#x20;

## 3. 문자열을 뒤집어 프린트하기

```java
package practice.algorithm.recursion;

public class PrintStringReverse {
    public static void main(String[] args) {
        String s = "elephant";
        printReverseOrder(s);
    }

    private static void printReverseOrder(String s) {
        if (s.length() == 0) {
            return;
        }

        printReverseOrder(s.substring(1));
        System.out.println(s.charAt(0));
    }

    /**
     * t
     * n
     * a
     * h
     * p
     * e
     * l
     * e
     * */
}
```

## 4. 입력된 정수를 이진수로 변환하기

```java
package practice.algorithm.recursion;

public class BinaryNumber {
    public static void main(String[] args) {
        int num = 10;
        toBinaryNumber(num);
    }

    private static void toBinaryNumber(int num) {
        if (num < 2) {
            System.out.println(num);
            return;
        }

        toBinaryNumber(num / 2) ;
        System.out.println(num % 2);
    }
    
    /**
     * 1
     * 0
     * 1
     * 0
     * */
}
```



## 5. 배열의 합 구하기

```java
package practice.algorithm.recursion;

public class ArraySum {
    public static void main(String[] args) {
        int[] numArr = new int[]{1, 10, 100, 1000, 10000};
        System.out.println("sumArray(numArr) = " + sumArray(numArr, 0));;
    }

    private static int sumArray(int[] numArr, int index) {
        if (index == numArr.length-1) {
            return numArr[index];
        }

        return numArr[index] + sumArray(numArr, index + 1);
    }
    
    /**
     * sumArray(numArr) = 11111
     * */
}
```

