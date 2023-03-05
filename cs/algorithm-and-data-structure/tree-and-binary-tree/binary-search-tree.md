---
description: 이진 검색 트리
---

# Binary Search Tree

* 일반적으로 트리 자료구조를 사용할 때에는 계층적인 데이터들을 표현하고자 할 때 사용한다.&#x20;
* 하지만 이진 검색 트리의 경우는 다르다. 이진 검색트리의 경우, 여러 개의 키를 저장하고 있고 insert, search, delete 등의 연산을 지원하는 자료구조를 의미하며, dynamic set, dictionary, **search structure** 라고도 불린다.&#x20;
  * 각각의 연산은 키에 대해서 삽입, 탐색, 삭제를 진행한다.&#x20;
* 가장 기본적으로는 배열이나 연결리스트를 사용하여 표현할 수 있다.&#x20;
  * 하지만 아래의 표에서 볼 수 있듯, 배열이나 연결리스트가 정렬되거나 정렬되지 않은 경우를 모두 통틀어서 보더라도, O(n)의 시간복잡도를 피할 수는 없다.&#x20;

<figure><img src="../../../.gitbook/assets/image (18) (1) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 어떻게 하면 효율적으로 구현할 수 있을까

* 위에서 본 것과 같이, 여러개의 데이터 sets 를 정렬 혹은 정렬되지 않은 배열이나 연결리스트를 사용할 경우, insert, search, delete 중 어느 하나는 O(n)의 시간복잡도를 갖게 된다.&#x20;
* 시간복잡도 O(n) 보다 효율적으로 구하는 방법을 고민하던 중 나온 방법이 바로 이진 탐색트리 (binary search tree), 레드-블랙 트리, AVL-트리 등 트리에 기반한 자료구조를 이용하는 방법이나 direct address table, 해쉬 테이블 등 테이블 구조를 이용하는 방법이다.&#x20;

## 검색 트리&#x20;

* dynamic set 을 트리 형태로 구현한다.&#x20;
* 일반적으로 insert, search, delete 연산이 트리의 높이(height)에 비례하는 시간복잡도를 가지게 된다.&#x20;
* 이진검색트리(binary search tree), 레드 블랙 트리 (red-black tree), b-tree 등 다양한 종류가 있다.

## 이진 검색 트리, binary search tree, bst

* 이진 트리이면서&#x20;
* 각 노드에 하나의 키를 저장한다.&#x20;
* **각 노드 v에 대해서 그 노드의 왼쪽 부트리에 있는 키들은 key\[v]보다 작거나 같고, 오른쪽 부트리에 있는 키들은 같거나 크다.**&#x20;

<figure><img src="../../../.gitbook/assets/image (12) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### heap 과의 비교

* heap
  * 부모는 자식보다 작다는 조건이 성립해야하며,&#x20;
  * 반드시 complete binary tree 구조를 갖추어야 한다.&#x20;
* bst&#x20;
  * 왼쪽 자식은 부모보다 작거나 같고, 오른쪽 자식은 부모와 같거나 크다는 조건을 만족해야 한다.&#x20;
  * 이진 트리 구조이기만 하면 된다.&#x20;

### search

&#x20;

<figure><img src="../../../.gitbook/assets/image (19) (4).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 탐색하고자 하는 숫자 x가 주어진다. 13이라고 해보자.&#x20;
* 루트 노드의 숫자는 항상 주어지므로, 15부터 탐색을 시작한다.&#x20;
* 만약 루트노드 = x 이면 찾은 것이므로 그냥 반환하면 되고, 아니라면 찾고자 하는 x와 루트를 비교하면서 탐색한다.&#x20;
* x 13은 루트인 15보다 작으므로 왼쪽 트리를 탐색한다.&#x20;
* 반복하면 아래와 같은 순서로 탐색된다.&#x20;
  * 15 -> 6 -> 7 -> 13&#x20;

<figure><img src="../../../.gitbook/assets/image (21) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 루트 노드 x, 찾는 값 k가 주어진다.&#x20;
* 루트노드가 null 이거나 k와 같다면, 바로 x를 반환한다.&#x20;
* 루트 노드값과 비교하여 찾는 값이 작다면 왼쪽 트리, 크다면 오른쪽 트리를 탐색한다.&#x20;
* 시간복잡도는 O(h), 여기서 h는 트리의 높이이다.&#x20;
* 만약 이진트리의 구조가 최악이 될 경우, 트리의 높이 h는 노드의 개수인 n까지 될 수 있다.&#x20;

