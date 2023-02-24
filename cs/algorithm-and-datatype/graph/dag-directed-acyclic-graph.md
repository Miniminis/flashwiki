---
description: 방향그래프
---

# DAG, Directed Acyclic Graph

## DAG&#x20;

* 방향 그래프에서는 1-> 2로 갈 수 있다고 해서 2->1 로의 이동을 보장할 수는 없다.
* DAG는 방향 사이클이 없는 그래프를 의미한다.&#x20;
  * 예를 들어 집짓는 작업의 우선순위를 보자
    * 땅사기, 기초공사하기, 기둥 세우기, 벽돌 쌓기, 도배하기, 전기공사하기
    * <도배를 하기 전에 전기공사를 해야한다> 처럼 순서가 존재한다.&#x20;
    * 이 경우, A (전기공사)-> B (도배)처럼 방향을 정의할 수 있다.
  * 작업의 우선순위에서 방향 사이클이 만들어 진다면, 순회할 수 있게 되면서 작업이 영원히 완료되지 않게 된다. 즉, 성립될수가 없다.  &#x20;

<figure><img src="../../../.gitbook/assets/image (31) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 위상정렬 1

* DAG의 노드들을 순서화하여 정렬하는 것을 말한다. 이때 모든 에지들은 왼쪽 -> 오른쪽 순서대로 나열해야한다. 먼저 작업하는 것의 순서대로 나열하는 것!&#x20;

<figure><img src="../../../.gitbook/assets/image (35) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### 구현하기

* 한 노드에 대해서 들어오는 엣지를 incoming, 나가는 엣지를 outgoing 이라고 하고,&#x20;
* 들어오는 엣지의 개수를 indgree, 나가는 엣지의 개수를 outdegree 라고 한다.&#x20;
* indegree = 0 의 의미는 결국, 현재 작업 전에 반드시 해야하는 선행작업이 없다는 것이다.&#x20;
* 그렇다면, indegree=0 인 노드는 시작점이 될 것이다. 아래의 경우, a 혹은 g가 될 수 있다.&#x20;
* a부터 한다고 하면, a를 제거하고, a에서 나가는 엣지를 제거한다.&#x20;
* 다음 번 indegree=0 인 노드 g 역시 제거한 뒤, g에서 나가는 엣지를 제거한다.&#x20;
* 이렇게 되면, 노드 d가 이번에는 indgree=0 이 되므로, d를 제거하고 d에서 나가는 엣지를 제거한다.&#x20;
* 다음은 f, e, c, b 등의 순서로 사라질 것이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (14) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 문제점 1. indegree=0 인 노드가 존재하지 않으면
* 문제점 2. indegree=0 을 어떻게 찾을 것인가
* 문제점 3. 코드 상에서 실제로 정점과 진출간선을 어떻게 제거할 것인가



## 위상정렬 2

* 위상정렬 알고리즘 1이 제일 첫번째 요소를 먼저 찾는 알고리즘이라면, 위상정렬 2 알고리즘은 제일 마지막 요소를 먼저 찾는 알고리즘이다.&#x20;
* 노드에서 outgoing edge 가 없는 경우라면, 위상정렬 선상에서 마지막 요소일 확률이 크다.&#x20;
* 해당 작업보다 나중에 해야할 작업이 없다는 의미이므로!&#x20;

<figure><img src="../../../.gitbook/assets/image (4) (5) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (42) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (8) (8) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (29) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (21) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

