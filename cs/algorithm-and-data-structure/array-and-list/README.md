---
description: 배열과 리스트
---

# Array and List

## 배열과 리스트

* 장점
  * 논리적 순서와 물리적 순서가 일치
  * 순서가 존재하여 특정 원소에 바로 접근이 가능
* 단점
  * 삽입이나 삭제 연산 시에 순서유지를 위한 재배치가 일어나기 때문에 오버헤드가 발생하여 큰 규모의 배열일 경우, 성능상 이슈가 발생할 수 있다. &#x20;



## arraylist와 가변 크기 배열

* 특정 언어에서는 배열의 크기를 동적으로 변화시킬 수 있다. 하지만 자바같은 경우는 그 크기가 정해져있기 떄문에 배열을 만들 때, 아예 크기를 지정해주어야한다.
* 그래서 자바에서는 보통 array list 라는 리스트를 쓰는데, 이 리스트의 경우에도 배열이 꽉찬 경우 요소를 추가할 때에는 배열의 크기를 현재 크기의 2배만큼 큰 새로운 배열을 만든 다음에, 기존 배열 요소를 새로운 배열에 복사하는 식이다. 이 경우에는 O(N)의 시간복잡도가 소요된다. 하지만 이경우가 그렇게 많지는 않기 때문에, 상환입력시간을 계산한다면 여전히 O(1)의 시간복잡도를 가지게 된다.
* 상환입력시간은 왜 O(1)이지?
  * 크기가 N인 배열을 생각해볼 때, 이 배열의 크기를 늘릴 떄마다 얼마나 많은 원소를 복사해야하는가 생각해보면 충분히 상상 가능하다.
  * 배열의 크기를 K로 늘리면, 이전 배열은 N/2 였을 테니까 원소의 개수도 그와 같다.
  * 이것을 반복하면 대략 아래와 같이 이어진다.
    * 마지막 배열 크기 증가 : N/2 개의 원소 복사
    * 이전 배열 크기 증가 : N/4 개의 원소 복사
    * 이전 배열 크기 증가 : N/8 개의 원소 복사
    * 두번째 배열 크기 증가 : 2 개의 원소 복사
    * 첫번째 배열 크기 증가 : 1개의 원소 복사
  * 이것을 다 더한다고 해도 절대로 N을 넘을 수 없다. 따라서 N보다 작은 상수가 된다. → O(1)



## 해시테이블

* 키-값 구조로써 매우 빠르고 효율적으로 탐색
* 준비물
  * 연결리스트
  * 해시코드 함수
* 과정
  * 키의 해시코드를 계산. 보통 int나 long 을 사용한다. 서로 다른 두 개의 키가 같은 해시 코드를 가리킬 수 있다.
  * hash(key) % array length 와 같은 방식으로 해시 코드를 이용해 배열의 인덱스를 구한다.
  * 배열의 각 인덱스에는 키와 값으로 이루어진 연결 리스트가 존재한다. 키와 값을 해당 인덱스에 저장한다.
  * 서로 다른 두 개의 해시코드가 같은 인덱스를 가리킬수도 있다.
  * 배열의 각 인덱스에는 키와 값으로 이루어진 연결리스트가 존재한다. 키와 값을 해당 인덱스에 저장한다. 충돌에 대비해서 반드시 연결리스트로 저장해야한다.
    * 충돌이란 서로 다른 두 개의 키가 같은 해시코드를 가리키거나, 서로 다른 두 개의 새시 코드가 같은 인덱스를 가리키는 경우를 말한다.
* 찾는 과정
  * 예를 들어 hi 라는 키가 있다고 해보자.
  * hi 로부터 해시코드를 계산하여 10320 이라는 코드를 뽑아내고
  * 이 해시 코드를 이용해서 배열의 인덱스를 구한다. → 0
  * 해당 키와 상응하는 값(abc)을 배열에 연결리스트에서 탐색하면 된다.
* 균형이진탐색트리
  * 탐색시간은 O(logN)
  * 배열을 미리 할당하지 않아도 되어서 공간활용도가 좋고
  * 키의 집합을 특정 순서대로 접근가능하다.



