# 행렬 경로 문제

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4126?tab=curriculum">https://www.inflearn.com/course/알고리즘-강좌/unit/4126?tab=curriculum</a></p></figcaption></figure>



## 접근하기

* 어떤 위치 (i, j) 를 두고 보았을 때, 해당 위치까지 오기 위해서는 (i, j-1)을 지나거나 (i-1, j)를 지나야 한다.&#x20;
* 그리고 (i, j-1)이나 (i-1, j)까지는 최선의 방법으로 와야 한다.&#x20;
* 순환식을 세워보면 아래와 같이 세울 수 있을 것이다.&#x20;
  *

      <figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4126?tab=curriculum">https://www.inflearn.com/course/알고리즘-강좌/unit/4126?tab=curriculum</a></p></figcaption></figure>
  * (1, 1) 일 경우에는 해당 위치가 최소값이 된다.&#x20;
  * 만약 j가 1일 경우는, 모든 오른쪽으로 가는 경우의 수는 없고, 반드시 아래쪽으로 가야한다. 때문에 (i-1, 1)까지의 최소값을 구하면 된다.&#x20;
  * i가 1일 경우에도 마찬가지이다. 제일 위쪽 경로가 1이게 되므로, 반드시 (1, 1)에서 출발하면 오른쪽으로 가야한다. 때문에 (1, j-1)까지의 최소합을 구하면 된다.&#x20;
  * 위처럼 특수한 케이스가 아닌 경우, 보통은 (i-1, j), (i, j-1) 까지의   최단경로 중에서 더 작은 값을 고르면, 해당 위치까지의 최단 경로가 된다.&#x20;



## 1. 그냥 풀기

* (1,1) 부터 시작해서 목적지에 도착할 때까지 해당 위치까지의 최단경로 합을 반복해서 구하고 있다. 재귀함수로 표현할 수 있다.&#x20;

```java
package practice.algorithm.dp;

public class MinimumSumPath {
    private static final int[][] m = {
            {0, 0, 0, 0, 0},
            {0, 6, 7, 12, 5},
            {0, 5, 3, 11, 18},
            {0, 7, 17, 3, 3},
            {0, 8, 10, 14, 9}
    };

    public static void main(String[] args) {
        System.out.println(findMinimumPath(4, 4));
    }

    private static int findMinimumPath(int i, int j) {
        if (i == 1 && j == 1) {
            return m[i][j];
        }

        if (i == 1) {
            return findMinimumPath(1, j-1) + m[i][j];
        }

        if (j == 1) {
            return findMinimumPath(i-1, 1) + m[i][j];
        }

        return Math.min(findMinimumPath(i-1, j), findMinimumPath(i, j-1)) + m[i][j];
    }
}
```



## 2. memoization 으로 캐싱하여 풀기

```java
package practice.algorithm.dp;

import java.util.Arrays;

public class MinimumSumPathMemo {
    private static final int[][] m = {
            {0, 0, 0, 0, 0},
            {0, 6, 7, 12, 5},
            {0, 5, 3, 11, 18},
            {0, 7, 17, 3, 3},
            {0, 8, 10, 14, 9}
    };

    private static final int[][] l = new int[m.length][m.length];

    public static void main(String[] args) {
        initializeArrayL();
        System.out.println(findMinimumPath(4, 4));
    }

    private static void initializeArrayL() {
        for (int i = 0; i < l.length; i++) {
            Arrays.fill(l[i], -1);
        }
    }

    private static int findMinimumPath(int i, int j) {
        if (l[i][j] != -1) {
            return l[i][j];
        }

        if (i == 1 && j == 1) {
            l[i][j] = m[i][j];
        } else if (i == 1) {
            l[i][j] = findMinimumPath(1, j-1) + m[i][j];
        } else if (j == 1) {
            l[i][j] = findMinimumPath(i-1, 1) + m[i][j];
        } else {
            l[i][j] = Math.min(findMinimumPath(i-1, j), findMinimumPath(i, j-1)) + m[i][j];
        }

        return l[i][j];
    }

}
```

