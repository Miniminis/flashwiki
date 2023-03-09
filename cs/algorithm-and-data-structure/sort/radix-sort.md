---
description: sorting in linear time
---

# radix sort

## 개념&#x20;

* n개의 d자리 정수들이 주어진다.
  * 예) 영문 문자열 : 5자리 문자열 데이터들 -> a부터 z까지 총 26개의 데이터가 올 수 있다. &#x20;
* 가장 낮은 자리수부터 정렬한다.&#x20;
* 그 이후에는 뒤에서 두번째 자리를 기준으로 정렬&#x20;
* 위를 반복하면 정렬이 완성된다.  &#x20;

<figure><img src="../../../.gitbook/assets/image (19) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## 어떤 원리로 정렬이 되는가&#x20;

stable sort : 동일한 값에 대하여 입력된 순서대로 정렬되어 출력되는 것을 말한다. &#x20;

radix sort 는 매 단계마다 stable sort 를 유지해야하며, 따라서 counting sort 를 이용하게 된다.&#x20;

* 첫번째 스텝을 거치면, 가장 낮은 자리수만으로 정렬이 된다.&#x20;
* 두번째 스텝을 거치면, 마지막 두 자리수에 대해서 정렬이 된다. 첫번째 스텝을 거쳤기 때문이다.&#x20;
  * 동점인 데이터에 대해서 (앞자리가 같은 수에 대하여) 입력된 순서대로 정렬이 된다. = stable&#x20;
* 시간 복잡도 O(d(n+k))
  * k(데이터가 될 수 있는 값의 개수), n(데이터의 개수), d(자릿수)
  * 만약 k = O(n), d 가 상수이면, 결국 시간복잡도는 O(n)이 될수도 있다. &#x20;

<figure><img src="../../../.gitbook/assets/image (35) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 요약&#x20;

<figure><img src="../../../.gitbook/assets/image (20) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (37) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (5) (2) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (33) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>
