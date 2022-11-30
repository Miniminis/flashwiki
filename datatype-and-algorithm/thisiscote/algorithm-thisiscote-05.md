---
title: <이코테 2021> 5. 이진탐색
category: algorithm
date: 2022-01-19 04:19:45
---

> 💡 동빈나 님의 [이코테 2021 강의 몰아보기](https://www.youtube.com/watch?v=m-9pAwq1o3w&list=PLRx0vPvlEmdAghTr5mXQxGpHjWqSz0dgC&index=1) 를 보면서 공부한 내용을 정리하고 있습니다. 더 자세한 내용은 **[이것이 취업을 위한 코딩 테스트다 with 파이썬](http://www.yes24.com/Product/goods/93519145) 을 참고해주세요** 😊 학습 도구로는 [리플렛](https://replit.com/) 을 사용하고 있고 원본 소스코드는 동빈님의 [Github](https://github.com/ndb796/python-for-coding-test) 에서 확인할 수 있고  스스로 공부한 소스코드는 [Github](https://github.com/Miniminis/algorithm-study-note-python) 에서 확인할 수 있습니다.

## 이진 탐색 알고리즘
- 순차 탐색 : 리스트 안에 있는 특정한 데이터를 찾기 위해서 앞에서부터 데이터를 하나씩 확인하는 방법
- 이진 탐색 : 정렬되어있는 리스트에서 탐색 범위를 절반씩 좁혀가며 데이터를 탐색하는 방법
  - 시작점, 끝점, 중간점을 이용하여 탐색 범위를 설정한다. 
  - O(logN)

![](/image/algorithm-thisiscote-05-1642535240073.png)

![](/image/algorithm-thisiscote-05-1642535208179.png)

- 예시
  - 이미 정렬된 10개의 데이터 중에서 값이 4인 원소 찾기
  - 시작점 : 0, 끝점 9, 중간점 4
    - 중간점이 두 개인 경우는 소수점 이하를 제거한 수를 중간점으로 본다.
  - 중간점과 목표인 4를 비교
    - 작으면 끝점을 중간점 왼쪽으로 이동시킨다.
      - 중간점 이후로는 볼 필요가 없기 때문임
    - 크면 시작점을 중간점 오른쪽으로 이동시킨다.
      - 중간점 이전으로는 볼 필요가 없기 때문임
    - 중간점과 목표인 4가 같다면 탐색을 중단한다. 

### 시간복잡도
- 단계마다 탐색범위를 2로 나누는 것과 동일하므로 연산횟수는 log2N 에 비례한다. 
- 이상적일 경우 시간 복잡도를 O(logN)을 보장한다. 
- 총 요소가 32개일 경우 
  - 1회 연산후 16개
  - 2회 연산후 8개
  - 3회 연산후 4개 데이터가 남는다.

### 재귀함수로 구현하기
```python 
# 이진탐색 재귀함수로 구현하기 
# target 값이 몇번째에 존재하는지 위치 출력하기 

# 전체 원소개수 n, 목표 타겟 target
n, target = map(int, input().split())
array = list(map(int, input().split()))

def binery_search(array, start, end, target):
  # start > end : return None
  # mid = (start + end) // 2
  # tartget == mid : return 
  # target > mid : start = mid+1
  # target < mid : end = mid-1

  if start > end :
    return None
  
  mid = (start + end) // 2
  print('==>', start, end, mid)

  
  if target == array[mid]:
    return mid
  
  if target > array[mid]:
    return binery_search(array, mid+1, end, target)
  else:
    return binery_search(array, start, mid-1, target)

result = binery_search(array, 0, n-1, target)

if result == None:
  print('원소가 존재하지 않습니다.')
else:
  print(result+1, '번째에 원소가 존재합니다.')

# 10 7
# 1 3 5 7 9 11 13 15 17 19
# 1 3 5 6 9 11 13 15 17 19
```

#### 반복문으로 구현하기
```python
# 이진탐색 재귀함수로 구현하기 
# target 값이 몇번째에 존재하는지 위치 출력하기 

# 전체 원소개수 n, 목표 타겟 target
n, target = map(int, input().split())
array = list(map(int, input().split()))

def binery_search(array, start, end, target):
  while start <= end:
    mid = (start + end) // 2

    if array[mid] == target:
      return mid
    
    if array[mid] > target:
      end = mid - 1
    else:
      start = mid + 1
  
  return None

result = binery_search(array, 0, n-1, target)

if result == None:
  print('원소가 존재하지 않습니다.')
else:
  print(result+1, '번째에 원소가 존재합니다.')

# 10 7
# 1 3 5 7 9 11 13 15 17 19
# 4 번째에 원소가 존재합니다.


# 10 7
# 1 3 5 6 9 11 13 15 17 19
# 원소가 존재하지 않습니다.
```


## 파이썬 이진 탐색 라이브러리
- bisect_left(a, x) : 정렬된 순서를 유지하면서 배열 a에 x를 삽입할 가장 왼쪽 인덱스를 반환한다. 
- bisect_right(a, x) : 정렬된 순서를 유지하면서 배열 a에 x를 삽입할 가장 오른쪽 인덱스를 반환한다.

```python
# bineary search library practice
# 정렬을 지키면서 원소를 삽입할 수 있는 가장 왼쪽/오른쪽 인덱스를 출력해준다. 

from bisect import bisect_left, bisect_right

a = [1, 2, 3, 4, 5]
x = 4

print(bisect_left(a, x))
print(bisect_right(a, x))
```

### 값이 특정 범위에 속하는 데이터 개수 구하기
```python
# 값이 특정 범위에 속하는 데이터 개수 구하기

from bisect import bisect_left, bisect_right

def count_by_range(a, left_value, right_value):
    left_idx = bisect_left(a, left_value)
    right_idx = bisect_right(a, right_value)

    return right_idx - left_idx

a = [1, 2, 3, 3, 3, 3, 4, 4, 8, 9]

# 4의 개수 구하기
print(count_by_range(a, 4, 4))  
# 8-6 = 2

# -1, 3 범위 내에 있는 데이터 개수 출력
print(count_by_range(a, -1, 3))  
# 6 - 0 = 6
```


## 파라메트릭 서치
- 최적화 문제를 결정문제(예/아니오)로 바꾸어서 해결하는 기법
  - 최적화 문제 : 어떤 함수의 값을 가능한 낮추거나 높이는 방법에 대한 문제 
  - 이를 여러번의 결정문제로 바꾸어 해결한다. 
- 예) 특정 조건을 만족하는 가장 알맞은 값을 빠르게 찾는 최적화 문제
- 일반적으로 코테에서 파라메트릭 서치 문제는 이진 탐색을 이용하여 해결할 수 있다. 

