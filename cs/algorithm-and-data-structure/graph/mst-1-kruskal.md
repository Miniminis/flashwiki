---
description: MST 모든 노드들을 잇는 최소비용의 경로를 구하는 알고리즘
---

# MST 1 - Kruskal 의 알고리즘

## 개념

* **엣지들을 가중치의 오름차순으로 정렬한 뒤, 엣지들을 그 순서대로 하나씩 선택해가는 알고리즘이다.**&#x20;
* **단, 이미 선택된 엣지들과 사이클을 형성하면 선택하지 않는다.**&#x20;
* **n-1개의 엣지가 선택되면 종료한다.**&#x20;



## 그림으로 살펴보기

* 먼저 가중치가 1인 엣지를 선택한다.&#x20;
* 1인 엣지가 또 없다면, 2인 엣지를 선택한다. 2개 이상일 경우 아무거나 먼저 선택해도 상관없다.&#x20;
* 그다음 큰 4를 선택하고&#x20;
* 다음 순서인 6을 선택하려고 하는데, 6을 선택하는 순간, 사이클이 만들어지게 된다. 따라서 6은 건너뛴다.&#x20;
* 가중치가 7인 엣지로 이동한다. 첫번째 7은 선택했으나, 두번째 7은 사이클을 만들 수 있으므로 스킵한다.&#x20;
* 그 다음 엣지인 8, 역시 사이클을 만들지 않는 1개만 선택한다.&#x20;
* 가중치가 9인 엣지까지 선택했다면, 그만한다. 이미 엣지의 개수가 노드의 개수보다 1개 적은 8개가 되었기 때문이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (10) (1) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (22) (1) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (26) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 작동원리

* kruskal 알고리즘의 임의의 한 단계를 생각해본다.&#x20;
* A를 현재까지 알고리즘이 선택한 엣지의 집합이라고 하고, A를 포함하는 MST 가 존재한다고 가정해보자.&#x20;

<figure><img src="../../../.gitbook/assets/image (9) (1) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 사이클 검사&#x20;

* 초기상태에는 선택된 엣지가 없다.&#x20;
* 각각의 연결요소를 하나의 집합으로 표현한다.&#x20;
* 서로 다른 집합에 속한 원소를 연결하는 엣지라면 사이클을 만들지 않는다. 즉, 같은 집합 내의 두 개의 노드를 연결하는 엣지는 사이클을 만든다.&#x20;
* 예를 들어보자. 아래의 그림에서는 아래와 같은 식으로 진행되는 것이다.&#x20;
  * {h}, {g} -> 서로 다른 집합이므로 합쳐준다.&#x20;
  * {i}, {c} -> 서로 다른 집합이므로 또 합쳐준다.&#x20;
  *

<figure><img src="../../../.gitbook/assets/image (54) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (2) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 구현하기&#x20;

<figure><img src="../../../.gitbook/assets/image (4) (1) (5) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### 집합에 대한 표현

* 서로소인 집합들을 표현해야한다. = disjoint 한 sets 들을 표현해야한다 -> find(u), union(u, v)&#x20;
* union-find problem 이라고 부르기도 한다. 일반적으로는 아래와 같이 풀어간다.&#x20;
  * 각 집합을 하나의 트리로 표현한다.&#x20;
  * 집합의 각 요소들이 트리의 노드가 된다. 누가 루트이고 누가 부모이든지 상관이 없다.&#x20;
  * 이곳에서는 자식노드부터 부모노드까지 타고 올라가는 식으로 연산을 할 예정이기 때문에 트리의 각 노드는 자식노드가 아닌 부모노드의 주소를 가지게 된다.&#x20;
  * 각각의 노드는 하나의 부모노드 주소만 가지기 때문에 일반적인 하향식 트리 구조보다 구현하기가 훨씬 간단하다.&#x20;
    * 상향식 트리구조이기 때문에 1차원 배열로 표현할 수 있다.&#x20;

<figure><img src="../../../.gitbook/assets/image (60) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### Find-Set(v)

* 자신이 속한 트리의 루트를 찾는다. 서로 다른 두 트리의 루트가 같다면, 같은 집합에 속해있는 것으로 간주한다.&#x20;
* 이를 위해 findSet(v)에서는 루트 노드를 반환하는 로직이 필요하다. 부모노드를 표시한 배열 p에서 현재 index 값과 p\[index] 값이 같아야 루트노드이므로, 해당 위치까지 반복하여 재귀함수를 호출해준다.&#x20;
* 이때 시간복잡도는 트리의 높이인 h만큼 소요될 수 있으니, o(h)가 되고, h는 다시 최악의 경우, 각 트리가 일렬로 나열되어 하나의 자식이 하나의 부모와 연결된 구조로 쌓여있을 경우, n이 될 수 있다.&#x20;
  * O(h) -> O(n)

<figure><img src="../../../.gitbook/assets/image (55) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### union(u, v)

* 두 트리를 합하는 연산인 union(u, v)에서는 우선 서로 다른 두 트리의 루트 x, y를 각각 찾는다.&#x20;
* 그리고는 한쪽의 루트를 다른 쪽 트리의 부모로 지정하면, 두 트리가 합쳐지게 된다.&#x20;
* 이때 시간복잡도는 두 트리의 루트를 찾는데 걸리는 시간 O(h)가 소요되고, 루트 노드를 합치는데 드는 시간은 O(1) 이 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (25) (2) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### weighted union

