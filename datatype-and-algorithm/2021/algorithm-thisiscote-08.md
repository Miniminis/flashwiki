---
title: <이코테 2021> 8. 기타 그래프 이론
category: algorithm
date: 2022-02-06 05:57:52
---

> 💡 동빈나 님의 [이코테 2021 강의 몰아보기](https://www.youtube.com/watch?v=m-9pAwq1o3w&list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&index=1) 를 보면서 공부한 내용을 정리하고 있습니다. 더 자세한 내용은 **[이것이 취업을 위한 코딩 테스트다 with 파이썬](http://www.yes24.com/Product/goods/93519145) 을 참고해주세요** 😊 학습 도구로는 [리플렛](https://replit.com/) 을 사용하고 있고 원본 소스코드는 동빈님의 [Github](https://github.com/ndb796/python-for-coding-test) 에서 확인할 수 있고  스스로 공부한 소스코드는 [Github](https://github.com/Miniminis/algorithm-study-note-python) 에서 확인할 수 있습니다.

## 서로소집합
- 서로소 : 공통원소가 없는 두 집합을 의미함
  - {1, 2}, {3, 4} : 서로소에 해당함
  - {1, 2}, {2, 4} : 서로소에 해당하지 않음

### 서로소 집합 자료구조
- 서로소 부분 집합들로 나누어진 원소들의 데이터를 처리하기 위한 자료구조
- 두종류의 연산을 지원함
  - 합집합 Union
    - 두 개의 원소가 포함된 집합을 하나의 집합으로 합치는 연산
  - 찾기 Find
    - 특정한 원소가 속한 집합이 어떤 집합인지 알려주는 연산 
- 합치기 찾기 (Union Find) 자료구조라고 불리기도 한다. 

### 동작과정
1. 합집합 연산을 확인하여 서로 연결된 두 노드 A, B를 확인한다. 
   1. A, B의 루트 노드인 A', B'를 찾는다. 
   2. A'를 B'의 부모 노드로 설정한다. 
2. 모든 합집합 연산을 처리할 때까지 1번의 과정을 반복한다. 

> [동작과정 살펴보기](https://youtu.be/aOhhNFTIeFI?list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&t=140) 

1. 처리할 연산들이 주어진다. 
   1. Union(1, 4)
   2. Union(2, 3)
   3. Union(2, 4)
   4. Union(5, 6)
2. 각 노드들의 부모 노드를 기록하는 테이블을 생성한 뒤 처음에는 자기 자신으로 초기화 한다. 
   - 1->1, 2->2, ... 6->6
3. Union 연산의 두 노드를 비교하여 더 작은 노드를 루트 노드로 설정한 뒤 부모 노드 테이블을 갱신한다. 
   1. 일반적으로는 더 큰 루트 노드가 작은 루트노드를 따르도록 하는 것이 관례이다. (그렇지 않게 작성하는 방법도 있긴 하다)
   2. 이때 중요한 점은 부모노드 != 루트노드
   3. 예) 3의 경우 부모는 2이지만 루트노드는 3->2->1 이 된다. 
4. 연결된 형태를 통해서 집합의 형태를 확인할 수 있다. 
   1. 루트노드가 같은 그룹은 하나의 집합이라고 판단할 수 있다. 
   2. 예시
      1. {1, 2, 3, 4} : 1
      2. {5, 6} : 5

### 연결성
- 기본적인 형태의 서로소 집합 자료 구조에서는 루트 노드에 즉시 접근할 수 없다. 
  - 루트 노드를 찾기 위해서 부모 테이블을 계속해서 확인하며 거슬러 올라가야 한다. 
- 예시 
  - 3의 루트를 찾는 과정 : 3-> 2-> 1

```python
 # 서로소집합 구현하기

# 노드의 개수, 간선 개수 입력받기
v, e = map(int, input().split())

# parent[] 테이블 생성
parent = [0] * (v+1)

# parent table 요소 자기 자신으로 초기화하기
for i in range(1, v+1):
  parent[i] = i

# 특정 원소의 부모를 찾기 위한 함수
def find_parent(parent, x):
  if parent[x] != x:
    find_parent(parent, parent[x])
  return x

# 두 원소가 속한 집합 합치기 (부모 요소 갱신)
def union(parent, a, b):
  a = find_parent(parent, a)
  b = find_parent(parent, b)

  if a < b:
    parent[b] = a
  else:
    parent[a] = b

# 유니온 연산 수행
for i in range(e):
  a, b = map(int, input().split())
  union(parent, a, b)

# 각 원소가 속한 집합 출력하기
for i in range(1, v+1):
  print(find_parent(parent, i), end=' ')

print()

# 부모테이블 요소 출력하기 
for i in range(1, v+1):
  print(parent[i], end = ' ')
```

### 기본적인 서로소 집합 자료구조의 구현시 문제점
- 합집합 연산이 편향되게 수행되는 경우에 비효율이 발생할 수 있다. 
- 최악의 경우 찾기 함수가 모든 노드를 다 호출하여 시간복잡도가 최대 O(v) 까지 발생할 수 있다. 
  - 예시
    - Union(4, 5), Union(3, 4), Union(2, 3), Union(1, 2)
    - {1, 2, 3, 4, 5} 원솔르 가진 집합 1개가 존재한다. 

### 해결방법 : 경로압축
- 찾기 함수를 최적화 하기 위하여 경로압축을 사용할 수 있음
- 루트노드를 찾는 과정에서 찾기함수를 재귀적으로 호출하고 부모테이블 값을 바로 갱신해버린다. 
- 결국 부모테이블의 값이 루트노드의 테이블이 되는 셈이다.
- 기존 방법에서 find_parent() 함수만 아래와 같이 수정해주면 된다. 

```python
# 특정 원소의 부모를 찾기 위한 함수
def find_parent(parent, x):
  if parent[x] != x:
    parent[x] = find_parent(parent, parent[x])
  return parent[x]

```

## 서로소 집합을 활용한 사이클 판별
- 무방향 그래프 내에서의 사이클을 판별할 때 사용할 수 있다. 
  - 방향 그래프에서 사이클 여부는 DFS 를 이용하여 판별할 수 있다. 
- 사이클 판별 알고리즘은 다음과 같다. 
1. 각 간선을 하나씩 확인하며 두 노드의 루트 노드를 확인한다. 
   1. 루트 노드가 다르다면 두 노드에 대해서 합집합 연산을 수행 
   2. 루트 노드가 서로 같다면 사이클 발생 
2. 그래프에 포함되어 있는 모든 간선에 대하여 1번 과정을 반복한다. 

### 사이클 판별 알고리즘 살펴보기
> [자세히 설명 보기](https://youtu.be/aOhhNFTIeFI?list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&t=1055)

```python
 # 사이클 판별하기 

# 특정 원소를 포함하는 집합 찾기 (루트노드찾기))
def find_parent(parent, x):
  if parent[x] != x:
    parent[x] = find_parent(parent, parent[x])
  return parent[x]

# 합집합 연산 수행하기 
def union_parent(parent, a, b):
  a = find_parent(parent, a)
  b = find_parent(parent, b)

  if a < b:
    parent[b] = a
  else:
    parent[a] = b

# 사용자 입력받기
v, e = map(int, input().split())
parent = [0] * (v+1)

for i in range(1, v+1):
  parent[i] = i

cycle = False

for i in range(e):
  a, b = map(int, input().split())

  if find_parent(parent, a) == find_parent(parent, b):
    cycle = True
    break
  else:
    union_parent(parent, a, b)

if cycle:
  print('cycle 발생')
else:
  print('cycle 발생안함')

```

## 최소신장트리 알고리즘

최소신장트리
- 그래프에서 모든 노드를 포함하면서 사이클이 존재하지 않는 부분 그래프

![](/.gitbook/assets/algorithm-thisiscote-08-1644115014510.png)

실제 문제에서의 응용
![](/.gitbook/assets/algorithm-thisiscote-08-1644115170243.png)

### 크루스칼 알고리즘
- 대표적인 최소 신장 트리 알고리즘
- 그리디 알고리즘으로 분류됨
- 동작과정
  1. 간선 데이터를 비용에 따라서 오름차순으로 정렬한다. 
  2. 간선을 하나씩 확인하며 현재의 간선이 사이클을 발생시키는지 확인한다. 
     1. 사이클 발생할 경우 : 스킵함
     2. 사이클 발생하지 않을 경우 : 최소 신장 트리에 포함시킴
  3. 모든 간선에 대래서 2번의 과정을 반복한다. 

### 크루스칼 알고리즘 동작과정
> [설명 자세히 보기](https://youtu.be/aOhhNFTIeFI?list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&t=1526) 

1. 그래프의 모든 간선 정보에 대하여 오름차순으로 정렬 수행
   1. 참고 : 최종적으로 만들어지는 최소신장트리의 간선 개수 = 전체 노드의 개수 -1
   2. 기본적으로 트리가 가지는 특징이며 사이클도 가지지 않는다. 
2. 가장 비용이 작은 간선부터 확인한다. 
3. 차례대로 비용이 작은 순서부터 반복한다. 
4. 같은 집합에 속해있지 않으면 유니온 함수 호출하여 집합 합치기
5. 이미 같은 집합에 속해있다면 스킵한다. 
6. 마지막으로 최소 신장트리가 만들어지만 그에 포함된 간선의 비용만 더하면 최소 비용을 산출할 수 있다! 


```python
# 크루스칼 알고리즘

# 특정 원소를 포합하는 집합 찾는 함수 정의하기 : 경로단축기법 사용하기
def find_parent(parent, x):
  if parent[x] != x:
    parent[x] = find_parent(parent, parent[x])
  return parent[x]


# 합집합 연산함수 정의하기
def union_parent(parent, x, y):
  x = find_parent(parent, x)
  y = find_parent(parent, y)

  if x < y:
    parent[y] = x
  else:
    parent[x] = y

# 사용자로부터 노드개수, 간선개수 입력받기
v, e = map(int, input().split())

# 부모노드 테이블 생성
parent = [0] * (v+1)

# 부모노드 테이블 초기화하기
for i in (1, v+1):
  parent[i] = i

# 간선정보 배열, 최종비용합계 변수 초기화하기 
edge = []
result = 0

# 간선정보 입력받기 : 시작노드, 끝노드, 비용 정보 입력받기
for _ in range(e):
  a, b, cost = map(int, input().split())
  edge.append((cost, a, b))

# 비용 순으로 오름차순 정렬하기
edge.sort()

# 간선정보에 따라 집합비교하기
for e in edge:
  cost, a, b = e

  # 두 요소의 집합이 같지 않을 때에만 유니온 함수 호출 후 비용에 포함시키기 
  if find_parent(parent, a) != find_parent(parent, b):
    union_parent(parent, a, b)
    result += cost

# 결과적으로 계산된 최소비용을 출력한다.
print(result)
```

### 크루스칼 알고리즘의 성능 분석
- 간선의 개수가 E개 일때 O(ElogE)의 시간복잡도를 가진다. 
- 이 알고리즘에서 가장 많은 시간이 요구되는 곳은 간선을 정렬하는 부분
- 표준 라이브러리를 이용해서 E개의 데이터를 정렬하기 위한 시간 복잡도는 O(ElogE)


## 위상정렬
- 사이클이 없는 방향 그래프의 모든 노드를 방향성에 거스르지 않도록 순서대로 나열하는 것
  - [자세한 설명보기](https://youtu.be/aOhhNFTIeFI?list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&t=1987)
- 예를들면 선수과목을 고려한 학습순서설정 과 같은 문제에서 사용될 수 있다. 
  - 세 과목을 듣는 적절한 학습 순서는 다음과 같다!
  - 자료구조 -> 알고리즘 -> 고급 알고리즘

### 참고) 진입차수와 진출차수
![](/.gitbook/assets/algorithm-thisiscote-08-1644117202815.png)

- 진입차수 : 특정한 노드로 들어오는 간선의 개수
- 진출차수 : 특정한 노드에서 나가는 간선의 개수 

### 위상정렬 알고리즘 동작과정
- 방법 : DFS, 큐
- 큐를 이용할 때
  1. 진입차수가 0인 모든 노드를 큐에 넣는다. 
  2. 큐가 빌때까지 다음의 과정을 반복한다. 
     1. 큐에서 원소를 꺼내서 해당 노드에서 나가는 간선을 그래프에서 제거한다. 
     2. 새롭게 진입차수가 0이된 노드를 큐에 넣는다. 
  3. 각 노드가 큐에 들어온 순서 = 위상정렬 수행한 결과 

> [자세한 설명보기](https://youtu.be/aOhhNFTIeFI?list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&t=2177)

1. 위상정렬수행할 그래프 준비하기 
   1. 이때 그래프는 사이클이 없는 방향 그래프여야 한다. 
2. 가장 먼저 진입차수가 0인 모든 노드를 큐에 넣는다. 
   1. 1번 노드가 큐에 삽입된다. 
3. 이제 큐에서 노드 1을 꺼내고 여기에서 나가는 간선을 제거한다. 
   1. 1->2, 1->5 간선이 제거된다. 
   2. 이제 2, 5의 진입차수가 0이 된다. 이들을 큐에 넣는다. 
4. 큐에서 다시 노드를 꺼낸다. 일반적으로 더 작은 노드를 꺼내므로 2가 나오게 된다. 
   1. 2에서 나가는 간선을 제거한다. 2->3, 2->6 이 제거된다. 
   2. 이제 3번 노드가 진입차수가 0으로 큐에 삽입된다. 
5. 5번 노드를 꺼낸다.
   1. 5->6으로 가는 간선이 제거된다. 
   2. 6번 노드도 진입차수가 0으로 큐에 삽입된다. 
6. 3번 노드를 꺼낸다. 
   1. 3->4 간선이 제거된다. 
   2. 진입차수가 0이 된 노드가 없으므로 스킵한다. 
7. 6번 노드를 꺼낸다. 
   1. 6->4 간선이 제거된다. 
   2. 4번의 진입차수가 0이 되었다. 4번 노드를 큐에 넣는다. 
8. 4번 노드를 꺼낸다. 
   1. 4->7 간선을 제거한다.
   2. 7번노드도 진입차수 0으로 큐에 삽입된다. 
9. 7번 노드를 꺼낸다. 
   1. 7-> 간선이 없고 진입차수가 0이된 노드가 없으므로 종료한다. 
10. 큐에 삽입된 전체 노드의 순서
    - 1->2->5->3->6->4->7

### 위상정렬의 특징
- 현재의 그래프가 사이클을 갖지 않은 방향그래프일때만 수행할 수 있다. 
  - DAG : direct acyclic graph : 순환하지 않는 방향 그래프
- 여러가지의 답이 존재할 수 있다. 
  - 한 단계에서 큐에 새롭게 들어가는 원소가 2개 이상인 경우라면 여러가지 답이 존재할 수 있다. 
- 모든 원소를 방문하기 전에 큐가 빈다면 사이클이 존재한다고 판단할 수 있다. 
  - 사이클에 퐘된 원소 중에서 어떤 원소도 큐에 들어가지 못하기 때문이다. 
- 스택을 활용한 DFS 를 이용해서 위상정렬을 수행할수도 있다. 

```python
# 위상정렬 알고리즘

# 큐 자료구조 이용하기 위하여 라이브러리 임포트하기 
from collections import deque

# 노드의 개수, 간선의 총 개수 입력받기
v, e = map(int, input().split())

# 각 노드별 진압차수 정보 담을 배열 생성하긴
indegree = [0] * (v+1)

# 그래프 생성하기 : 2차원배열
graph = [[] for _ in range (v+1)]

# 방향그래프 초기화하기
for _ in range(e):
  a, b = map(int, input().split())
  graph[a].append(b)
  indegree[b] += 1

# 결과 배열 정의하기
result = []

# 위상정렬 수행하는 함수 만들기
def topology_sort():
  # 큐 자료구조 사용을 위해서 초기화
  q = deque()

  # 큐가 빌때까지 반복
  while q:  
    # 큐에서 제일 왼쪽 요소 꺼내기
    now = q.popleft()

    # 꺼낸 요소 결과배열에 넣기
    result.append(now)

    # 해당 배열 내 원소 루프 돌면서
    for i in graph[now]:
      # 진입차수 정보 -1 하기
      indegree[i] -= 1

      # 만약 진입차수가 0이면 큐에 넣기 
      if indegree[i] == 0:
        q.append(i)

topology_sort()

print(result)

# 결과배열 순회하며 위상정렬 결과 출력하기 
for i in result:
  print(i, end='')


# 7 8 
# 1 2
# 1 5
# 2 3
# 2 6
# 3 4
# 4 7
# 5 6
# 6 4
```

### 위상정렬 성능분석
- 모든 노드를 차례대로 방문해서 그 노드에 연결된 간선을 차례대로 제거해야한다. 
- 시간 복잡도는 O(V+E)이다. 
  - 노드의 개수 + 노드에 연결된 간선의 개수 