## 떡볶이 떡 만들기 문제
문제
- 떡볶이 떡을 만든다. 
- 떡 길이는 일정하지 않지만 한 봉지 안에 들어가는 떡의 총 길이는 절단기로 잘라서 맞춰준다. 
- 절단기 높이(H)는 높이보다 긴 떡은 잘리고 낮은 떡은 잘리지 않도록 되어있다. 
  - 예를들면 높이가 19, 14, 10, 17cm 인 떡이 있을 경우
  - 절단기 사용 후에는 15, 14, 10, 15cm 가 된다. 
  - 잘린 떡의 길이는 차례대로 4, 0, 0, 2cm 이고 손님은 6cm 만큼의 길이를 가져가게 된다. 
- 손님이 요청한 길이가 M일때 적어도 M만큼의 떡을 얻기 위해서 절단기에 설정할 수 있는 높이의 최댓값을 구하는 프로그램을 작성하라. 
- 조건
  - 떡의 개수 N, 떡의 길이 M
  - 떡의 개별 높이 배열
  - 입력예시 
    - 4 6 
    - 19 15 10 17
  - 출력예시
    - 15 
  - 1 <= N <= 1,000,000
  - 1 <= M <= 2,000,000,000


풀이
- 적절한 높이를 찾을 때까지 이진 탐색을 수행하여 높이 H를 반복해서 조정하면 됨!
- 현재 이 높이로 자르면 조건을 만족할 수 있는가? 를 확인한 뒤에 조건의 만족여부 (예/아니오)에 따라서 탐색 범위를 좁혀서 해결할 수 있다. 
  - 높이값이 커지면 잘린 떡의 길이는 작아지고 
  - 높이값이 커지면 잘린 떡의 길이는 높아질 것이다. 
