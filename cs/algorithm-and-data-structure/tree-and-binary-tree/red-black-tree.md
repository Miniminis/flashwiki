# Red-Black Tree

## 배경

* 이진 검색 트리에서 최악의 경우, 트리의 높이가 노드의 개수인 n과 같아질 수 있으므로, 최악의 시간복잡도가 O(n)이 될 수 있다.&#x20;
* 이런 문제를 개선하기 위해서 보다 균형잡힌 트리 구조인 레드 블랙 트리 구조를 생각해보게 되었다.&#x20;



## 개념

<figure><img src="../../../.gitbook/assets/image (9) (7).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 이진탐색트리의 일종이다.&#x20;
* 균형잡힌 트리 : 높이가 logn
* search, insert, delete 연산을 최악의 경우에도 O(logn) 시간을 지원하게 된다.&#x20;
* 각 노드는 하나의 키, 왼쪽자식, 오른쪽 자식, 부모노드의 주소를 저장하고 있다. 이진검색트리의 경우, 부모는 굳이 저장하지 않는 경우가 일반적이었는데, 레드-블랙 트리의 경우는 부모 노드의 주소를 저장하는 것이 더 편하므로 저장한다.&#x20;
* 자식 노드가 존재하지 않을 경우는 nil 노드라고 부르는 특수한 노드가 있다고 가정한다.&#x20;
* 따라서 모든 리프노드는 nil 노드가 되고 모든 값이 있는 노드들은 내부 노드로 간주된다.&#x20;
* 루트의 부모도 nil 노드라고 가정하고, 노드들은 내부 노드와 nul 노드로 분류된다.&#x20;

## 조건

레드블랙트리를 충족하기 위해서는 다음과 같은 조건을 충족해야한다.&#x20;

1. 각 노드는 red 이거나 black 이고,&#x20;
2. 루트 노드는 black 이며,&#x20;
3. 모든 리프노드는 black 이고&#x20;
4. red 노드의 부모와 자식노드들은 전부 black 이고, (즉, red 노드는 연속되어 등장하지 않고)
5. 모든 노드에 대해서 그 노드들로부터 자손인 리프노드에 이르는 모든 경로에는 동일한 개수의 black 노드가 존재한다. &#x20;

## 높이&#x20;

* 노드 x의 높이 h(x) 는 자신으로부터 리프노드까지 가는 가장 긴 경로에 포함된 엣지의 개수이다.&#x20;
* 노드 x의 블랙 높이 bh(x) 는 x로부터 리프노드까지의 경로상의 블랙 노드의 개수이다. (노드 x자신은 불포함한다.)&#x20;
  * nil 노드를 포함한다.&#x20;
* 높이가 h인 노드의 블랙 높이는 bh >= h/2&#x20;
  * 조건 4번에 의해서 red 노드는 연속해서 나올 수 없으므로
* <mark style="background-color:yellow;">노드 x를 루트로 하는 임의의 서브트리는 적어도 2^bh(x) - 1개의 내부 노드를 포함한다. (수학적 귀납법에 의해)</mark>&#x20;
* <mark style="background-color:yellow;">n개의 내부노드를 가지는 레드블랙트리의 높이는 2log(n+1) 이하이다.</mark>&#x20;
  * n >= 2^bh -1 >= 2^h/2 -1 이므로, 여기서 bh 와 h는 각각 루트노드의 블랙높이와 높이이다.&#x20;

## Left and Right rotation

<figure><img src="../../../.gitbook/assets/image (4) (1) (5).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* bst 에서 한 노드에 대해서 부분적으로 노드의 구조를 수정하는 것이다.&#x20;
* 시간 복잡도는 O(1) 이며, 이진탐색트리의 특성을 유지한다.&#x20;
* 위의 경우, 베타만 x에 합해진다. 이렇게 변경하더라도 이진탐색트리의 특성이 유지되는 것에 주목하자.&#x20;
  * 원래 베타는 x보다는 크고, y보다는 작은 특성이 있었는데, 위와 같이 구조가 바뀌어도 전혀 문제가 없다!&#x20;

<figure><img src="../../../.gitbook/assets/image (16) (5).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## Search

* binary search tree 와 일치한다. &#x20;

