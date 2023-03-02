---
description: 트리와 이진트리
---

# Tree and Binary tree

## 트리 tree

<figure><img src="../../../.gitbook/assets/image (34) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 계층적인 구조를 표현할 때 사용하는 자료구조이다.&#x20;
* 그래프의 일종이라고 볼 수도 있다. 유입되는 라인이 1개이고, 방향성을 가지지 않는 그래프라고도 말할 수 있다. &#x20;
* 사용
  * 가계도, 조직도, 디렉토리와 서브디렉토리 구조 등

### 구조&#x20;

* 노드, 엣지 (링크, 브랜치라고도 부름)
* 제일 최상단의 노드를 루트 노드라고 부른다.
* 부트리, subtree : 전체 트리의 일부를 자른 트리&#x20;
* 레벨 : 루트로부터의 거리
* 높이 : 전체 트리의 높이. 서로 다른 레벨의 개수

### 관계

* 부모 - 자식 : 상대적인 개념. 상하관계 &#x20;
* 형제 : 부모가 같은 노드들&#x20;
* 리프노드 : 자식이 없는 노드
* 조상 - 자손 : 부모의 부모와의 관계&#x20;

### 특징

* 노드의 개수가 n개인 어떤 트리는 반드시 n-1개의 엣지를 갖는다.&#x20;
  * 노드의 개수가 4개이면, 반드시 3개의 연결선, 엣지를 갖는다.&#x20;
* 루트에서 어떤 노드로 가는 경로는 유일하다. 또한 임의의 두 노드간의 경로도 유일하다. (같은 노드를 두번 이상 방문하지 않는다는 전제 하에!)&#x20;

### 종류&#x20;

* tree : 계층구조를 갖는 자료구조
* binary tree : 이진트리. 최대 2개의 자식을 갖는다.&#x20;
* binary search tree
  * 이진탐색트리
  * 루트를 기준으로 루트보다 작으면 왼쪽, 크면 오른쪽에 위치한다.&#x20;
* complete binary tree
  * 마지막 레벨을 제외하고는 모든 노드가 꽉차있는 트리
  * 꽉 차있지 않은 마지막 레벨에서도 왼쪽부터 오른쪽으로 노드가 쌓여야한다는 특징이 있다.&#x20;
* full binary tree
  * 모든 노드가 0개 혹은 2개의 자식노드를 갖는 트리&#x20;
* perfect binary tree
  * 모든 노드가 2개의 자식을 갖는 트리
  * 모든 리프(맨 마지막 노드)들이 모두 같은 레벨에 있다.&#x20;
  * 트리의 높이가 h일 때, 2^h -1 개의 노드를 갖는다.&#x20;
* 참고
  *

      <figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p><a href="https://cs.stackexchange.com/questions/32397/is-there-a-difference-between-perfect-full-and-complete-tree">Complete Binary Tree vs. Full Binary Tree vs. Perfect Binary Tree</a> </p></figcaption></figure>



## 이진트리, binary tree

* 각 노드는 최대 2개의 자식을 갖는다.&#x20;
* 일반적으로 왼쪽 자식과 오른쪽 자식의 위치가 정해진다.&#x20;
  * 부모와 자식의 구성이 같아도, 왼쪽 자식과 오른쪽 자식이 서로 다른 경우는 서로 다른 이진 트리로 간주한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (6) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### expression tree

* 수식의 계산을 트리 구조로 표현한 것 &#x20;

<figure><img src="../../../.gitbook/assets/image (10) (7).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### huffman code

<figure><img src="../../../.gitbook/assets/image (24) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 데이터를 압축하거나 인코딩하는 기본적인 알고리즘 중 하나
* 가변 길이를 인코딩할 때, 예를 들면, a부터 z까지의 알파벳으로 구성된 텍스트 파일 인코딩할 때, 해당 파일의 각 알파벳을 적절하게 인코딩 할 때, 파일 길이를 최소로 인코딩하는 곳에 사용된다.&#x20;
  * 즉, 파일 압축을 최적화하는 곳에 사용된다. &#x20;
* 위의 그림은 각 알파벳별로 주어진 이진코드로 압축하는 과정&#x20;



## full and complete binary tree

<figure><img src="../../../.gitbook/assets/image (43) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* perfect binary tree :  모든 레벨에서 노드가 꽉꽉 차있는 트리
  * 높이가 h인 perfect binary tree 는 2^h-1개의 노드를 가진다.&#x20;
* full binary tree : 자식을 2개만 갖거나 아예 갖지 않는 트리 &#x20;
* complete binary tree : 마지막 레벨을 제외하고는 노드가 가득 차있어야 하며, 비어있는 노드는 오른쪽에서부터 연속된 몇개의 노드만 해당된다.&#x20;
  * 힙은 기본적으로 complete binary tree 여야 한다.&#x20;
