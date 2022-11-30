---
title: <이코테 2021> 3. BFS & DFS
category: algorithm
date: 2022-01-14 06:28:11
---

> 💡 동빈나 님의 [이코테 2021 강의 몰아보기](https://www.youtube.com/watch?v=m-9pAwq1o3w&list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&index=1) 를 보면서 공부한 내용을 정리하고 있습니다. 더 자세한 내용은 **[이것이 취업을 위한 코딩 테스트다 with 파이썬](http://www.yes24.com/Product/goods/93519145) 을 참고해주세요** 😊 학습 도구로는 [리플렛](https://replit.com/) 을 사용하고 있고 원본 소스코드는 동빈님의 [Github](https://github.com/ndb796/python-for-coding-test) 에서 확인할 수 있고  스스로 공부한 소스코드는 [Github](https://github.com/Miniminis/algorithm-study-note-python) 에서 확인할 수 있습니다.

# BFS & DFS
- 대표적인 그래프 탐색 알고리즘
- 탐색이란 많은 양의 데이터 중에서 원하는 데이터를 찾는 과정!
- DFS/BFS 는 코테에서 매우 자주 등장하는 유형임!

## 시작하기 전에
### 스택
- 먼저 들어온 데이터가 나중에 나가는 형식의 자료구조 
- 선입후출
- 입구와 출구가 동일한 형태로 스택을 시각화함
  - 예) 박스쌓기
- 다양한 알고리즘에서 사용됨
- 연산
  - 리스트를 스택 자료구조 구현을 위해 이용할 수 있음
  - 삽입 : stack.append(n)
  - 삭제 : stack.pop()
  - 최상단 원소부터 출력 : print(stack[::-1])
  - 최하단 원소부터 출력 : print(stack)
- 자바
  - stack 자료구조를 이용할 수 있음
  - 삽입 : push(n)
  - 삭제 : pop()
  - 최상단 원소출력 : peek()

### 큐
- 먼저 들어온 데이터가 먼저 나가는 형식의 자료구조
- 선입선출
- 입구와 출구가 모두 뚫려있는 터널과 같은 형태로 시각화 
- 예시) 은행 대기줄
- 연산
  - 리스트를 이용해서 구현은 할 수 있겠지만 시간복잡도가 증가한다. 
  - 따라서 python 에서는 deque() 라이브러리를 사용함
  - 삽입 : append(n)
  - 삭제 : popeleft()
  - 순서대로 출력 : queue
  - 역순으로 바꾸기 : queue.reverse()
- java
  - queue 자료구조를 지원함 : linkedList
  - 삽입 : offer(n)
  - 삭제 : poll()
  - 순서대로 출력 : for loop + q.poll()

### 재귀함수
- 자기자신을 다시 호출하는 함수
- recursive function
- 단순한 형태의 재귀함수 예제
  - `재귀함수를 호출합니다` 를 무한히 출력
  - 어느정도 출력하다가 최대 재귀 깊이 초과 메시지가 출력됨

```text
def recursive_func():
  print("재귀함수를 호출합니다!")
  recursive_func()

recursive_func()

# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# 재귀함수를 호출합니다!
# Traceback (most recent call last):
#   File "main.py", line 8, in <module>
#     recursive_func()
#   File "main.py", line 6, in recursive_func
#     recursive_func()
#   File "main.py", line 6, in recursive_func
#     recursive_func()
#   File "main.py", line 6, in recursive_func
#     recursive_func()
#   [Previous line repeated 992 more times]
#   File "main.py", line 5, in recursive_func
#     print("재귀함수를 호출합니다!")
# RecursionError: maximum recursion depth exceeded while calling a Python object
# exit status 1
```

- 일반적으로 재귀함수는 특정 조건에서 종료될 수 있도록 종료조건을 명시해야한다.

```python
def recursive_func(i):
  if i == 100:
    return
  
  print(i, "번째 재귀함수에서", i+1, " 번째 재귀함수를 호출합니다!")
  recursive_func(i+1)

  print(i, "번째 재귀함수를 종료합니다!")


recursive_func(1)


# 95 번째 재귀함수에서 96  번째 재귀함수를 호출합니다!
# 96 번째 재귀함수에서 97  번째 재귀함수를 호출합니다!
# 97 번째 재귀함수에서 98  번째 재귀함수를 호출합니다!
# 98 번째 재귀함수에서 99  번째 재귀함수를 호출합니다!
# 99 번째 재귀함수에서 100  번째 재귀함수를 호출합니다!
# 99 번째 재귀함수를 종료합니다!
# 98 번째 재귀함수를 종료합니다!
# 97 번째 재귀함수를 종료합니다!
# 96 번째 재귀함수를 종료합니다!
# 95 번째 재귀함수를 종료합니다!
# 94 번째 재귀함수를 종료합니다!
# 93 번째 재귀함수를 종료합니다!
```