* union(u, v)의 연산을 좀 더 효율적으로 할 수 있는 방법을 찾아본다. 우선 시간복잡도에 가장 큰 영향을 미치는 것은 트리의 높이 h이므로, 결국 관건은 최종 트리의 높이를 최소한으로 유지하는 것이다.&#x20;
* 이를 위해 weighted union 방법을 이용해보자.  이 방법은 두 집합을 union 할 때, 작은 트리의 루트를 큰 트리의 루트의 자식으로 만들어버린다. 작은 트리가 큰 트리로 흡수되어야 시간 복잡도에 가장 큰 영향을 미치는 트리의 높이 h 값에 큰 영향이 없기 때문이다.&#x20;
  * 큰 트리의 높이가 이미 작은 트리보다 큰 상태라면, 높이가 전혀 변하지 않을 수도 있다.&#x20;
* 이를 위해서는 각 트리의 크기(노드의 개수)를 계속 카운트하고 있어야 하는데, 시작부터 각 트리의 사이즈를 1로 초기화한 배열을 정의하고, 매번 업데이트 될 때마다 해당 사이즈를 업데이트 시켜주어야 한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (7) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



이때, 어떤 트리의 높이가 h라는 말은 다시 말하자면 아래와 같이 해석될 수 있다.&#x20;

* 최초에 높이가 1인 상태로 모두 시작한다.&#x20;
* 어떤 트리의 높이가 2가 되었다는 말은 높이가 1에서 2로 상승했고,  노드의 개수도 2배 증가하였다는 것을 의미한다.&#x20;
* 트리의 높이가 3이 되었다는 말은 높이가 2에서 3으로 증가했고, 노드의 개수는 다시 2배만큼 증가하였다는 의미한다.&#x20;
* 이를 반복하자면 결국 높이가 h라는 말은 최소한 1부터 h번 union 연산을 진행했다는 뜻이고, 각  연산마다 노드의 개수가 2배가 되었다는 것을 의미한다.&#x20;
* 따라서 높이가 h라면, 해당 트리의 노드 개수는 적어도 2^h보다는 많아진다는 의미이고 결국 노드의 개수 n을 가지는 트리에 대하여 높이는 logn 이 되고 이 높이는 곧 시간복잡도이므로 O(logn)이 된다.&#x20;



### path compresion&#x20;

* findSet(g)에서 적용할 수 있는 방법&#x20;
* 루트를 찾는 과정에서 현재 노드의 부모의 부모를 나의 루트로 삼는 연산을 반복한다면 전체 트리의 길이가 절반으로 줄어들게 된다.&#x20;
* 또한 우선 루트 e를 찾고, 전체 노드에 대해서 모두 부모를 e로 바꾸는 방법도 있다. 그러면 트리의 길이가 눈에 띄게 줄어들고 시간복잡도 면에서도 훨씬 효율적이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (1) (9).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## Kruscal 알고리즘의 시간복잡도

* 결국, weighted union + path compression = WUPC 방법을 적용하여 kruscal 알고리즘을 사용한다면 시간복잡도는 원래에 비해 눈에 띄게 줄어들게 된다.&#x20;
* M번의 union-find 연산의 총 시간복잡도는 O(N+Mlog\*N)이다.&#x20;
  * log\*N 은 log(log(log(logN)))... 을 반복했을 때, 1이 되는 횟수를 의미한다.&#x20;
  * 여기서 log\*N의 표를 보자면, 많아봐야 log\*N은 5이하로 거의 선형시간대를 기록하게 된다.&#x20;
  * 따라서 한번의 find-union 은 결국 O(1) 시간 정도로 거의 선형시간 알고리즘이 된다.&#x20;
* 결국 시간복잡도를 보면,&#x20;
  * 최초로 초기화하는 시간이 O(1)&#x20;
  * 트리(집합)을 만드느라 전체 순회를 하는 시간이 O(n)&#x20;
  * 그리고 소팅하는 시간이 O(mlogm)&#x20;
  * 두 번의 findSets 와 한번의 Union 연산이 일어나지만, 각각의 시간복잡도가 선형시간대이므로, 결국 O(m) 의 시간이 소요된다.&#x20;
* 위의 연산과정에서 최종적으로 시간복잡도에 가장 큰 영향을 미치는 것은 결국 O(mlogm)이고 이는 다시 표현하자면 O(mlogn)으로 표현할 수 있다.&#x20;
  * 엣지의 개수 m은 많아봐야 n(n-1)/2 개를 넘을 수 없으므로, mlogm = mlogn^2 = 2mlogn = mlogn &#x20;

<figure><img src="../../../.gitbook/assets/image (53) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (36) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 관련문제 풀어보기

* [https://www.acmicpc.net/workbook/view/1899](https://www.acmicpc.net/workbook/view/1899)
* [https://www.acmicpc.net/workbook/view/12965](https://www.acmicpc.net/workbook/view/12965)
* [https://www.acmicpc.net/workbook/view/4289](https://www.acmicpc.net/workbook/view/4289)

