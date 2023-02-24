# Queue

## queue

* 먼저 들어온 데이터가 먼저 나가는 형식의 자료구조
* 선입선출, FIFO, first in first out
* 입구와 출구가 모두 뚫려있는 터널과 같은 형태로 시각화
  * 예) 은행대기줄
* 한 쪽 끝은 front로 정하여 삭제 연산만 수행한다. -> enqueue
* 다른 쪽 끝은 rear로 정하여 삽입 연산만 수행한다.  -> dequeue
* 데이터 삽입 전에는 full 인지 확인, 삭제 전에는 empty 인지 확인해야한다.&#x20;
* 장단점
  * 데이터의 접근, 삽입, 삭제가 빠르다.&#x20;
  * 중간에 있는 데이터에 대한 접근이 불가하다.&#x20;
* 예시&#x20;
  * 그래프의 넓이 우선 탐색 (BFS) 에서 주로 사용된다.&#x20;
  * 컴퓨터 버퍼에서 주로 사용된다. 마구 입력되었으나 처리를 못할 때, 일단 버퍼 큐에 일렬로 대기를 시켜놓는다.&#x20;
  * 프린트의 대기열에도 사용된다.&#x20;
  * 은행업무, 콜센터 고객 대기시간, 프로세스 관리
* 구현&#x20;
  * 리스트로 구현은 가능하나, 시간복잡도가 증가한다.
  * Queue 자료구조를 지원함: LinkedList
  * `java.util.Queue`, `java.util.LinkedList` import 하면 사용할 수 있다.&#x20;
* 지원 매소드
  * add : 추가
  * offer : 추가
  * poll : 첫번째 요소 제거, queue 비어있다면 null
  * remove : remove 첫번째 요소 제거
  * clear : 큐 전체 제거
  * peek : 맨 첫번째 요소 조회

## 예시

```java
package practice.algorithm.stackqueue;

import java.util.LinkedList;
import java.util.Queue;

public class QueuePractice {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        queue.offer("h");
        queue.offer("e");
        queue.offer("l");
        queue.add("l");
        queue.add("o");
        System.out.println("queue = " + queue);

        System.out.println("queue.isEmpty() = " + queue.isEmpty());
        System.out.println("queue.poll() = " + queue.poll());
        System.out.println("queue.remove() = " + queue.remove());
        System.out.println("queue.peek() = " + queue.peek());
        System.out.println("queue = " + queue);

        queue.clear();
        System.out.println("queue = " + queue);
        System.out.println("queue.isEmpty() = " + queue.isEmpty());

        /**
         * queue = [h, e, l, l, o]
         * queue.isEmpty() = false
         * queue.poll() = h
         * queue.remove() = e
         * queue.peek() = l
         * queue = [l, l, o]
         * queue = []
         * queue.isEmpty() = true
         * */
    }
}


```



## 우선순위 큐 - priority queue

* 선형 큐와는 다르게 우선순위가 높은 요소일수록 먼저 삭제된다.&#x20;
* 만약에 우선순위가 같다면, 삽입된 순서를 따른다.&#x20;
* 삽입 및 삭제시, 우선순위에 따라서 요소들을 정렬해야한다. 따라서 힙 자료구조로 구현된다. -> [heap](../heap/ "mention")참고&#x20;
* 예시&#x20;
  * 응급실에서 응급환자가 우선순위가 높아서 먼저 진료받는 환자가 뒤로 밀리는 경우&#x20;
* 시간 복잡도 : 힙으로 구현한다면,
  * 삽입과 삭제 : O(logN)
  * 우선순위가 가장 높은 요소를 탐색할 때에는 O(1)&#x20;



## 원형 큐 - circular queue, ring buffer

* 큐는 사이즈가 고정되어있고 데이터 삽입과 삭제시 나머지 요소들은 이동하지 않는다. 그러다가 결국 index 가 마지막을 가리키게 되면, 앞쪽 공간이 텅텅 비어있더라도 그것을 제대로 활용하지 못하게 된다.&#x20;
* 원형 큐는 문제를 해결하기 위해서 현재의 요소들을 앞으로 재배치하는 별도의 작업을 진행한다.&#x20;
* ```
  function enqueue() {
    // ...
    rear = (rear + 1) % queue_size;
  }

  function dequeue() {
    // ...
    front = (front + 1) % queue_size;
  }
  ```
  * 위와 같이 큐 전체 사이즈가 5이고, 현재 rear 가 마지막 인덱스인 4를 가리키고 있다면, 다시 enqueue 하는 과정에서 전체 인덱스 범위를 넘어서는 5가 되어버린다. 이때, 이 5를 다시 전체 큐의 사이즈만큼 나누게 되면, (4+1) % 5 = 0이 된다. 인덱스가 다시 처음으로 돌아가게 되는 것이다.&#x20;





## 참고&#x20;

* [https://velog.io/@sisofiy626/자료구조-2.-스택Stack과-큐Queue-덱Deque](https://velog.io/@sisofiy626/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-2.-%EC%8A%A4%ED%83%9DStack%EA%B3%BC-%ED%81%90Queue-%EB%8D%B1Deque)
* [https://coding-factory.tistory.com/602](https://coding-factory.tistory.com/602)