#### 팩토리얼 구현문제
- `n! = 1 * 2 * 3  ... * n`
- 수학적으로 0! 과 1! 의 값은 1이다. 
- 이 문제는 1) 반복적으록 구현하는 방법과 2) 재귀함수로 구현하는 방법, 2가지가 있다. 

```python
# BFS & DFS
# 재귀함수
# 팩토리얼 구현예제 
# n! = 1 * 2 * 3  ... * n

## 반복문으로 구현
def loop(i):
  result = 1

  for i in range(1, i+1):
    result *= i
  return result

print(loop(5))  # 120


## 재귀함수로 구현
def self_func(i):
  if i <= 1:
    return 1

  return i * self_func(i-1)

print(self_func(5))  #120
```


#### 최대공약수 계산 (유클리드 호제법) 예제
- 두 개의 자연수에 대해서 최대 공약수를 구하는 대표적인 알고리즘으로 유클리드 호제법이 있다. 
- 유클리드 호제법
  - 두 자연수 A, B 에 대하여 A를 B로 나눈 나머지를 R이라고 할때 
  - A, B의 최대 공약수는 B와 R의 최대 공약수와 같다. 
- 예시) GCD(192, 162)
  - A / B
  - 192 / 162
  - 162 / 30
  - 30 / 12
  - 12 / 6 
  - 6

```python
# 유클리드 호제법으로 최대공약수 구하기
def gcd(a, b):
  if a % b == 0:
    return b
  else:
    return gcd(b, a % b)

print(gcd(192, 162))
# 6
```

#### 재귀함수 사용시 유의사항
- 잘 활용하면 복잡한 알고리즘을 간결하게 만들 수 있다. 
  - 하지만 때떄로 오히려 다른사람이 이해하기 어려운 코드가 될 수 있으니 신중하게 사용해야함
- 모든 재귀함수는 반복문으로 표현 가능 -> 그 반대도 가능
- 재귀 함수가 반복문보다 유리한 경우도 있고, 불리한 경우도 있다. 
- 컴퓨터가 함수를 연속적으로 호출하면 컴퓨터 메모리 내부의 스택에 프레임이 쌓인다. 
  - 스택 사용해야할 때, 구현상 스택 라이브러리 대신에 재귀함수를 이용하는 경우가 많음

## DFS : Depth First Search
- 깊이 우선 탐색
- 깊은 부분을 우선적으로 탐색하는 알고리즘
- 스택 자료구조 혹은 재귀함수를 이용한다. 
- 구체적인 동작과정
  1. 탐색 시작 노드를 스택에 삽입하고 방문처리
  2. 스택의 최상단 노드에 방문하지 않은 인접한 노드가 하나라도 잇으면 그 노드를 스택에 넣고 방문처리
  3. 방문하지 않은 인접 노드가 없으면 스택에서 최상단 노드를 꺼냄
  4. 더이상 2번 과정을 수행할 수 없을때까지 반복한다. 

![](/.gitbook/assets/algorithm-thisiscote-03-1642115544522.png)

```python
# DFS - Deep first search

def dfs(graph, i, visited):
  visited[i] = True
  print(i, end=' ')

  for j in graph[i]:
    if not visited[j]:
      dfs(graph, j, visited)

graph = [
  [],
  [2, 3, 8],
  [1, 7],
  [1, 4, 5],
  [3, 5],
  [3, 4],
  [7],
  [2, 6, 8],
  [1, 7]
]

visited = [False] * 9

print(dfs(graph, 1, visited))
```


## BFS : Breadth First Search
- 너비 우선 탐색
- 그래프에서 가까운 노드부터 우선적으로 탐색하는 알고리즘
- 큐 자료구조를 이용하며 동작 과정은 다음과 같음
  1. 탐색 시작 노드를 큐에 삽입하고 방문처리를 한다. 
  2. 큐에서 노드를 꺼낸 뒤에 해당 노드의 인접노드 중에서 방문하지 않은 노드를 모두 큐에 삽입하고 방문처리를 한다. 
  3. 더 이상 2번의 과정을 수행할 수 없을 때까지 반복한다. 
- 최단거리 구하는 문제에서 자주 사용된다. 
- 동작예시 

![](/.gitbook/assets/algorithm-thisiscote-03-1642137061678.png)


```python
 # BFS - Breadth First Search
from collections import deque

def bfs(graph, i, visited):
  visited[i] = True
  queue = deque([i])

# queue 가 빌때까지 반복
  while queue:
    v = queue.popleft()
    print(v, end=' ')

    for j in graph[v]:
      if not visited[j]:
        queue.append(j)
        visited[j] = True

graph = [
  [],
  [2, 3, 8],
  [1, 7],
  [1, 4, 5],
  [3, 5],
  [3, 4],
  [7],
  [2, 6, 8],
  [1, 7]
]

visited = [False] * 9

print(bfs(graph, 1, visited))
# 1, 2, 3, 8, 7, 4, 5, 6
# 1 2 3 8 7 4 5 6
```


