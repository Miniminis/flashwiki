# Stack

## stack

* 나중에 들어간 것이 먼저 나오는 구조&#x20;
* 선입후출, LIFO, last in first out
* 입구와 출구가 동일한 형태로 스택을 시각화
  * 예) 박스쌓기, 접시쌓기 등
* 예시
  * 그래프의 깊이 우선 탐색(DFS)에서 사용한다.&#x20;
  * 시스템 해킹에서 버퍼오버플로우 취약점을 이용한 공격을 할 때, 스택 메모리의 영역에서 한다.&#x20;
  * 컴퓨터 사칙연산 계산에서 후위표기법
  * 인터럽트처리, 수식의 계산, 서브루틴의 복귀 번지 저장 등에 쓰인다.&#x20;
  * 백트래킹, 인터넷 사용기록 보관
  * 브라우저의 뒤로가기
  * 실행취소
  * 재귀함수
  * 역순문자열 - 문자열 거꾸로 뒤집기&#x20;
* 장단점
  * top 를 통해 접근해서 데이터 접근, 삽입, 삭제가 빠르다.&#x20;
  * top 위치 이외의 데이터에 접근할 수 없다. 그래서 탐색에는 모든 요소를 꺼내서 진행해야한다.&#x20;
* 시간 복잡도&#x20;
  * 삽입과 삭제 : O(1)
  * 탐색 : O(n)&#x20;
* 구현&#x20;
  * `java.util.Stack` import 하면 바로 사용 가능함&#x20;
* 지원 매소드
  * push : 삽입
  * peek : 최상단 원소 출력
  * isEmpty, empty : 스택이 비어있다면 true, 아니면 false
  * pop : 삭제&#x20;

## 예시

```java
package practice.algorithm.stackqueue;

import java.util.Stack;

public class StackPractice {
    public static void main(String[] args) {
        Stack<String> stack = new Stack<>();

        stack.push("h");
        stack.push("e");
        stack.push("l");
        stack.push("l");
        stack.push("o");
        System.out.println("stack = " + stack);

        System.out.println("stack.empty() = " + stack.empty());

        System.out.println("stack.peek() = " + stack.peek());
        System.out.println("stack = " + stack);

        System.out.println("stack.pop() = " + stack.pop());
        System.out.println("stack.pop() = " + stack.pop());
        System.out.println("stack = " + stack);

        while(!stack.empty()) {
            System.out.println("stack.pop() = " + stack.pop());
        }
        System.out.println("stack = " + stack);
        System.out.println("stack.isEmpty() = " + stack.isEmpty());
    }
    
    /*
    stack = [h, e, l, l, o]
    stack.empty() = false
    stack.peek() = o
    stack = [h, e, l, l, o]
    stack.pop() = o
    stack.pop() = l
    stack = [h, e, l]
    stack.pop() = l
    stack.pop() = e
    stack.pop() = h
    stack = []
    stack.isEmpty() = true
    */
}


```



