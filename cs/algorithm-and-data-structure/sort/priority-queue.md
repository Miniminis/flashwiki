---
description: '힙의 응용 : 우선순위 큐'
---

# priority queue

## 우선순위 큐

* 최대 우선순위큐 (maximum priority queue)는 다음의 두 가지 연산을 지원하는 자료구조이다.&#x20;
  * insert(x) : 새로운 원소 x를 삽입한다.&#x20;
  * extract\_max() : 최대값을 삭제하고 반환한다.&#x20;
* 최소 우선순위 큐 (minimum priority queue) 는 extract-max 대신에 extract-min 을 지원하는 자료구조이다.&#x20;
* max heap 을 이용하여 최대 우선순위 큐를 구현한다.&#x20;



## insert(x)&#x20;

<figure><img src="../../../.gitbook/assets/image (32) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (8) (2) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (31) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## extract\_max()

* 힙의 최대값인 루트 노드를 삭제하고 반환해주는 연산&#x20;
* max heap 에서 최대값을 찾는 것은 너무 쉽다. 루트값이기 때문이다.&#x20;
* 문제는 그 이후이다. node 하나가 삭제되어야 하는데 complete binary tree 모양을 유지할 수 있는 유일한 방법은 제일 마지막 노드를 삭제하는 것이다.&#x20;
* 따라서 여기서는 루트 데이터를 삭제하고, 제일 마지막 데이터를 루트로 가져온 뒤, 이 루트 값에 대하여 다시 heapify 를 진행한다.&#x20;
  * heapify : 루트에 대하여 자식 노드와 비교하여 자식이 더 크면, 교체하고, 자신이 더 크면 그대로 유지한채 연산을 끝낸다.&#x20;

<figure><img src="../../../.gitbook/assets/image (40) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