## 음료수 얼려먹기
문제
- N * M 크기의 얼음틀이 있다.
- 구멍이 뚫려있는 부분은 0, 칸막이가 존재하는 부분은 1로 표시된다. 
- 구멍이 뚫려있는 부분끼리 상, 하, 좌, 우로 붙어있는 경우 서로 연결되어 있는 것으로 간주한다. 
- 이때 얼음틀의 모양이 주어졌을 때, 총 아이스크림의 개수를 구하는 프로그램을 작성해라
- 예) 4 * 5 의 얼음틀 -> 아이스크림이 총 3개 
- 연결요소찾기 문제 
- 문제조건
  - 세로길이, 가로길이가 주어짐
  - 얼음틀의 형태가 주어짐
    - 구멍 부분 : 0
    - 막힌 부분 : 1
- 입력예시 
  - 4 5 
  - 00110
  - 00011
  - 11111
  - 00000
- 출력예시
  - 3

해결
- 해결아이디어
  - 0 부분끼리 연결된 덩어리가 곧 얼음이 되므로, 연결된 노드를 한 덩어리로 보고 visited count 를 체크하면 된다.
  - DFS 로 풀어보기 TIP
    1. 특정한 지점의 주변 상, 하, 좌, 우를 살펴본 뒤에 주변 지점 중에서 값이 '0' 이고 방문하지 않은 지점이 있다면 해당 지점을 방문한다. 
    2. 방문한 지점에서 다시 상, 하, 좌, 우를 살피면서 방문을 진행한다.
       1. 이를 반복하면 연결된 모든 지점을 방문할 수 있음
    3. 모든 노드에 대해서 1-2번의 과정을 반복하고 방문하지 않은 지점의 수를 카운트 한다. 

```python
# DFS & BFS 문제
# 음료수 얼려먹기

# n, m 입력받기
n, m = map(int, input().split())

# 현재 얼음틀 상태 입력받기
ice_frames = []

for i in range(n):
  ice_frames.append(list(map(int, input())))

print(ice_frames)

# 총 덩어리 개수 : result
result = 0

# dfs func 정의
# - 연결된 모든 노드가 방문되면 return True
# - 현재위치가 0면 1로 채우기
# - 해당 위치부터 상하좌우 모두 방문 재귀호출 -> 방문 가능하면 1로 채우는 과정 재귀적으로 반복할것 

def dfs(x, y):
  if x <= -1 or x >= n or y <= -1 or y >= m:
    return False

  if ice_frames[x][y] == 0:
    ice_frames[x][y] = 1

    dfs(x-1, y)
    dfs(x+1, y)
    dfs(x, y-1)
    dfs(x, y+1)

    return True
  return False

# n, m for loop -> dfs 함수 호출하기 
# - visited == True -> result +=1
# print(result)
for i in range(n):
  for j in range(m):
    if dfs(i, j) == True:
      result += 1

print(result)
```


## 미로탈출문제
문제
- N * M 크기의 직사각형 형태의 미로가 있다. 
- 여러마리의 괴물이 이어서 이를 피해서 탈출해야한다. 
- 사용자의 위치는 현재 (1, 1)에 있고 미로의 탈출구는 (N, M)에 있다. 
- 한번에 한칸씩만 이동이 가능하다. 
- 괴물있으면 0, 괴물 없으면 1로 표시되어있다. 
- 미로는 반드시 탈출할 수 있는 형태로 되어있다. 
- 탈출을 위해 움직여야하는 최소 칸의 개수는 무엇일까?
- 4<= N, M<=200
- 0과 1로 이루어진 미로정보가 주어짐
- 시작과 마지막은 항상 1
- 입력예시
````text
5 6
101010
111111
000001
111111
111111
````
- 출력예시
  - 10

해결
- BFS 로 해결함
- 처음 1,1 에서 시작
- 매번 새로운 노드에 도달할 때 해당 노드의 값을 +1 로 바꾸어준다. 
- 방문한 노드의 값이 1일때만 move count 로 +1 한다. 
- 마지막 노드에 도달할 때는 움직인 횟수를 알 수 있게 된다. 

```python
# 미로찾기 문제 
# 입력값: 미로의 크기 (n, m), 미로정보
# 반환값 : 최단거리
# 아이디어
  # BFS 를 이용
  # 방문한 노드의 값을 +1 변경
  # 노드값이 1인 경우에만 move cnt 로 체크한다.

from collections import deque

n, m = map(int, input().split())
maze = []

for i in range(n):
  maze.append(list(map(int, input())))

dx = [+1, -1, 0, 0]
dy = [0, 0, -1, +1]

def bfs(x, y):
  queue = deque()
  queue.append((x, y))

  while queue:
    x, y = queue.popleft()

    for i in range(4):
      nx = x + dx[i]
      ny = y + dy[i]

      if nx < 0 or nx >= n or ny < 0 or ny >= m:
        continue
      
      if maze[nx][ny] == 0:
        continue
      
      if maze[nx][ny] == 1:
        maze[nx][ny] = maze[x][y] + 1
        queue.append((nx, ny))

  return maze[n-1][m-1]


print(bfs(0, 0))
```




