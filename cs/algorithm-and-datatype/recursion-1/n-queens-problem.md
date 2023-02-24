# N Queens problem

## 문제&#x20;

* 상하좌우 대각선까지 이동할 수 있는 말인 퀸을 체스판에 둔다고 하자. N개의 퀸이 체스판에서 서로 공존하기 위해서 놓을 수 있는 방법을 말해라.&#x20;



## 풀이

### 힌트

* 되추적기법 Back Tracking
  * 문제를 풀다가 막다른 길에 다다를 때, 가장 최근에 했던 결정을 번복하며 다시 풀어가는 방법
  * 상태공간트리를 깊이 우선적으로 탐색하여 해를 찾는 알고리즘&#x20;
  * 상태공간트리 : 찾는 해를 반드시 포함하는 트리
    * 해가 존재한다면, 반드시 트리 내부의 노드로 존재한다는 것. 트리를 체계적으로 탐색하면 해를 구할 수 있다.&#x20;
* back tracking 알고리즘을 구현하는 두 가지 방법&#x20;
  * recursion 을 이용하는 방법 : 가장 간단하고 명료하다.&#x20;
  * stack 자료구조를 이용하는 방법&#x20;



### 풀이

<figure><img src="../../../.gitbook/assets/image (36) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4077?tab=curriculum">https://www.inflearn.com/course/알고리즘-강좌/unit/4077?tab=curriculum</a></p></figcaption></figure>

* 상태공간트리를 활용한 백트래킹 기법을 사용한다. 상태공간트리 내부에 반드시 답이 존재한다.
* 재귀함수를 호출하는 것을 이 상태공간트리의 노드에 도착한 것으로 생각을 해본다. 이때, 노드에 도착하고나서 가장 먼저 해야할 것은 해당 노드가 더 깊게 들어가볼 필요가 있는지 아닌지를 체크하는 것이다. 만약에 이미 다른 노드와의 비교에서 충돌했다면, 더 깊게 들어가볼 필요도 없는 것이므로 다음으로 넘어가면 된다.&#x20;
* 적용할 알고리즘을 정리해보면 다음과 같은 흐름을 지닌다.&#x20;
  * 위의 상태공간트리에서 level 을 현재노드, 1번에서 level 까지 어떻게 말이 놓였는지는 배열로 표현한다고 해보자.&#x20;
    * cols\[i] = j 의 경우, i번째 말이 j번째에 놓였다는 것을 의미한다.&#x20;
  * 충돌의 조건을 정의한다.&#x20;
    * 1\) 같은 노드 번호를 가질 때
      * 어차피 각 레벨에선 하나의 노드만 놓을 수 있기 때문에 레벨 내부에서는 생각하지 않아도 된다.&#x20;
      * 같은 column 에 위치할 때 = 노드의 값이 같다.
    * 2\) 대각선 상에서 마주칠때&#x20;
      * 하나의 배열 int\[] a 에 a\[level] = node 형태로 저장된다고 생각하면, 이전 level 인 i에 대해 아래와 같은 식이 성립한다.&#x20;
        * level - i = | a\[i] - a\[level] |&#x20;
        * 두 노드 중 어느것이 더 앞서있는지 모르기 때문에 절대값을 구해야한다.&#x20;
  * 충돌의 조건을 통과한 경우 true, 통과 못한 경우 false 를 반환한다.&#x20;
    * level = N 인 경우, 모든 말이 놓였다는 의미이므로 true 를 반환한다. 최종 성공!&#x20;
  * 그 이외의 경우는 1부터 N까지 순회하며 level+1 번째 행에 말을 놓은 뒤, 위의 조건을 검색하기 위해 recursion 을 호출한다. recursion 에서 true 를 반환하면, true 를, 아닐 경우, false 를 반환한다.&#x20;

&#x20;

<figure><img src="../../../.gitbook/assets/image (37) (1) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4077">https://www.inflearn.com/course/알고리즘-강좌/unit/4077</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (2) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4077">https://www.inflearn.com/course/알고리즘-강좌/unit/4077</a></p></figcaption></figure>



```java
package practice.algorithm.recursion;

/**
 * N Queens
 * */
public class NQueens {
    private static final int N = 4;
    private static final int[] cols = new int[N+1];    //놓인 말의 위치 행과 열 cols[i] = j

    public static void main(String[] args) {
        queens(0);
    }

    private static boolean queens(int level) {
        if (!isPromising(level)) {
            return false;
        }

        if (level == N) {
            for (int i = 1; i <= N; i++) {
                System.out.println("(" +  i + ", " + cols[i] + ")");
            }
            return true;
        }

        for (int i = 1; i <= N; i++) {
            cols[level + 1] = i;
            if (queens(level+1)) {
                return true;
            }
        }
        return false;
    }

    private static boolean isPromising(int level) {
        for (int i = 1; i < level; i++) {
            if (cols[i] == cols[level]) {
                return false;
            }

            if (level - i == Math.abs(cols[i] - cols[level])) {
                return false;
            }
        }
        return true;
    }

    /**
     * (1, 2)
     * (2, 4)
     * (3, 1)
     * (4, 3)
     * */

}
```

