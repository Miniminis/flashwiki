# Queue

## queue

* 먼저 들어온 데이터가 먼저 나가는 형식의 자료구조
* 선입선출, FIFO, first in first out
* 입구와 출구가 모두 뚫려있는 터널과 같은 형태로 시각화
  * 예) 은행대기줄
* 한 쪽 끝은 front로 정하여 삭제 연산만 수행한다.&#x20;
* 다른 쪽 끝은 rear로 정하여 삽입 연산만 수행한다.&#x20;
* 그래프의 넓이 우선 탐색 (BFS) 에서 주로 사용된다.&#x20;
* 컴퓨터 버퍼에서 주로 사용된다. 마구 입력되었으나 처리를 못할 때, 일단 버퍼 큐에 일렬로 대기를 시켜놓는다.&#x20;
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
* 참고
  * [https://coding-factory.tistory.com/602](https://coding-factory.tistory.com/602)

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