* 노드가 N개인 full or complete 이진 트리의 높이는 O(logN) 이 된다.&#x20;
* 일반적인 이진트리는 높이가 최악의 경우 N이 될 수 있다.&#x20;

## 이진 트리의 표현

&#x20;&#x20;

<figure><img src="../../../.gitbook/assets/image (52) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 연결구조 (연결리스트)로 표현한다.&#x20;
* 각 노드에 현재 자신의 데이터 필드 + 왼쪽 자식노드 + 오른쪽 자식 노드 + 부모노드의 주소를 저장한다.&#x20;
  * 하지만 부모노드의 경우는 반드시 필요한 경우가 아니면 보통은 저장하지 않는다.&#x20;
  * 트리는 보통 위에서부터 아래로 내려가는 구조로 탐색하기 때문에 부모 주소가 크게 필요하지 않기 때문인데, 만약 부모의 주소를 기억하는 것이 중요한 경우라면, 이런 방식으로 저장하는 것이 꼭 필요하다고 할 수 있다.&#x20;
* 첫번째 노드, 즉 루트 노드의 주소는 따로 보관한다.&#x20;



## 이진 트리의 순회 (traversal)&#x20;

* 순회 : 이진 트리의 모든 노드를 방문하는 일&#x20;
* 중순위 순회, inorder  : 왼쪽 -> 루트 -> 오른쪽&#x20;
* 선순위 순회, preorder : 루트 -> 왼쪽 -> 오른쪽 &#x20;
* 후순위 순회, postorder: 왼쪽 -> 오른쪽 -> 루트&#x20;
* 레벨 오더 순회, level-order : (루트가 존재하는) 1레벨 -> 2레벨 -> 3레벨 ... &#x20;

### 중순위 순회 in-order traversal&#x20;

이진 트리를 루트 노드 r, 루트의 왼쪽 부트리인 T(L), 오른쪽 부트리인 T(R) 로 나누어서 생각한다.&#x20;

1. 먼저 왼쪽 부트리를 inorder로 순회하고&#x20;
2. r을 순회하고&#x20;
3. T(R) 을 inorder 로 순회한다.&#x20;

기본적으로 recursive 하다.&#x20;

* 방문 순서는 : 2 -> 3 -> 5 -> 5 -> 7 -> 8
* 2 -> 3 -> 5 -> 5-> 7 -> 8&#x20;

<figure><img src="../../../.gitbook/assets/image (27) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (50) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* x의 왼쪽을 재귀함수로 호출하고, 현재의 노드 x를 출력한 뒤, 노드 x의 오른쪽 서브트리를 재귀로 다시 호출한다.&#x20;

### 선순위순회 pre order와 후순위 순회 post order&#x20;

* pre order : 먼저 루트 방문 -> 왼쪽 서브 트리 -> 오른쪽 서브트리
  * 위의 예에서 보면, 5 -> 3 -> 2 -> 5 -> 7 -> 8
* post order : 왼쪽 서브 트리 -> 오른쪽 서브 트리 -> 루트 방문
  * 위의 예시에서 보면, 2 -> 5 -> 3 -> 8 -> 7 -> 5

<figure><img src="../../../.gitbook/assets/image (13) (1) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### expression tree 의 순회

<figure><img src="../../../.gitbook/assets/image (28) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* inorder 순회하면 아래와 같이 출력된다.&#x20;
  * x + y \* a + b / c&#x20;
  * 하지만 이렇게 출력될 경우, 곱셈이 먼저 실행되므로 의도했던 식과 달라지게 된다.&#x20;
* 해결 - 각 부트리를 순회할 때, 시작과 종료 시점에 괄호를 추가하면 다음과 같이 출력될 수 있다.&#x20;
  * (x+y) \* ((a + b)/c)&#x20;
* postorder 순회하면 후위 표기식이 출력 된다.&#x20;
  * x y + a b + b / \*&#x20;

### level-order 순회&#x20;

* 이진 트리를 레벨 순서로 방문하는 전략을 말한다.&#x20;
* 동일 레벨에서는 왼쪽에서 오른쪽 순서로 방문하며,&#x20;
* 큐를 이용하여 구현한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (46) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 방문 순서는 아래와 같다.&#x20;
  * 루트 노드이자 시작점인 3을 큐에 넣는다.&#x20;
    * 큐 : 3
  * 큐에서 요소를 뽑는다. 제일 첫 요소인 3의 자식 노드를 다시 큐에 넣는다.&#x20;
    * 방문 : 3
    * 큐 : 1, 5&#x20;
  * 큐에서 1을 뽑고, 다시 1의 자식노드인 0과 2를 큐에 넣는다.&#x20;
    * 방문 : 3, 1&#x20;
    * 큐 : 5, 0, 2
  * 큐가 empty 가 될 때까지 위의 과정을 반복한다.
    * 방문 : 3, 1, 5, 0, 2, 4, 6, 7, 8

<figure><img src="../../../.gitbook/assets/image (23) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>
