---
description: 너비 우선 순회
---

# BFS, Breadth First Search

## 그래프의 순회&#x20;

* 순회 : 그래프의 모든 노드를 방문하는 일
* 대표적인 두 가지 방법
  * BFS, Breadth-first search : 너비 우선 순회&#x20;
  * DFS, Depth-first search : 깊이 우선 순회&#x20;

## BFS

<figure><img src="../../../.gitbook/assets/image (33) (1) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 노드들을 동심원의 형태로 순회한다.&#x20;
  * 출발노드가 주어지거나 임의의 노드 1개를 출발노드로 정한다.&#x20;
  * 출발노드 S에서 우선 S를 방문처리하고&#x20;
  * S와 거리가 1인 바로 인접한 노드들을 방문하고,&#x20;
  * 그 이후로는 S로부터 거리가 2인, 거리가 3인 노드들을 차례대로 방문하는 방식이다.&#x20;
* 정리하자면, 다음과 같다.&#x20;
  * L0 = S -> 시작점&#x20;
  * L1 = L0의 모든 이웃 노드들
  * L2 = L1의 이웃들 중에서 L0에 속하지 않는 노드들
  * L3 = L2의 이웃들 중에서 L1에 속하지 않는 노드들&#x20;
  * Ln = Ln-1 의 이웃들 중에서 Ln-2에 속하지 않는 노드들&#x20;

### 큐를 이용하여 표현하기

1. 방문 여부를 체크하는 배열과 노드들을 넣을 큐를 하나 준비한다.&#x20;
2. 시작점 1을 우선 방문 체크표시하고, 큐에 넣는다.&#x20;
3. 큐가 empty 가 될 때까지 아래를 반복한다.&#x20;
   1. 큐에서 제일 첫번째 노드인 1을 빼고 1과 인접한 노드 중 아직 방문 표시가 안된 2, 3 노드를 큐에 넣는다.
   2. 다시 제일 첫번째 노드인 2를 뺀다. 그리고 2와 인접한 노드 중에서 아직 방문 표시가 안된 4, 5를 큐에 넣는다.&#x20;
   3. 3을 빼고 -> 7, 8을 넣는다.&#x20;
   4. 4를 뺀다. 인접한 노드 중 방문표시가 안된 노드가 없으므로 아무것도 하지 않는다.&#x20;
   5. 5을 뺀다. 아직 방문 안한 6을 넣는다.&#x20;
   6. 7을 뺀다.&#x20;
   7. 8을 뺀다.&#x20;
   8. 그리고 마지막으로 6을 뺀다.&#x20;
   9. 큐가 비었음을 확인하고, 노드 방문순서를 출력한다. 1-> 2-> 3-> 4-> 5-> 7-> 8-> 6

<figure><img src="../../../.gitbook/assets/image (1) (6) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (38) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (11) (3) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### 코드로 구현하기

* 그래프 G와 출발점 S가 주어진다.&#x20;
* 먼저 방문체크용 배열과 큐 Q를 생성하고 S를 큐에 넣는다. S를 방문표시한다.&#x20;
* 큐가 비어있지 않는 동안 아래를 반복한다.&#x20;
  * 큐에서 요소 u를 하나 뽑고&#x20;
  * u 요소와 인접한 v를 방문표시한 뒤, 큐에 넣는다.&#x20;

<figure><img src="../../../.gitbook/assets/image (15) (4).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## BFS로 최단경로 구하기

<figure><img src="../../../.gitbook/assets/image (2) (9) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 에지를 경로로 간주한다.&#x20;
* 출발점 S로부터 레벨 1인 L1까지의 길이는 당연히 1이다. S와 인접한 레벨 1은 거리가 1이기 때문이다.&#x20;
* 나머지 노드들에 대해서도 같은 방식이 적용되므로, 당연히 s에서 Li 에 속한 노드까지의 최단 경로의 길이는 i이다.&#x20;
* 구현하기&#x20;
  * 입력 : 방향 혹은 무방향 그래프 G=(u, v), 그리고 출발노드 s 가 주어진다.&#x20;
  * 출력 : 모든 노드 v에 대해서&#x20;
    * d\[v] = s로부터 v까지의 최단 경로의 길이 = 에지의 개수&#x20;
    * 𝛑\[v] = s로부터 v까지의 최단경로상에서 v의 직전 노드 = predecessor &#x20;
* 시간복잡도
  * 모든 노드는 방문 여부를 체크한 뒤, 큐에 넣어지기 때문에 n번 while 문을 순회하게 된다.&#x20;
  * 그리고 모든 노드가 두 번씩 구현되므로, 시간 복잡도는 O(n+m) 으로 표현되게 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (37) (1) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (10) (7) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## BFS 트리

* BFS 트리에서는 S부터 V까지 가는 최단 경로를 알 수 있다.&#x20;
* 어떤 에지도 2개의 layer 를 건너가지 않는다. 동일 레이어의 노드를 연결하거나 혹은 1개의 레이어를 건너간다.&#x20;

<figure><img src="../../../.gitbook/assets/image (24) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## 최단경로 출력하기&#x20;

* 살펴본 바와 같이, BFS 는 단순히 그래프의 모든 노드를 방문할 뿐만 아니라, 각 노드들 간의 최단 경로 및 predecessor 까지 알 수 있다.&#x20;
* 최단경로를 출력해볼까.&#x20;
  * S를 출발점, V를 임의의 노드라고 보았을 때, 만약에 s=v인 경우, 시작점이 곧 목적지가 되므로, 최단 경로는 자기 자신인 s를 출력하면 된다.&#x20;
  * 만약에 직전노드인 𝛑 \[v] = null 이라면, 출발점이거나 서로 연결되지 않았거나 한 경우인데, 이때, 출발점은 이미 위에서 걸러졌으므로, 서로 연결되지 않은 노드라고 볼 수 있겠다.&#x20;
  * 위의 케이스를 모두 통과했다면, 재귀함수를 통해 표현할 수 있다.&#x20;

<figure><img src="../../../.gitbook/assets/image (20) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



* BFS 에서는 그래프가 disconnected 이거나 방향 그래프라면, 모든 노드가 방문되지 않을 수 있다.&#x20;
* 이 때는 BFS 를 여러번 반복해서 모든 노드를 방문해야한다.&#x20;
  * 방문되지 않은 임의의 노드 v부터 시작하여 모든 노드가 방문될때까지 BFS(G, v) 를 반복한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (22) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

