# heap sort

## 시간복잡도&#x20;

* 최악의 경우, 시간 복잡도 O(nlogn)&#x20;
  * merge 역시 시간복잡도가 O(nlogn) 이지만, 추가적인 배열공간이 필요했다.&#x20;
  * 하지만 heap sort 는 주어진 배열 그 자체 내에서 수행되기 때문에 추가적인 메모리가 필요없다.&#x20;
* sort in place : 추가 배열이 불필요하다.&#x20;
* 이진 힙(binary heap) 자료구조를 사용한다.&#x20;

## heap/binary Heap

* heap 의 정의&#x20;
  * complete binary tree 이면서,&#x20;
  * heap property 를 만족해야한다.&#x20;
    * max heap property : 부모는 자식보다 크거나 같다.&#x20;
    * min heap property : 부모는 자식보다 작거나 같다.&#x20;
* complete binary tree?&#x20;
  * binary tree : 최대 2개의 노드를 가지는 트리형태&#x20;
  * full binary tree : 모든 레벨에 노드들이 꽉 차있는 형태&#x20;
  * complete binary tree : 마지막 레벨을 제외하면 완전히 꽉 차있고, 마지막 레벨에는 가장 오른쪽부터 연속된 몇 개의 노드가 비어있을 수 있다.&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (26) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## heap 의 표현

* heap 이 완전이진트리의 구조를 갖추어야한다고 해서 별도로 특별한 구현이 필요한 것은 아니다.&#x20;
* heap 의 경우, 완전이진트리의 구조이기 때문에 다른 트리와는 다르게 1차원 배열만으로도 표현이 가능하다. &#x20;
* 루트부터 레벨별로 인덱스에 규칙을 부여할 수 있다.&#x20;
  * 레벨 i 가 1부터 차례대로 늘어난다고 가정하면, 아래와 같은 규칙으로 인덱스가 정해질 수 있다.&#x20;
  * 루트노드 : a\[1]
  * a\[i]의 부모  = a\[i/2]
  * a\[i]의 왼쪽 자식 = A\[2i]
  * a\[i]의 오른쪽 자식 = A\[2i+1]

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 기본연산 max-heapify&#x20;

<figure><img src="../../../.gitbook/assets/image (14) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 어떤 트리의 루트에 대하여 루트의 왼쪽 서브 트리와 오른쪽 트리가 그 자체로 각각 heap 을 만족하지만, 루트 자체만 heap property 를 만족하지 않을 때, 이 전체 트리를 heap 으로 만드는 연산을 바로 max-heapify 라고 한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (8) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

1. 루트값과 루트의 자식 중 더 큰값을 서로 교체해준다.&#x20;
   1. 루트의 자식 중 더 큰 값과 교체하기 때문에 루트가 내려간 서브트리가 아닌 반대쪽 서브트리는 여전히 힙 구조를 유지하게 된다. (이미 두 값 중, 더 큰 값과 루트가 교체되기 때문에 교체된 노드와 나머지 노드에 대해서는 이슈가 없다는 것이다)&#x20;
   2. 이제 루트 단의 서브트리만 고민해주면 된다.&#x20;
2. 루트가 가진 서브트리에 대해서 heap property를 충족할 때까지 위의 1번 연산을 반복해준다.&#x20;



<figure><img src="../../../.gitbook/assets/image (28) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (41).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (6).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## 시간복잡도&#x20;

* h = O(logn)&#x20;



## heap sort&#x20;

* heap sort 를 하기 위해서는 우선 데이터가 저장되어있는 1차원 배열을 heap 구조로 해석해야한다.&#x20;
* complete binary tree 를 heap 으로 바꾸는 과정을 진행
  * 제일 아래 레벨부터 leaf 노드가 아닌 첫번째 노드부터 차례대로 heapify 를 진행한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (2) (9).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (4) (5) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 시간복잡도는 O(n) 이 된다.&#x20;
  * 이 시간은 힙을 만드는데 드는 시간이고,&#x20;
  * 사실은 힙 정렬을 하는데에는 추가로 nlogn 만큼의 시간이 든다.&#x20;
  * 결론적으로는 nlogn 만큼의 시간이 소요되므로, 힙을 만드는데 드는 시간에 대해서는 크게 강조를 않하는 경향이 있다.&#x20;

### 정렬할 배열을 힙으로 만들기&#x20;

<figure><img src="../../../.gitbook/assets/image (17) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 시작점 : n/2 이므로 arr\[4] = 1이 된다.&#x20;
* 시작점 arr\[4]의 자식은 arr\[8]=12, arr\[9]=11 이고, heapify 를 진행하면, 1과 12의 위치가 바뀌게 된다.&#x20;
* 그 다음 인덱스인 arr\[3]=6에 대하여 자식 arr\[6]=15, arr\[7]=3 과 비교를 하면, 역시 6과 15의 자리가 바뀌게 된다.&#x20;
* 위의 과정을 반복하면, 힙이 만들어진다.&#x20;

### 만든 힙을 기준으로 정렬 진행하기&#x20;

힙의 조건을 이용해야한다. 기본 max 힙에서는 배열의 제일 첫번째 요소가 최대값이 된다. (부모가 자식보다 커야하기 때문)&#x20;

1. 우선 주어진 데이터 배열을 힙으로 해석한다.&#x20;
   1. 힙 구조에 맞게 배열의 데이터를 재구성한다.&#x20;
2. 힙에서 최대값(루트)를 가장 마지막 값과 바꾼다.&#x20;
   1. 힙에서의 최대값은 배열의 가장 첫번째 요소이다.
3. 힙의 크기가 1 줄어든 것으로 간주한다. 즉, 가장 마지막 값은 힙의 일부가 아닌 것으로 간주한다.&#x20;
   1. 오름차순 정렬에 있어서 제일 마지막 값을 완성한 것으로 보면 된다.&#x20;
   2. 이 값에 대해서는 이미 정렬이 된 것이므로 더이상 신경쓰지 않는다.&#x20;
4. 루트노드에 대해서 heapify(1) 한다.&#x20;
   1. 15를 제외한 나머지 값은 다시 heap 구조로 재정비된다.&#x20;
5. 하나의 노드가 남을때까지 2-4번을 반복한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (22).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (13) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



## 코드로 보기

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

