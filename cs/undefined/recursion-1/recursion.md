# recursion 응용 - 미로찾기

## 순환적으로 생각하기

현재 위치에서 출구까지 가려면 아래와 같이 두 가지 중 하나의 조건을 만족해야한다.&#x20;

1. 현재 위치가 이미 출구이거나&#x20;
2. 현재 위치와 이웃한 셀에서 현재 위치를 지나치지 않고 출구까지 가는 경로가 있거나

&#x20;즉 여기에도 순환적인 개념이 바로 드러나는데, 현재 위치에서 출구까지 가는 방법에 대해 현재 위치와 이웃한 셀에서 출구까지 가는 방법을 조사하는 것으로부터 알 수 있다는 점이다.&#x20;



```java
package practice.algorithm.recursion;

/**
 * 미로찾기
 * */
public class Maze {
    private static final int N = 8;
    private static int[][] maze = {
            {0, 0, 0, 0, 0, 0, 0, 1},
            {0, 1, 1, 0, 1, 1, 0, 1},
            {0, 0, 0, 1, 0, 0, 0, 1},
            {0, 1, 0, 0, 1, 1, 0, 1},
            {0, 1, 1, 1, 0, 0, 1, 1},
            {0, 1, 0, 0, 0, 1, 0, 1},
            {0, 0, 0, 1, 0, 0, 0, 1},
            {0, 1, 1, 1, 0, 1, 0, 0},
    };

    private static final int PATHWAY_COLOR = 0;
    private static final int WALL_COLOR = 1;
    private static final int BLOCKED_COLOR = 2;
    private static final int PATH_COLOR = 3;

    public static void main(String[] args) {
        printMaze();
        findMazePath(0, 0);
        printMaze();
    }

    private static void printMaze() {
        for (int i = 0; i < maze.length; i++) {
            for (int j = 0; j < maze.length; j++) {
                System.out.print(maze[i][j]);
            }
            System.out.println();
        }
        System.out.println();
    }

    public static boolean findMazePath(int x, int y) {
        //범위를 벗어나는 경우
        if (x < 0 || y < 0 || x >= N || y >= N) {
            return false;
        }

        //통로 색이 아닌 경우
        if (maze[x][y] != PATHWAY_COLOR) {
            return false;
        }

        //마지막 출구 좌표인 경우
        if (x == N-1 && y == N-1) {
            maze[x][y] = PATH_COLOR;
            return true;
        }

        maze[x][y] = PATH_COLOR;
        if (findMazePath(x-1, y) || findMazePath(x+1, y)
                || findMazePath(x, y-1) || findMazePath(x, y+1)) {
            return true;
        }

        maze[x][y] = BLOCKED_COLOR;
        return false;
    }

    /**
     * 00000001
     * 01101101
     * 00010001
     * 01001101
     * 01110011
     * 01000101
     * 00010001
     * 01110100
     *
     * 30000001
     * 31101101
     * 30010001
     * 31001101
     * 31112211
     * 31333121
     * 33313331
     * 21112133
     * */
}
```

