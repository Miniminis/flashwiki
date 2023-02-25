---
description: 합병 정렬
---

# merge sort

## 개념&#x20;

* 분할정복법을 이용하는 대표적인 알고리즘 중 하나이다.&#x20;
* 분할정복법 (Divide and Conquer)
  * 분할 : 해결하고자 하는 문제를 작은 크기의 `동일한` 문제들로 분할 &#x20;
  * 정복 : 각각의 작은 문제를 `순환적`으로 해결 -> 재귀함수
  * 합병 : 작은 문제의 해를 합하여(merge) 원래 문제에 대한 해를 구한다.&#x20;



## 진행순서

1. 데이터가 저장된 배열을 절반으로 나눈다.&#x20;
2. 각각을 순환적으로 정렬한다.&#x20;
3. 정렬된 두 개의 배열을 합쳐 전체를 정렬한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (13) (1) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

#### 가장 중요한 것은 바로 merge 하는 과정&#x20;

* 이미 합치고자 하는 두 개의 배열이 정렬된 상태이기 때문에 아래와 같이 진행해야한다.&#x20;
* 배열 A와 배열 B에서 가장 작은 값을 골라 추가 배열C에 저장한다.&#x20;
* 해당 값을 제외한 뒤, 나머지 요소를 비교하여 더 작은 값을 추가 배열에 넣는다.&#x20;
* 차례대로 반복한다.&#x20;



#### 코드로 살펴보기&#x20;

<figure><img src="../../../.gitbook/assets/image (10) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (25) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 합병정렬에서는 반드시 추가 배열이 필요하다.&#x20;
* 추가배열에 정렬된 상태로 완료되면, 이 임시 배열을 다시 원래 배 열로 옮겨준다.&#x20;
* 시간복잡도&#x20;
  * 데이터가 n개라면, 데이터를 2/n개로 나누어 정렬하는 시간이 각각 2/n시간이다.&#x20;
  * 그리고 merge 하는 과정동안 n 번 반복하게 된다.&#x20;
  * T(n) = T(n/2) + T(n/2) + n (if n >= 1)&#x20;
  * 이는 곧 O(nlogn) 이 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (17) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

