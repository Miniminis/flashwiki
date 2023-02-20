---
description: 다익스트라 알고리즘
---

# 최단경로

## 개념

* 가중치 (방향) 그래프 G=(V, E) 의 모든 에지에 가중치가 주어진다.&#x20;
  * 무방향 그래프의 경우, 방향 엣지를 2배해주면 된다.&#x20;
* BFS 의 최단경로&#x20;
  * BFS의 최단경로는 모든 엣지의 가중치가 1로 일정하기 때문에, edge 의 개수가 최소가 되는 경우만 구해주면 된다.&#x20;
  * 하지만 가중치가 부여되는 순간, BFS 만으로 해결할 수는 없다.&#x20;
* 경로 p=(v0, v1, ... vk) 의 길이는 경로 상의 모든 에지의 가중치의 합&#x20;
* 노드 u에서 v까지의 최단경로의 길이를 (u, v) 라고 표시한다.&#x20;

## 유형

* single-source
  * 하나의 출발노드 s로부터 다른 모든 노드까지의 최단 경로를 찾는다.&#x20;
  * one-to-all&#x20;
  * 대표적으로 dijkstra의 알고리즘이 있다. &#x20;
* single-destination
  * 모든 노드로부터 하나의 목적지 노드까지의 최단 경로를 찾는다.&#x20;
  * 위의 single-source 문제와 동일하다. (반대로 생각하면 됨) &#x20;
* single-pair
  * one-to-one path problem&#x20;
  * 주어진 하나의 출발노드 s로부터 하나의 목적지 노드 t까지의 최단경로를 찾는다.&#x20;
  * 최악의 경우, 시간복잡도에서 single-source 문제보다 나은 알고리즘이 없다.&#x20;
    * \= single-source 알고리즘처럼 s부터 다른 노드까지의 모든 경로를 구하는 과정에서 우연히 특정 지점 t까지의 최단경로를 찾는 것과 큰 차이가 없다.&#x20;
* all-pairs&#x20;
  * 모든 노드 쌍에 대해서 최단 경로를 찾는다.&#x20;
  * all-to-all 알고리즘&#x20;
  * 대표적으로 floyd-washall 알고리즘이 존재한다.&#x20;

## 음수가중치

* 음수 사이클이 있다면, 최단 경로가 정의되지 않는다.&#x20;
  * 음수 사이클 : 어떤 그래프의 사이클 중 최단 경로가 음수인 경우&#x20;
* 알고리즘에 따라 음수 가중치가 있어도 작동하는 경우도 있고, 그렇지 않은 경우도 있다.&#x20;
* 다익스트라 알고리즘의 경우, 음수 가중치를 고려하지 않는다.&#x20;

## 기본특성

<figure><img src="../../../.gitbook/assets/image (49).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 최단 경로의 어떤 부분 경로도 역시 최단 경로이다.&#x20;
* 최단경로는 사이클을 포함하지 않는다.&#x20;
  * 음수 사이클이 없다는 가정하에 해당한다.&#x20;
  * 사이클이 이루어지면, 굳이 돌아서 가지 않아도 더 짧은 경로가 존재하므로 말이 안된다.&#x20;

## single-source (one-to-all)&#x20;

* 입력 : 음수 사이클이 없는 가중치 방향 그래프 G=(V, E) 와 출발노드 s 가 주어진다.&#x20;
* 목적 : 각 노드 v에 대해서 다음을 계산한다.&#x20;
  * d\[v]
    * 처음에는 d\[s]=0, d\[v]=무한대 로 시작한다.&#x20;
    * 알고리즘이 진행됨에 따라서 감소해간다. 하지만 항상 d\[v] >= δ(s, v) 를 유지한다.
      * s에서 v까지의 최단경로
    * 최종적으로는 d\[v] = δ(s, v)
  * 𝛑\[v] : s에서 v까지의 최단경로상에서 v의 직전노드 (predecessor)&#x20;
    * 그런 노드가 없는 경우, 𝛑\[v] = null&#x20;