&#x20;

## 3. bottom-up 방식으로 풀기

* 어디를 bottom 으로 볼 것인가?&#x20;
  * (i, j) 까지의 값을 구할 때, (i-1, j)나 (i, j-1) 의 값은 미리 계산되어있어야 한다.&#x20;
  * 가장 단순하게는 행 우선으로 지그재그 방식으로 계산하면 될 것이다.&#x20;
* 시간복잡도는 당연히 n^2 이 된다. &#x20;

```java
package practice.algorithm.dp;

public class MinimumSumPathBottomUp {
    private static final int N = 4;

    private static final int[][] m = {
            {0, 0, 0, 0, 0},
            {0, 6, 7, 12, 5},
            {0, 5, 3, 11, 18},
            {0, 7, 17, 3, 3},
            {0, 8, 10, 14, 9}
    };

    private static final int[][] l = new int[N+1][N+1];

    public static void main(String[] args) {
        System.out.println(findMinimumPath());
    }

    private static int findMinimumPath() {
        for (int i = 1; i <= N; i++) {
            for (int j = 1; j <= N; j++) {

                if (i == 1 && j == 1) {
                    l[i][j] = m[i][j];
                } else if (i == 1){
                    l[i][j] = m[1][j-1] + m[i][j];
                } else if (j == 1) {
                    l[i][j] = m[i-1][j] + m[i][j];
                } else {
                    l[i][j] = Math.min(l[i-1][j], l[i][j-1]) + m[i][j];
                }

            }
        }

        return l[N][N];
    }

}
```

## tip. 무한대값 이용하여 코드 단순화하기

* 기존 방식의 경우, i-1, j-1 등의 값에 i==1 or j==1 인 경우, 인덱스가 0으로 유효하지 않은 값이 조회될 우려가 있기 때문에, 총 4가지의 케이스로 나누어 코드를 작성하였다. 하지만, i== 0, j==0 일 때를 각각 무한대값으로 초기화를 한다면 비교시에 해당 값이 최소값으로 뽑힐 염려가 없으므로, 2개의 경우로만 나누어서 코드를 작성할 수 있게 된다.&#x20;
* 하지만 자바에서는 int 형에 무한대값을 적용할 수 없으므로, 가장 최대값인 Integer.MAX\_VALUE 를 선언하였다.&#x20;

```java
package practice.algorithm.dp;

public class MinimumSumPathBottomUpTrick {
    private static final int N = 4;

    private static final int[][] m = {
            {0, 0, 0, 0, 0},
            {0, 6, 7, 12, 5},
            {0, 5, 3, 11, 18},
            {0, 7, 17, 3, 3},
            {0, 8, 10, 14, 9}
    };

    private static final int[][] l = new int[N+1][N+1];

    public static void main(String[] args) {
        initializeL();
        System.out.println(findMinimumPath());
    }

    private static void initializeL() {
        for (int i = 0; i < N+1; i++) {
            for (int j = 0; j < N+1; j++) {
                if (i == 0 || j == 0) {
                    l[i][j] = Integer.MAX_VALUE;
                }
            }
        }
    }

    private static int findMinimumPath() {
        for (int i = 1; i <= N; i++) {
            for (int j = 1; j <= N; j++) {

                if (i == 1 && j == 1) {
                    l[i][j] = m[i][j];
                } else {
                    l[i][j] = Math.min(l[i-1][j], l[i][j-1]) + m[i][j];
                }

            }
        }

        return l[N][N];
    }

}
```

## 경로 출력하기

위에서 구한 값들은 목적지까지 도달하는데, 각 방문 칸들의 합을 최소로 하는 경로에서의 최소값을 구한 것이다. 만약 경로 그 자체를 출력하고자 한다면, 추가로 경로 배열을 하나 만들어서 출력해줄 수 있다.&#x20;