## insert

<figure><img src="../../../.gitbook/assets/image (7) (1) (4).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 위반될 가능성이 있는 조건들&#x20;
  * 2번째 조건 : 루트 노드는 블랙이어야 한다.&#x20;
    * 만약 원래 빈 트리였다면, 새로 입력한 노드가 레드이므로 위반될 가능성이 있다.&#x20;
    * 그냥 레드를 블랙으로 바꾸면 된다.&#x20;
  * 4번째 조건 : z의 부모 p\[z] 가 red 이면 위반이다.&#x20;
* 결국 문제가 되는 것은 4번 조건을 위반할 경우! &#x20;
* 해결방법&#x20;
  * 반복문을 돈다. 삽입하는 노드 z는 red 노드이다.&#x20;
  * 삽입할 노드 z와 z의 부모노드를 쌍으로 위로 계속 올려준다.&#x20;
  * 두 부모노드 p\[z]가 black 이 되면 종료한다. 조건 2가 위반일 경우 (즉, 루트 노드까지 타고타고 올라간 경우) z를 블랙으로 바꿔주고 종료한다.&#x20;

### 위반 조건 해소하기

케이스를 6가지로 나누어서 살펴보려고 한다. 이 때, 1-3번케이스는 부모노드가 조상노드의 왼쪽 자식인 경우이고, 4-6번 케이스는 오른쪽 자식인 경우이다.&#x20;



#### case 1. 삼촌이 red 인 경우&#x20;

* 나의 부모 A와 삼촌인 D를 모두 블랙으로 바꾸어주고, 조상노드 C를 레드로 바꾸어준다.&#x20;
* 최악의 경우, 이때 조상의 조상노드가 이미 레드여서 조상의 조상 p\[c] 와 c가 같은 레드 색을 갖게 되는 경우인데, 이 경우가 바로 위에서 말한 레드 트리를 한 쌍으로 루트까지 계속 올려주는 경우를 말한다.&#x20;
* 루트까지 가게되는 경우가 최악이지만, 그 경우에도 간단하게 루트 노드의 색만 바꾸어주면 해결된다. &#x20;

<figure><img src="../../../.gitbook/assets/image (44) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

#### case 2, 3. z의 삼촌이 black 인 경우

<figure><img src="../../../.gitbook/assets/image (47) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* case 2. z가 부모의 오른쪽 자식인 경우
  * p\[z] 에 대해서 left rotation 을 해서 경우 3로 바꾸어준다.&#x20;
* case 3. z가 부모의 왼쪽 자식인 경우
  * 할아버지를 중심으로 right rotation 을 한다.&#x20;
* black height, red-red 문제 모두 해결되었다.&#x20;

#### case 4 \~ 6 : case 1, 2, 3과 대칭적이므로 생략한다.&#x20;



### 위반조건 해소 코드 - RB insert fixup&#x20;

* 반복실행 : p(z) != null && color(p(z)) == red&#x20;

<figure><img src="../../../.gitbook/assets/image (22) (4).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### insert 의 시간복잡도&#x20;

* BST 에서의 insert : O(logn)&#x20;
* RB-INSERT-FIXUP
  * 경우 1에 해당할 경우, z가 2레벨 상승한다.&#x20;
  * 경우 2-3에 해당할 경우, O(1)&#x20;
  * 따라서 트리의 높이에 비례하는 시간복잡도를 갖게 된다.&#x20;
* 즉, insert 의 시간복잡도는 O(logn)&#x20;

<figure><img src="../../../.gitbook/assets/image (37) (1) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

## delete&#x20;

* 역시 보통의 BST 에서처럼 delete 한다.&#x20;
* 실제로 삭제된 노드 y가 red 였으면 종료한다.&#x20;
* y가 black 인 경우, RB-DELETE-FIXUP 을 호출한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (49) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

### 위반 케이스에 대한 예외처리 - RB-DELETE-FIXUP(T, x)&#x20;