### relaxation&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 대부분의 single-source 최단경로 알고리즘의 기본구조&#x20;
  * 초기화 : d\[s]=0, d\[v]=∞, 𝛑\[v]=NIL
  * 에지들에 대해서 반복적으로 relax 연산을 반복적으로 수행한다.&#x20;
* 알고리즘들 간의 차이는 어떤 에지에 대해서 어떤 순서로 relaxation 을 하느냐에 있다.&#x20;

### 기본알고리즘&#x20;

<figure><img src="../../../.gitbook/assets/image (10) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 모든 엣지들에 대해서 relax 연산을 했는데도 아무런 변화가 없을 때까지 반복해준다.&#x20;
  * 한번 두 노드에 대해서 relaxation 연산을 진행한다면, 다른 노드에 대해서도 다시 relax 연산으로 맞추어주어야 한다.&#x20;

#### 질문

1. 이렇게 계속 반복하면 최단 경로가 찾아지는가?&#x20;
   1. 그렇다. 매 relax 연산마다 노드별 최단경로값이 갱신된다. 따라서 목표지점 노드의 d 값을 찾으면 그것이 바로 최단경로가 된다.&#x20;
2. 몇 번이나 반복해야하는가?
   1. 최악이 경우에도 n-1 번을 넘지 않는다.  &#x20;

<figure><img src="../../../.gitbook/assets/image (14) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 그래프의 모든 노드를 거쳐서 가는 경우라고 하더라도, 엣지의 개수는 n-1을 넘을 수 없다.&#x20;
  * 최단경로에 사이클은 존재하지 않기 때문에&#x20;



### bellman-ford 알고리즘&#x20;

<figure><img src="../../../.gitbook/assets/image (6) (1) (4).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 반복 사이클을 n-1 번으로 고정시킴&#x20;
* n-1번 relax 연산을 반복했음에도, 여전히 d\[v] >= d\[u] + w(u, v)가 존재한다면, 그것은 음수 사이클이 존재한다는 의미이므로 최단경로를 구할 수 없다고 return&#x20;
* 시간복잡도 : O(nm)&#x20;

<figure><img src="../../../.gitbook/assets/image (52) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

#### worst scenario&#x20;

<figure><img src="../../../.gitbook/assets/image (57).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 진행 순서가 1000 -> 400 -> 200 -> 100 ... 이렇게 진행되면, 최악의 경우, d\[v] 에 대해서 1000 -> 800 -> 600 ... 으로 매 라운드마다 update 가 진행될 수 있다.&#x20;
* 그렇게되면 v 지점 이후의 노드들에 대해서는 쓸데없이 매 라운드마다 계속 업데이트가 진행된다.&#x20;
* 해결방법&#x20;
  * 최종적으로 d\[v] 가 고정되었을 때, 그 이후의 지점에 대해서 relaxation 을 시작하도록 하면 비효율을 줄일 수 있다.&#x20;
  * 지금처럼 모든 노드들에 대해서 매번 relax 연산을 하지 않고, 어떤 특별한 조건을 만족하는 경우에만 relax 연산하도록 하는 방법이 바로 dijkstra 알고리즘이 된다.&#x20;

### dijkstra 알고리즘

* 음수 가중치가 없다고 가정한다.&#x20;
* s로부터 최단 경로의 길이를 이미 알아낸 노드들의 집합 S를 유지한다. 맨처음에는 S = {} 로 출발한다.&#x20;
* S에 속하지 않은 각 노드 u에 대해서 d(u)는 이미 S에 속한 노드들만 거쳐서 s로부터 u까지 가는 최단 경로의 길이가 된다.&#x20;
* 정리 : d(u) = MIN(d(v)) 인 노드 u에 대해서, d(u) 는 s에서 u까지의 최단 경로의 길이이다.

<figure><img src="../../../.gitbook/assets/image (50) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 초기화한다. S = {}
* 시작점 s 에 대해서 요소를 추가한다.&#x20;
  * S = {s}
* s -> 모든 경로에 대해서 relaxation 먼저 한다. 위의 경우, 10보다는 5가 작으므로, 출발점 s에서 가중치가 5인 경로를 택하게 된다.&#x20;
  *
