---
description: 퀵 소트
---

# quick sort

## 분할정복법

* 분할 : 배열을 다음과 같은 조건이 만족되도록 두 부분으로 나눈다.&#x20;
  * 기준값 pivot 을 기준으로 lower parts <= upper parts&#x20;
* 정복 : 각 부분을 순환적으로 정렬한다. -> partition&#x20;
* 합병 : Noting to do!&#x20;

<figure><img src="../../../.gitbook/assets/image (24) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 개요&#x20;

<figure><img src="../../../.gitbook/assets/image (16).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* partition() 에 의해 분할이 되고, pivot 인 q가 반환된다.&#x20;
* quickSort()를 재귀 호출하여 각각 정렬되면 끝!&#x20;

<figure><img src="../../../.gitbook/assets/image (11) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 피봇은 맨 마지막 값으로 고정시켜두고, 나머지 데이터들을 피봇보다 작은 파트와, 큰 파트로 나누어 정렬시킨 뒤, 제일 마지막에 그 가운데 값과 피봇을 바꿈으로써 정렬하면 된다.&#x20;
* 지금 검사하려는 값 j를 기준으로 생각해보자. 만약 j가 피봇 x보다 크다면 어떻게 될까? 그냥 가만히 있으면 된다.&#x20;
* 하지만 j가 피봇 x보다 작다면, pivot 보다 작은 값들 중 가장 마지막 값이었던 i 의 인덱스를 하나 증가시켜 위치를 이동시켜주고, 해당 위치의 값 즉 pivot 보다 큰 값들 중 제일 첫번째 값과 위치를 교환하면 된다. &#x20;

<figure><img src="../../../.gitbook/assets/image (21) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (1) (4).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 시간복잡도 : O(n^2)&#x20;

<figure><img src="../../../.gitbook/assets/image (15) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 최악의 경우, 피봇이 데이터들 중 최소거나 최고값이 되어서 분할이 적절하게 이루어지지 않는 경우를 말한다.&#x20;
* 이미 정렬된 데이터의 경우, 이런 일이 발생한다.&#x20;
* 최상의 경우 = 항상 절반으로 분할되는 경우
  * T(n) = 2T(n/2) + O(n) = O(nlogn)&#x20;



## 이상적인 시간복잡도

* 항상 절반으로 나누어떨어진다면, 물론 가장 좋겠지만, 현실적으로 그것은 불가능하다.&#x20;
* 항상 한쪽이 적어도 1/9 이상이 되도록 분할되는 것이 보장된다면? 이것은 현실적으로 그렇게 어려운 일은 아닐 것이다.&#x20;
* (9/10)^k \* n = 1, k = log10/9n = O(logn)&#x20;
  * 9/10 정도의 나눔 비율만 유지되더라도 시간복잡도가 O(logn)으로 확실히 줄어들게 된다.&#x20;
* 퀵정렬이 시간복잡도 상으로 O(n^2) 이지만 빠른 이유가 바로 이것이다. 대부분의 경우 O(logn)을 유지하기 때문!&#x20;

<figure><img src="../../../.gitbook/assets/image (5) (1) (5).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

{% hint style="info" %}
결국 quick sort 의 성능은 partition 이 얼마나 잘 이루어지느냐에 따라 성능상 차이가 난다.&#x20;
{% endhint %}



## 평균 시간 복잡도&#x20;

* 평균 혹은 기대값?&#x20;
  * 모든 입력 i에 대하여, I가 입력으로 들어올 확률 P(I)와 I를 정렬하는데 걸리는 시간 T(I)를 곱한 값의 합이다.&#x20;
  * 문제는 P(I) 를 모른다는 것. 그래서 P(I) 에 대해서 적절한 가정을 한 후, 분석을 한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (12) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 피봇을 선택할 때

* 첫번째 값이나 마지막 값을 피봇으로 선택하는 것은 좋은 방법이 아니다.&#x20;
  * 이미 정렬된 데이터나 거꾸로 정렬된 데이터가 최악의 경우 들어올 수 있다.&#x20;
  * 하지만 현실의 데이터는 그렇게 랜덤하지 없다. 따라서 정렬된 데이터가 입력될 확률은 생각보다 매우 높다.&#x20;
  * 그래서 좋은 방법이 아님!&#x20;
* median of three
  * 첫번째 값과 마지막 값, 그리고 가운데 값 중에서 중진값을 피봇으로 선택하는 방법을 택할 수 있다.&#x20;
  * 첫번째나 마지막 값을 택해서 이미 정렬된 데이터에 대하여 0:10 으로 계속 분할에 실패하는 경우를 막기 위한 현실적인 방법이다. &#x20;
  * 하지만 최악의 경우에 대한 시간복잡도가 달라지지는 않는다. &#x20;
* ramdomized quicksort
  * 피봇을 랜덤하게 선택하는 방법이 있다.&#x20;
  * 평균 시간 복잡도는 O(nlogn)이 된다.&#x20;

