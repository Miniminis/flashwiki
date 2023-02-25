# Deque

## dequeue

* 큐 2개를 겹쳐놓은 것과 같다. (= double ended queue = dequeue)&#x20;
* 양쪽에서 데이터의 입출력이 모두 가능한 자료구조
* 연속적인 메모리 기반으로 하는 시퀀스 컨테이너
* 선언 이후 크기를 줄이거나 늘릴 수 있는 가변적 크기를 갖음
* 스택과 큐의 특성을 모두 지니고 있어서 둘 다로도 활용가능하다.&#x20;
* 구현 메소드&#x20;
  * addFirst&#x20;
  * offerFirst&#x20;
  * addLast/add
  * offerLast&#x20;
  * removeFirst
  * pollFirst
  * removeLast
  * pollLast&#x20;
  * remove
  * poll
  * getFirst
  * peekFirst
  * getLast
  * peekLast
  * peek
  * removeFirstOccurrence&#x20;
  * removeLastOccurrence&#x20;
  * element
  * addAll
  * push
  * pop
  * remove&#x20;
  * contain
  * size&#x20;
* 스택과 큐와 차이점&#x20;
  * 스택, 큐
    * rear 가 다음 요소가 삽입될 위치를 가리킨다.&#x20;
    * 앞, 뒤로만 접근 가능하다.&#x20;
  * 덱
    * rear가 마지막 요소 자체를 가리킨다.&#x20;
    * index 를 이용해서 중간 접근이 가능하다.&#x20;
* 시간복잡도&#x20;
  * 삽입과 삭제에 O(1)&#x20;
* 구현
  * 양방향 연결 리스트&#x20;



## 참고&#x20;

* [java dequeue 클래스 설명](https://haenny.tistory.com/365)&#x20;

