# 피보나치 수열의 시간복잡도

<figure><img src="../../../.gitbook/assets/image (8) (8).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

피보나치 수열은 함수의 매 수행마다 2개씩 호출이 늘어나므로 O(2^n)의 시간복잡도를 갖는다.&#x20;

하지만 위의 그림에서도 알 수 있듯, 불필요한 작업을 많이 반복하고 있는데, 예를 들면, F(1)의 경우, 이미 계산한 값이 있는데도 매 함수 호출마다 반복적으로 호출되고 있는 것을 볼 수 있다. &#x20;

### 개선&#x20;

이러한 점은 기존의 결과값이 존재하는지 검사하는 로직을 추가하여 아주 간단하게 개선할 수 있다.&#x20;

<figure><img src="../../../.gitbook/assets/image (31).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

함수가 호출될 때, 기존 결과 배열의 값을 참조하여 이미 존재하면 그냥 그 배열의 값을 반환하도록 하면, 불필요하게 이미 했던 연산을 반복하지 않아도 된다.&#x20;



```java
/**
 * 피보나치수
 * - 캐싱을 적용하여 시간복잡도 낮추기
 * */
public class Fibonacci2 {

    private static int[] cache;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        cache = new int[n+1];
        Arrays.fill(cache, 0);

        System.out.println(fibonacci(n));
    }

    private static int fibonacci(int idx) {
        if (idx <= 2) {
            return cache[idx] = 1;
        }

        if (cache[idx] > 0) {
            return cache[idx];
        }

        return cache[idx] = fibonacci(idx-1) + fibonacci(idx-2);
    }
}
```



## 응용 &#x20;

* 만약 아래와 같은 코드가 있다면, 시간복잡도가 어떻게 될까?&#x20;

<figure><img src="../../../.gitbook/assets/image (7) (8).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

많은 사람들이 n2^n 이라고 생각할 수 있다. 하지만, 위의 경우는 allF(n)의 결과를 반복하면 아래와 같다.&#x20;

* 2^1 + 2^2 + 2^3 + ... + 2^(n-1) + 2^n&#x20;
* \= 2^n -1 + 2^n&#x20;
* \= 2\*2^n -1&#x20;
* \= O(2^n)

&#x20;
