---
description: 도둑이 보석을 훔쳐갈 때, 가방에 들어가는 한도 내에서 가장 비싼 것을 훔쳐가려면 어떻게 해야할까?
---

# Knapsack problem

## 개념

* 도둑이 보석을 훔쳐가는 상황이다. 가방이 감당할 수 있는 무게 내에서 가장 비싼 값의 보석들을 훔쳐가려면 어떻게 해야할까?&#x20;
* N개 아이템과 배낭이 주어진다. 각각의 아이템은 w의 무게와, v의 가격을 가지고 있다.&#x20;
* 배낭의 용량은 W이다.
* **배낭의 용량을 초과하지 않으면서 가격이 최대가 되는 부분집합을 구하고자 한다.**&#x20;

<figure><img src="../../../.gitbook/assets/image (12) (5).png" alt=""><figcaption></figcaption></figure>

* 보통 이러한 문제는 greedy 적으로 제일 먼저 접근하곤 한다.&#x20;
  * 1\) 가격이 비싼 순서대로 넣거나 2) 무게가 가벼운 순서대로 넣거나 3) 단위 무게당 가격이 높은 것부터 선택하는 방식으로 접근하는데, 안타깝게도 1-3번의 방법 모두 정답을 구할 수 없다.&#x20;
  * 답은 가격의 합이 40이 되는 {3, 4} 인데, 따라서 이 문제는 greedy 로 풀 수가 없다.&#x20;

## 순환식

* 순환식을 생각해보자.&#x20;
* 아이템이 i개가 주어진다고 했을 때, 1부터 i개의 아이템에 대한 무게와 가격정보가 주어질 것이다.&#x20;
*   이때 우리는 마지막 i번째를 선택하는 경우와 그렇지 않은 경우를 나누어서 생각해볼 수 있다.&#x20;

    * i 번째를 선택하지 않는다면, 순환식 OPT(i, w) = OPT(i-1, w) 이 된다.&#x20;
    * i번째를 선택한다면, OPT(i, w) = OPT(i-1, W-wi) + vi&#x20;
      * 단 i번째를 선택하는 경우는 배낭의 무게가 W >  wi 를 만족해야한다. 그렇지 않으면, 무조건 첫번째 케이스를 선택할 수 밖에 없다.&#x20;

    <figure><img src="../../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>



## 계산순서

* i-1 값만 고려하면 되므로, 행 우선 계산순서로 진행하면 된다. &#x20;

## 코드&#x20;

<figure><img src="../../../.gitbook/assets/image (6) (3).png" alt=""><figcaption></figcaption></figure>



## 시간복잡도

* O(nW)
* 그렇다면 이것은 다항시간인가?&#x20;
  * 그렇지 않다.&#x20;
  * 다항시간은 입력 크기에 대한 다항함수일때 성립한다.&#x20;
  * 이 문제의 경우, 입력받는 값는 n개의 가격과 n개의 무게이므로, 2n이 된다.&#x20;
  * 그리고 W값은 그냥 주어지는 값이다.&#x20;
  * W값을 입력받기 위해 필요한 비트의 수는 logW이다. logW = k라고 한다면, 결국 W = 2^k가 된다.&#x20;
  * 결국, 이 문제의 시간 복잡도는 O(n2^k)가 되는 것이다.&#x20;
* Knapsack problem 은 유명한 NP hard 알고리즘 중 하나이다.&#x20;
  * NP-hard 알고리즘 : 다항시간 알고리즘이 존재하지 않았고, 앞으로도 존재하지 않을 것이라고 생각되는 알고리즘