* 이때, d\[s]=0 이고 d\[1] 의 경우, 이미 최단 경로인 5로 선택되었으므로, d\[1]=5 가 확정되고 더이상 변할 일이 없다.
  * S = {s, a}
* 이후에는 다시 1로부터 뻗어있는 엣지들 중에서 가장 최소값을 선택한다. 위의 경우는 가중치 2인 엣지가 된다.&#x20;
  * S = {s, a, b}
* 그렇게 되면, d\[2] = 5 + 2 = 7이고 이 경로는 최단 경로들만 선택한 것이므로, 더이상 바뀔 가능성이 없다. 확정시킨다.&#x20;

<figure><img src="../../../.gitbook/assets/image (51) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (58).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (55).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (60).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (53).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* while 문은 전체 node 개수만큼 n 번 반복&#x20;

<figure><img src="../../../.gitbook/assets/image (56).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 최소우선순위큐 (최소힙)&#x20;
* prim 의 알고리즘과 동일하다.&#x20;
* 우선순위 큐를 사용하지 않고 단순하게 구현할 경우 O(n^2)&#x20;
* 이진 힙을 우선순위 큐로 사용할 경우 O(nlogn + mlogn)&#x20;
* fibonacci heap 을 사용하면 O(nlogn+m)에도 구현이 가능하다.&#x20;



## Floyd-Warshall 알고리즘 (all-to-all)

<figure><img src="../../../.gitbook/assets/image (4) (1) (8).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 동적계획법 알고리즘 중 대표적
* 가중치 방향그래프 G=(V, E), V = {1, 2, 3,..., n}
* 모든 노드 쌍들간의 최단경로의 길이를 구한다.&#x20;
* d^k\[i,j]
  * 중간에 노드 집합 {1, 2, 3, ..., k}에 속한 노드들만 거쳐서 노드 i에서 j까지 가는 최단 경로의 길이&#x20;
  * 방문 가능한 노드들이 정해져있고, 그 노드들만 이용해서 지나는 경우를 의미한다.  &#x20;
* d^0\[i,j]
  * 어떠한 노드도 지나지 않고, i에서 j로 이동하는 방법의 가짓수
  * i, j 간에 간선이 존재한다면, 그것을 제공하면 되고,&#x20;
  * 없다면, 무한대로 처리&#x20;
* d^k\[i,j]
  * 중간의 노드집합 {1, 2, ... k}에 속한 노드들만 거쳐서 노드 i에서 j까지 가는 최단경로는 두 가지 경우가 있다.&#x20;
    * 노드 k를 지나는 경우와
    * 노드 k를 지나지 않는 경우
  * min{d^k-1\[i,j], d^k-1\[i,k] + d^k-1\[k, j]}
* d^n\[i,j]
  * 아무 조건 없이 i에서 j까지 가는 최단경로&#x20;

<figure><img src="../../../.gitbook/assets/image (3) (1) (5).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (6) (9) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (7) (9).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 3차원 배열 d\[k]\[i]\[j]를 만들지 않고, d\[i]\[j] 의 값에 그냥 덮어 씌워도 된다.&#x20;
* 기존의 값은 다음 값을 계산하기 위한 재료일 뿐, 굳이 보관하고 있지 않아도 된다.&#x20;
* 발생할 수 있는 문제점
  * n우선으로 반복 계산이 들어가기 때문에, 실제로 i, j > k인 상황에서는 k-1번째까지의 값이 아니라, k번째까지의 값이 이미 업데이트된 상태로 계산이 되게 된다.&#x20;
  * 하지만 문제될 것이 없는 것은 d^k\[i, k] 와 d^(k-1)\[i, k] 은 항상 같다. k가 끝점이므로, k를 지나버릴수는 없기 때문이다.&#x20;



### 경로찾기&#x20;

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 실제로 i에서 j까지 가는 데에 거치게 되는 노드들, 즉 최단경로의 경로 그 자체를 출력할 수 있게 된다.&#x20;
