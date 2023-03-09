# bit masking

## 1. 비트 마스킹&#x20;

* 컴퓨터는 내부적으로 모든 자료를 이진수로 표현한다. 이를 이용해서 정수의 이진수 표현을 아예 자료구조로 쓰는 기법을 비트마스크라고 한다.&#x20;

## 2. 비트 연산자 연습&#x20;

```java
/**
 * 두 수가 모두 1일때만 1, 나머지는 0
 * */
public class AND {
    public static void main(String[] args) {
        int a = 4;  //100
        int b = 7;  //111

        System.out.println(a & b);  //100
    }
}

/**
* 둘 중 하나라도 1이면 1, 나머지는 0
* */
public class OR {
    public static void main(String[] args) {
        int a = 2;  //010
        int b = 5; //101

        System.out.println(a | b);  //111 = 7
    }
}

/**
 * 모든 비트에 NOT 연산을 한다.
 * */
public class NOT {
    public static void main(String[] args) {
        int a = 3;  //011

        System.out.println(~a); //100 = -4

        int b = 7;  //111 -> 0000 .... 00000111 = 7
        System.out.println(~b); //000 -> 1111.... 11111000 = -8
    }
}


/*
* 서로 다르면 1, 같다면 0
* */
public class XOR {
    public static void main(String[] args) {
        int a = 3;      //011
        int b = 5;      //101

        System.out.println(a ^ b);  //110 = 6
    }
}


public class Shift {
    public static void main(String[] args) {
        System.out.println(9 << 2);
        //1001 -> 0010 0100 = 4+32 = 36

        System.out.println(Integer.toBinaryString(15));
        System.out.println(15 << 3);
        //1111 -> 0111 1000 = 8+16+32+64 = 24+96 = 120

        System.out.println(4 >> 2);
        //100 -> 001 = 1

        System.out.println(1 << 2);
        //001 -> 100 = 4
    }
}

```

## 3. 집합&#x20;

```java
/**
* 집합
 * - 원소를 0 ~ n-1 까지 번호를 부여한다.
 * - 번호에 해당하는 비트가 1이면 원소가 포함, 0이면 불포함이라고 판단
* */
public class Set {
    public static void main(String[] args) {
        //{A, B, C, D, E, F, G}
        //0, 1, 2, 3, 4, 5, 6

        /*
        * {A} 의 부분집합 = 1000000 = 64
        * {C, F}의 부분집합 = 0010010 = 2+16 = 18
        * */
    }
}

/**
 * 집합에 원소 추가
 * - 현재상태인 cur 이 존재할 때, p번 원소를 추가한다고 가정.
 * - cur 에서 p번째 비트만 1로 바꾸어주어야 한다.
 * */
public class SetAdd {
    public static void main(String[] args) {
        int b = 16;     //10000
        //목표 : 11010 = 26
        System.out.println(b | 1 << 3 | 1 << 1);

        int a = 32;     //100000
        //뒤에서 4번째 index에 원소를 추가하고자 할때, 만들려는 수는 101000

        System.out.println(Integer.toBinaryString(1 << 3));
        System.out.println(a | (1 << 3));       //40
    }
}

/**
 * 원소 삭제
 * - current 에서 p번 원소만 삭제
 * - p번 비트를 0으로 바꾸어주어야 한다.
 * */
public class SetRemove {
    public static void main(String[] args) {
        int p = 40;     //101000
        //3번째 원소를 제거하려면? 목표하는 수는 100000 = 32
        System.out.println(Integer.toBinaryString(1 << 3));
        System.out.println(p & ~(1 << 3));      //32
    }
}

/**
 * 1이면 0, 0이면 1로 변환
 * */
public class SetToggle {
    public static void main(String[] args) {
        int a = 7;  //111
        //목표 : 101 = 5
        System.out.println(a ^ 1 << 1);
    }
}

/**
 * 집합의 크기 구하기
 * */
public class SetSize {
    public static void main(String[] args) {
        System.out.println(bitCount(15));
    }

    private static int bitCount(int num) {
        if (num == 0) {
            return 0;
        }
        return num % 2 + bitCount(num / 2);
    }
}

/**
 * 합집합 구하기
 * */
public class SetCombine {
    public static void main(String[] args) {
        //a | b : 합집합
        //a & b : 교집합
        //a & ~b : 차집합
        //a ^ b : 합집합 - 교집합 = a와 b 중 하나에만 포함된 원소
    }
}

```



## 4. 참고&#x20;

* [https://travelbeeee.tistory.com/451](https://travelbeeee.tistory.com/451)
