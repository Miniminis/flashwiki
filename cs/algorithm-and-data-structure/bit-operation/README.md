# Bit Operation

{% embed url="https://youtube.com/watch?v=yHBYeguDR0A&feature=shares" %}

## 1. 덧셈&#x20;

* 십진수 덧셈과 같지만, 2가 넘어갈 경우, 앞자리로 +1 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (3) (5).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../../.gitbook/assets/image (16) (2).png" alt=""><figcaption></figcaption></figure>

## 2. 뺄셈&#x20;

* 역시 십진수 뺄샘과 유사하다. 부족할 경우, 앞자리에서 가져온다.&#x20;

<figure><img src="../../../.gitbook/assets/image (14) (1) (5).png" alt=""><figcaption></figcaption></figure>

## 3. 곱셈

* 십진수 곱셈과 비슷하다. 자리수별로 해주면 된다.&#x20;
* 1\*1일 때만 1이되고, 나머지 경우는 모두 0이 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (25) (3).png" alt=""><figcaption></figcaption></figure>

## 4. OR 연산 &#x20;

* 둘 다 0이면 0이고, 둘 중 하나라도 1이면 1이 된다.&#x20;
* XXXX | 0000 = XXXX&#x20;
* XXXX | 1111 = 1111&#x20;
* XXXX | XXXX = XXXX&#x20;

<figure><img src="../../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (29) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

## 5. AND 연산&#x20;

* 두개 모두 1이면 1, 나머지 경우는 모두 0 이다.&#x20;
* 0011 & 0101 = 0001&#x20;
* XXXX & 0000 = 0000&#x20;
* XXXX & 1111 = XXXX&#x20;
* XXXX & XXXX = XXXX&#x20;

<figure><img src="../../../.gitbook/assets/image (28) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (19) (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (27) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (26) (2).png" alt=""><figcaption></figcaption></figure>

## 6. NOT 연산&#x20;

* 각 자리 수의 반대 수를 표현한다.&#x20;
* \~ 0101 = 1010&#x20;

<figure><img src="../../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

## 7. XOR 연산&#x20;

* 두 개가 같다면 0, 다르면 1로 표현한다.&#x20;
* XXXX ^ 0000 = XXXX&#x20;
* XXXX ^ 1111 = \~XXXX&#x20;
* XXXX ^ XXXX = 0000&#x20;

<figure><img src="../../../.gitbook/assets/image (24) (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (4) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (20) (4).png" alt=""><figcaption></figcaption></figure>

## 8. Shift 연산&#x20;

* 왼쪽으로 2칸 이동 : 1001 << 2 = 100100&#x20;
* 오른쪽으로 2칸 이동 : 1001 >> 2 = 10&#x20;
* Logical Right Shift (부호 무시) :&#x20;
* Arithmatic Right Shift (부호 고려) &#x20;

<figure><img src="../../../.gitbook/assets/image (6) (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (13) (4).png" alt=""><figcaption></figcaption></figure>

### 8-1. Logical Right Shift : 부호 무시&#x20;

<figure><img src="../../../.gitbook/assets/image (22) (1) (3).png" alt=""><figcaption></figcaption></figure>

### 8-2. Arithmatic Right Shift&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (2) (6).png" alt=""><figcaption></figcaption></figure>

## 9. 실전문제&#x20;

### 9-1. 숫자 9의 3번째 인덱스 비트값이 0인지 1인지 확인하시오&#x20;

* 9를 이진수로 표현하면, 1001&#x20;
* 3번째 인덱스는 1 이다.&#x20;
* 이를 구하기 위해서는 아래와 같은 연산을 해야한다.&#x20;
  * 먼저, 9에서 3번째 인덱스를 제외하고 나머지 숫자를 날릴 수 있는 수를 구한다.&#x20;
  * 다시 말하자면, 1001 에서 세번째 인덱스 수인 1을 가져오기 위해서 나머지는 다 0으로 만들어야 한다.&#x20;
  * 즉, 1001 ? XXXX = 1000 이렇게 되어야 하는 수 XXXX와 연산 ? 를 구해야한다.&#x20;
  * 답은 & 1000 이다. 1001 & 1000 = 1000 이므로.&#x20;
  * 이제 1000 비트값을 만들어주기 위해서 연산을 만들면, 바로 1 << 3 이 된다.&#x20;
* 코드로 표현하면 아래와 같다.&#x20;

```java
public class BitOperation1 {

    static boolean getBit(int num, int idx) {
        return (num & (1 << idx)) != 0;
    }

    public static void main(String[] args) {
        //1 0 0 1
        System.out.println(getBit(9, 3));
        // 0 1 0 1
        System.out.println(getBit(5, 3));
    }
}
```



### 9-2. 숫자 N의 i번째 비트값을 1로 세팅하는 함수&#x20;

```java
package practice.algorithm.bit;

public class SetBit {

    static int setBit(int num, int idx) {
        return num | (1 << idx);
    }

    public static void main(String[] args) {
        //1 0 0 1 -> 9 (same)
        System.out.println(setBit(9, 3));
        // 0 1 0 1 -> 13 (changed)
        System.out.println(setBit(5, 3));
    }
}
```

### 9-3. 숫자 N의 i번째 비트값을 0으로 세팅하는 함수 &#x20;

```java
package practice.algorithm.bit;

public class ClearBit {

    static int clearBit(int num, int idx) {
        return num & ~(1 << idx);
    }

    public static void main(String[] args) {
        //1 0 0 1 -> 0 0 0 1 = 1
        System.out.println(clearBit(9, 3));
        // 0 1 0 1 -> 0 1 0 1 = 5
        System.out.println(clearBit(5, 3));
    }
}
```

### 9-4. 그밖에

```java
package practice.algorithm.bit;

public class ClearLeftBit {

    static int clearLeftBits(int num, int idx)  {
        return num & ((1 << idx) -1);
    }

    public static void main(String[] args) {
        //1
        System.out.println(clearLeftBits(169, 3));
    }
}


package practice.algorithm.bit;

public class ClearRightBit {

    static int clearRightBits(int num, int idx)  {
        return num & (-1 << (idx+1));
    }

    public static void main(String[] args) {
        //1
        System.out.println(clearRightBits(169, 3));
    }
}


package practice.algorithm.bit;

public class UpdateBit {

    static int updateBit(int num, int idx, boolean val)  {
        return num & ~(1 << idx) | ((val? 1:0) << idx);
    }

    public static void main(String[] args) {
        //1
        System.out.println(updateBit(169, 3, false));
    }
}

```

