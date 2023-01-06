# heap sort

## 시간복잡도&#x20;

* 최악의 경우, 시간 복잡도 O(nlogn)&#x20;
  * merge 역시 시간복잡도가 O(nlogn) 이지만, 추가적인 배열공간이 필요했다.&#x20;
  * 하지만 heap sort 는 주어진 배열 그 자체 내에서 수행되기 때문에 추가적인 메모리가 필요없다.&#x20;
* sort in place : 추가 배열이 불필요하다.&#x20;
* 이진 힙(binary heap) 자료구조를 사용한다.&#x20;

## Heap, or Binary Heap

* heap 의 정의&#x20;
  * complete binary tree 이면서,&#x20;
  * heap property 를 만족해야한다.&#x20;
    * max heap property : 부모는 자식보다 크거나 같다.&#x20;
    * min heap property : 부모는 자식보다 작거나 같다.&#x20;
* complete binary tree?&#x20;
  * binary tree : 최대 2개의 노드를 가지는 트리형태&#x20;
  * full binary tree : 모든 레벨에 노드들이 꽉 차있는 형태&#x20;
  * complete binary tree : 마지막 레벨을 제외하면 완전히 꽉 차있고, 마지막 레벨에는 가장 오른쪽부터 연속된 몇 개의 노드가 비어있을 수 있다.&#x20;

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (26).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## heap 의 표현

* heap 이 완전이진트리의 구조를 갖추어야한다고 해서 별도로 특별한 구현이 필요한 것은 아니다.&#x20;
* heap 의 경우, 완전이진트리의 구조이기 때문에 다른 트리와는 다르게 1차원 배열만으로도 표현이 가능하다. &#x20;
* 루트부터 레벨별로 인덱스에 규칙을 부여할 수 있다.&#x20;
  * 레벨 i 가 1부터 차례대로 늘어난다고 가정하면, 아래와 같은 규칙으로 인덱스가 정해질 수 있다.&#x20;
  * 루트노드 : a\[1]
  * a\[i]의 부모  = a\[i/2]
  * a\[i]의 왼쪽 자식 = A\[2i]
  * a\[i]의 오른쪽 자식 = A\[2i+1]

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 기본연산 max-heapify&#x20;

<figure><img src="../../../.gitbook/assets/image (14).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 어떤 트리의 루트에 대하여 루트의 왼쪽 서브 트리와 오른쪽 트리가 그 자체로 각각 heap 을 만족하지만, 루트 자체만 heap property 를 만족하지 않을 때, 이 전체 트리를 heap 으로 만드는 연산을 바로 max-heapify 라고 한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

1. 루트값과 루트의 자식 중 더 큰값을 서로 교체해준다.&#x20;
   1. 루트의 자식 중 더 큰 값과 교체하기 때문에 루트가 내려간 서브트리가 아닌 반대쪽 서브트리는 여전히 힙 구조를 유지하게 된다.&#x20;
   2. 이제 루트 단의 서브트리만 고민해주면 된다.&#x20;
2. 루트가 가진 서브트리에 대해서 힙 구조를 충족할 때까지 위의 1번 연산을 반복해준다.&#x20;