- 절단기의 높이는 0에서 10억까지 정수 중 하나 
  - 이렇게 큰 탐색 범위를 보면 가장 먼저 이진탐색을 떠올려야한다! 
- 문제 예시를 통해서 그림으로 이해해보자. 

![](/image/algorithm-thisiscote-05-1642537105204.png)
- 1차 탐색 
  - 시작 0, 끝 19, 중간 (0+19)//2 = 9
  - 잘린 떡 총합 : 10+6+1+8= 25cm
  - 25 > 6 : 충족 
  - 충족하면 저장
- 2차 탐색
  - 시작 10, 끝 19, 중간 (10+19)//2 = 14
  - 잘린 떡 총합 : 5+1+0+3 = 9
  - 9 > 6 : 충족
  - 충족하면 저장
- 3차 탐색
  - 시작 15, 끝 19, 중간 (15+19)//2 = 17
  - 잘린 떡 총합 : 2 
  - 2 < 6 : 불충족
  - 충족 못하므로 저장하지 않는다.
- 4차 탐색 
  - 시작 15, 끝 16,  중간 (15+16)//2 = 15
  - 잘린 떡 총합 : 4+0+0+2 = 6
  - 6 = 6 : 충족! 
  - 충족하면 저장
- 이런 방법으로 진행하면 매번 저장된 값이 최적의 값이므로 더이상 이진탐색을 수행하지 못했을 때, 저장된 값이 최적의 해가 된다. 

```python
# 떡볶이
# 적어도 m만큼 떡을 가져가기 위해서 절단기의 높이는?

n, m = map(int, input().split())
rice_cakes = list(map(int, input().split()))

def binery_search(array, start, end, target, result):

  if start > end:
    return result
  
  mid = (start + end) // 2
  total = 0

  for i in array:
    if i > mid:
      total += i - mid

  if total >= target:
    result = mid
    return binery_search(array, mid+1, end, target, result)
  else:
    return binery_search(array, start, mid-1, target, result)
  
print(binery_search(rice_cakes, 0, max(rice_cakes), m, 0))

# 입력 : 떡의 개수 n / 떡의 길이 m / 각 떡의 길이들 array
# 4 6
# 19 15 10 17
# 출력
# 15
```

## 정렬된 배열에서 특정 수의 개수 구하기 문제
문제
- N개의 원소를 포함하고 있는 수열이 오름차순으로 정렬되어있음
- 수열에서 x가 등장하는 횟수를 계산해라
- 예를들어 수열 {1, 1, 2, 2, 2, 2, 3}이 있을 때, x=2 라면, 현재 수열에서 값이 2인 원소가 4개이므로 4를 출력한다. 
- 시간복잡도 O(logN)으로 알고리즘을 설계하지 않으면 시간초과 판정을 받게 된다. 
- 조건
  - N개의 원소, X가 찾는 값
  - 1 <= N <= 1,000,000
  - -10^9 <= x <= 10^9
  - x와 일치하는 원소개수가 0이면 -1을 출력한다. 
  - 입력예시
    - 7 2
    - 1 1 2 2 2 2 3
  - 출력예시
    - 4

풀이
- 시간복잡도 O(logN)으로 동작하는 알고리즘을 요구하는 문제이다. 
  - 일반적인 선형탐색으로는 시간초과 판정을 받을 수 있다. 
  - 하지만 데이터가 정렬되어있기 때문에 이진 탐색을 수행할 수 있다. 
- 특정 값이 등장하는 첫번째 위치와 마지막 위치를 찾아 위치 차이를 계산해 문제를 해결할 수 있다. 
- 직접 구현과 표준라이브러리 모두 사용 가능
  - [자바 이진탐색 구현 & 표준라이브러리 예시](https://shlee0882.tistory.com/124)

```python
# 정렬된 배열에서 특정 수의 개수 구하기 문제

from bisect import bisect_left, bisect_right

n, m = map(int, input().split())
array = list(map(int, input().split()))

def binery_search(array, target): 
  count = bisect_right(array, target) - bisect_left(array, target)

  if (count == 0):
    return -1
  
  return count

print(binery_search(array, m))
```