### 최소값/최대값 찾기&#x20;

* 이진 검색 트리에서 최소값은 루트에서 출발해서 계속 왼쪽으로 내려가 더이상 왼쪽이 없는 순간까지 내려가면서 구할 수 있다.&#x20;
* left\[x] 가 null 이 될 때까지 탐색하면 된다. 가장 왼쪽노드!&#x20;
* 시간복잡도는 O(h) 가 된다.&#x20;
* 최대값도 마찬가지로 생각하면 된다. 제일 오른쪽 노드를 탐색하면, 최대값이 된다. 역시 시간 복잡도는 O(h)&#x20;

### Successor 찾기&#x20;

* 노드 x의 successor 란 key\[x] 보다 크면서 가장 작은 키를 가진 노드를 의미한다.&#x20;
* 역시 시간 복잡도는 O(h) 가 된다.&#x20;
* 모든 키들이 서로 다르다고 가정해보자.&#x20;

<figure><img src="../../../.gitbook/assets/image (2) (1) (4).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

3가지 경우로 나누어 생각해본다.&#x20;

1. 노드 x의 오른쪽 서브 트리가 존재할 경우, 오른쪽 서브트리의 최소값이 해가 된다.&#x20;
2. 오른쪽 서브트리가 없는 경우, 어떤 노드 y의 왼쪽 서브트리의 최대값이 x가 되는 그런 노드 y가 x의 successor 가 된다.&#x20;
   1. 즉, 부모를 따라서 루트까지 올라가는 동안, 처음으로 누군가의 왼쪽 자식이 되는 그런 노드를 의미한다.&#x20;
3. 그런 노드 y가 존재하지 않을 경우에는 successor 가 존재하지 않는다고 말할 수 있다. 즉, x가 최대값이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (6) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### Predecessor&#x20;

* 노드 x의 predecessor 란 key\[x]보다 작으면서 가장 큰 키를 가진 노드를 의미한다.&#x20;
* successor 과 반대를 말한다.&#x20;
* 시간복잡도는 역시 O(h) 이다.&#x20;



### insert&#x20;

* 새로운 노드를 이진검색트리에 추가하는 것이다.&#x20;
* 이진검색트리에서 새로운 노드가 추가될 때에는 다른 노드들의 위치는 전혀 건드리지 않는다. 그냥 새로운 노드를 기존의 leaf 자리에 추가한다.&#x20;
* 14 노드를 추가한다고 생각해보자.&#x20;
* 노드는 처음부터 추가하는 것이 아니라 루트노드 15부터 값을 차례대로 비교하면서 내려간다.&#x20;
* 14 < 15 이므로 왼쪽트리로 내려간다.&#x20;
* 6 < 14이므로 오른쪽으로 내려간다.&#x20;
* 같은 방식으로 7 < 14 -> 13 < 14 ..&#x20;
* 결국 13의 오른쪽에 14 노드가 추가된다.&#x20;
* 이를 구현할 때에는 두개의 포인터 x, y를 사용한다.&#x20;
  * x = 15, y = null&#x20;
  * x = 6, y = 15&#x20;
  * x = 7, y = 6
  * x = 13, y = 7&#x20;
  * x = null, y = 13 -> y의 오른쪽에 추가!&#x20;

<figure><img src="../../../.gitbook/assets/image (17) (5).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

이처럼 binary search tree 에서는 노드를 삭제하지 않는 이상 노드들은 순서의 변경 없이 항상 같은 구조를 유지하기 때문에, 큰 이상이 없는 한 가장 먼저 insert 된 노드가 루트 자리를 차지하게 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (33) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 위의 탐색 순서를 코드로 표현한 것이다.&#x20;
* 한 가지 기억할 점은 while 문이 한번도 실행되지 않은 경우에 대한 예외처리이다. y = null 인 경우, 트리 자체가 원래 empty 였다는 것을 의미하므로, 삽입하고자 하는 수 x자체가 그냥 루트가 된다.&#x20;
* 만약에 y가 null 이 아니라면, 반복문이 정상적으로 실행된 것이므로, 원래대로 key\[y] vs. key\[z]를 비교하여, 키 y 보다 크면 오른쪽, 작으면 왼쪽으로 삽입시킨다.&#x20;