* 여기서 x값은 nil 노드일 수 있다. 삭제된 노드 y의 자식노드가 없었음을 의미한다.&#x20;
* 만약 x값이 red 라면, 해당 값만 black 으로 바꾸어주면 되니까 쉽게 해결이 가능해진다. &#x20;
* 발생할 수 있는 예외는 세 가지이다.
  * 2번 조건 : y가 루트였고, x가 red 인 경우, 위반하게 된다.&#x20;
  * 4번 조건 : p\[y]와 x가 모두 red 일 경우, 위반이다.&#x20;
  * 5번 조건 : 원래 y를 포함했던 모든 경로는 이제 black 노드가 하나 부족하게 된다.&#x20;
    * 노드 x에 extra black 을 부여해서 일단은 조건 5를 만족한다.&#x20;
    * 노드 x는 double black 혹은 red & black&#x20;

### 아이디어&#x20;

* extra black 을 트리의 위쪽으로 올려보낸다.&#x20;
* x가 red & black 상태가 되면, 그냥 black 노드로 만들고 끝내버린다.&#x20;
* x가 루트가 되면 그냥 extra black 을 제거한다. (루트가 블랙을 1개 갖고 있건 2개 갖고 있건 상관없다)&#x20;
* loop invariant&#x20;
  * x는 루트가 아닌 double black 노드이고&#x20;
  * w를 x의 형제노드라고 한다.&#x20;
  * x자체가 double black 노드이기 때문에 w는 반드시 존재해야한다. (아니면 black height 가 성립되지 않는다)&#x20;



## RB delete fixup &#x20;

* 총 8가지 케이스로 해결한다. 케이스 1-4 와 케이스 5-8 은 대칭적.&#x20;
* 케이스 1-4는 x가 부모의 왼쪽에 있는 경우이고, 케이스 5-8은 x가 부모의 오른쪽에 있는 경우를 말한다.&#x20;

#### 케이스 1. w가 red 인 경우

<figure><img src="../../../.gitbook/assets/image (45) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* w가 red 이므로 w의 자식들은 black 일수밖에 없고,&#x20;
* w를 black 으로, p\[x] 를 red 로 바꾼 뒤에,&#x20;
* p\[x] 에 대해서 left rotation 을 적용한다.&#x20;
* x의 새로운 형제 노드는 원래 w의 자식노드가 된다. 따라서 black 노드가 이다.&#x20;
* 이 경우, 케이스 1-> 케이스 2, 3, 4로 변경되었다.&#x20;

#### 케이스 2. w가 black이고 w의 자식들도 black 인 경우

<figure><img src="../../../.gitbook/assets/image (40) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* x의 extra-black 을 뺏고, w를 red 로 바꾸어준다.&#x20;
* p\[x]에게 뺏은 extra black 을 넘겨준다. 그러면 B가 black 이 된다.&#x20;
* p\[x]를 새로운 x 로 삼고 계속 진행한다.&#x20;
  * 만약 p\[x]가 black이었다면, 새로운 double black 노드가 되어서 x로 삼고 반복 수행한다. &#x20;
* 만약 경우 1에서 이 경우에 도달했다면, p\[x] 는 red 였고, 따라서 새로운 x는 red & black 이 되어서 종료되게 된다.&#x20;
  * 경우 1에서 왔다면 경우 2를 처리하고 바로 종료된다.&#x20;

#### 케이스 3. w가 black이고 w의 왼쪽 자식이 red 인 경우

<figure><img src="../../../.gitbook/assets/image (56) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* w에 대해서 right rotation 을 한번 적용하여 w를 red로, w의 왼자식을 black 으로 바꾸는 전략이다.&#x20;
* x의 새로운 형제w는 오른쪽 자식이 red 가 된다. 이는 4번 케이스이다.&#x20;

#### 케이스 4. w가 black, w의 오른쪽 자식이 red 인 경우&#x20;

<figure><img src="../../../.gitbook/assets/image (14) (1) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* w의 색을 현재 p\[x]의 색으로 바꾼다.&#x20;
* p\[x]를 black 으로, w의 오른 자식을 black 으로 바꾼다.&#x20;
* p\[x]에 대해서 left-rotation 을 적용한다.&#x20;
* x의 extra-black 을 제거하고 종료한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (5) (1) (4) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (30) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### 정리

<figure><img src="../../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### 시간복잡도

* BST 에서의 DELETE O(logn)&#x20;
* RB-DELETe-FIXUP : O(logn)&#x20;
