---
description: 깊이 우선 순회
---

# DFS, Depth First Search

## 깊이 우선 순회

<figure><img src="../../../.gitbook/assets/image (13) (1) (2) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 막다른 길에 다다랐을 때, 다시 되돌아가는 지점, 순서를 기억한다. 되돌아가는 노드는 그 노드가 처음 도착할 때 어디서부터 왔는지에 따라 결정된다.&#x20;
* 결국 거슬러 올라가서 출발점인 1번노드까지 가면, 1번 노드에서 더이상 방문할 곳이 없다면, 종료한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (12) (1) (3) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 구현하기

* DFS는 보통 stack 을 이용하여 구현한다.&#x20;
* 먼저 임의의 노드 v에 대하여 방문 체크를 한다.&#x20;
* 이 노드 v에 대하여 인접한 노드 중에서 아직 방문하지 않은 노드 u를 DFS(G, u) 진행한다.&#x20;
* 아래처럼 재귀로 호출할 경우, 원래 노드에 방문했을 때 온 방향으로 자연스럽게 돌아갈 수 있다. 재귀의 경우, 함수가 종료되면, 호출된 그 지점으로 다시 되돌아가므로.&#x20;

<figure><img src="../../../.gitbook/assets/image (6) (8) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 모든 노드 방문하기

* 그래프가 disconnected 되었거나 방향그래프라면 DFS 로도 다 방문하지 못할 수도 있다.&#x20;
* 따라서 반복적으로 DFS 를 호출해주어야 한다.&#x20;
* 인접행렬로 구현했을 경우, 시간복잡도는 O(n^2)이 된다.&#x20;
  * 모든 노드의 개수 n에 대하여 n번 방문해야하므로, n^2이 된다.&#x20;
* 인접리스트로 구현할 경우, 시간복잡도는 O(n+m)이다.&#x20;
  * 모든 노드의 개수 n에 대하여 반복없이 m번 검사를 해야하기 때문.&#x20;

<figure><img src="../../../.gitbook/assets/image (32) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