### delete&#x20;

* 먼저 삭제할 노드를 찾고 삭제하는 과정이 필요하다.&#x20;

#### case 1. 자식 노드가 없는 경우

* 부모 노드 3의 오른쪽 필드를 null 로 바꿔준다.&#x20;

<figure><img src="../../../.gitbook/assets/image (58) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

#### case 2. 자식 노드가 1개인 경우

* 연결 리스트에서 요소를 삭제하는 것처럼 나의 자식과 부모를 연결시켜주는 것이다.&#x20;
* 나의 자식을 나의 위치로 끌어올리는 것이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (48) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

#### case 3. 자식 노드가 2개인 경우

* 자식 노드가 2개인 경우는 노드 자체를 삭제하지 않고, 노드의 주소값만 삭제한 뒤, 다른 노드의 주소값을 가져온다.&#x20;
* 예를 들어 13을 제거할 경우, 13과 가장 근접한 값을 가져온다면, 다른 노드들이 큰 구조 변경 없이 연산을 수행할 수 있다. 여기서는 15가 될 것이다.&#x20;
* 즉, 삭제하려는 노드의 successor 를 찾아서 그 데이터를 13의 자리로 옮겨오면 된다.&#x20;
* 이렇게 처리할 경우, 원래 삭제 대상이었던 노드 13의 왼쪽 트리인 6 트리의 경우, 아무 문제가 없다. 원래부터 13보다 작았기 때문에 당연이 그의 successor dls 15보다도 작을 것이기 때문이다.&#x20;
* 또한 오른쪽 서브트리인 18트리 역시 불만이 없는데, 왜냐하면, 13의 successor 를 찾느라 오른쪽 트리의 가장 최소값을 가져왔기 때문에, 이는 무조건 18트리의 다른 모든 값들보다 같거나 작은 것을 보장하기 때문이다.&#x20;
* 또 하나 주목할 점은, 교체 대상이 되는 15는 트리의 제일 왼쪽에 위치할 것이기 때문에 반드시 왼쪽 노드가 없다. 때문에 15를 13의 자리로 옮겨두고 나머지 자식 노드들에 대한 처리는 노드가 0개이거나 1개인 경우를 따르면 되므로 간단해진다.  &#x20;

<figure><img src="../../../.gitbook/assets/image (11) (1) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 위에서부터 차례대로 살펴보자.&#x20;
* 만약 삭제하려는 노드 z 의 왼쪽 노드나 오른쪽 노드가 null 이라면, 자식노드가 0이나 1인 경우이고, 삭제하려는 노드 자체가 삭제되는 것이므로, 해당 노드 z를 y라고 한다.&#x20;
* 만약 삭제하려는 노드 z의 왼쪽과 오른쪽이 모두 null 이 아니게 되면, 그의 successor 를 삭제하게 되므로, y를 successor(z) 로 한다.&#x20;
* 그 이후 식부터는 y를 삭제하는 로직을 짠다.&#x20;
* 모든 연산이 끝난 뒤, successor 를 삭제한 case 3의 경우, y의 키값을 z의 키값으로 복사해준다.&#x20;

## 시간복잡도

* 시간복잡도 O(h)&#x20;
* 최악의 경우, 트리의 높이 h =n 일 수 있기 때문에, 최악의 경우, O(n)일 수 있다.&#x20;
* 배열이나 연결리스트를 쓰는 경우와 큰 차이가 없는 것으로 느껴질 수 있지만, 사실은 트리의 높이가 n이 되는 경우는 많지 않다.&#x20;
* 이진 검색 트리를 개선하여 균형잡힌 트리 구조를 갖게 할 수 있다. &#x20;
  * 레드 - 블랙 트리 등&#x20;
  * 키의 삽입이나 삭제시에 추가로 트리의 균형을 잡아줌으로써 높이를 O(logn)으로 유지할 수 있다.&#x20;
