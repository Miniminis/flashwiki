---
description: 선형시간 정렬 알고리즘
---

# sorting in linear time

## counting sort

* n개의 정수를 정렬한다. 모든 정수는 0에서 k사이의 정수이다.&#x20;
  * 사전정보가 주어진다.&#x20;
* 일반적으로 k의 값은 작은 숫자인 경우가 많다.&#x20;
* 예) n명의 학생들의 시험점수를 정렬한다. 단, 모든 점수는 100이하의 양의 정수이다.&#x20;

## k=5 인 경우의 예&#x20;

* 0부터 5까지의 값이 8개 있다고 가정해보자.&#x20;
  * A배열과 같이 숫자가 입력되었다.&#x20;
* 어차피 나올 숫자의 범위가 0 <= x <= 5 임을 알고 있기 때문에, 입력으로 들어올 때, 각 숫자가 몇 번 들어왔는지를 카운트 한다.&#x20;
  * 이를 위해 배열 C에 각 데이터의 입력 횟수를 카운트한다.&#x20;
* 각 값을 이미 알고 있고, 그 값이 나온 횟수를 알기 때문에, 그대로 정렬하면 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (26) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (39) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

{% hint style="info" %}
**이렇게 진행해도 괜찮은가?**&#x20;
{% endhint %}

* 아니다. 대부분의 경우, 정렬할 key 값들은 key 만 달랑 정수형태로 존재하는 것이 아니라, 데이터 레코드의 일부분이기 때문에 이런 식으로 정렬을 하면 안된다.&#x20;
  * 레코드들의 정렬은 제대로 될 수 없다.&#x20;

## 그럼 어떻게 하나요

<figure><img src="../../../.gitbook/assets/image (25) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 배열 C에 count 를 할 때, 해당 데이터에 해당하는 것만 세는 것이 아니라, 해당 값보다 같거나 작은 것의 누적 합으로 카운팅한다.&#x20;
* 즉 배열 C에 있는 각 값은 해당 index 보다 같거나 작은 값들의 개수가 된다.
* 배열 C의 결과만 보고 정렬을 시작한다.&#x20;
* 배열 A에서 제일 마지막 값인 3을 선택한다. 3의 경우, 카운팅 결과를 보면, 3보다 같거나 작은 값이 7개가 된다. 따라서 정렬된 배열인 B에서 7번째 값에 3을 두면, 안전하다. 3은 정렬 완료이다.&#x20;
* 3의 개수가 하나 줄었으므로, 3에 대한 카운터 값을 1 감소시켜 6으로 만든다.&#x20;
* 그 다음 마지막 값인 0을 뽑는다. 0은 2개가 존재하니, 2번째 자리에 두면 안전하다. 다시 0에 대한 카운터를 1 줄인다.&#x20;
* 위의 과정을 반복하면 정렬 완성이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (16) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## 시간복잡도&#x20;

* O(n+k) 혹은 O(n)
  * 보통 n >= k이다.&#x20;
* k가 아주 크다면, 매우 비 실용적이게 된다.&#x20;
* stable 정렬 알고리즘이라고 부른다.&#x20;
  * 입력이 동일한 값이 있을 때, 입력에 먼저 나오는 값이 출력에서도 먼저 나온다.&#x20;
    * 즉, 입력 순서대로 정렬되며 출력된다.&#x20;
  * counting 정렬은 stable 하다.&#x20;

