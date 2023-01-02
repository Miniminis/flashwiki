# Initial Attempts to Solve Process Synchronization Problem

### 프로세스 간 동기화 문제를 해결하기 위한 초기의 방법론

<figure><img src="../../.gitbook/assets/image (6) (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 프로그램적 해결법의 충족조건

1. mutual exclusion 상호배제
   1. 프로세스 Pi 가 critical section 부분을 수행중이면 다른 모든 프로세스들은 그들의 critical section 에 들어가면 안된다.
2. progress
   1. 아무도 critical section 에 있지 않은 상태에서 critical section 에 들어가고자 하는 프로세스가 있으면 critical section 에 들어가게 해주어야 한다.
3. bounded waiting 유한대기
   1. 프로세스가 critical section 에 들어가려고 요청한 후 부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section 에 들어가는 횟수에 한계가 있어야 한다.
   2. 요청한 후에 특정시간 이내에는 들어갈 수 있어야 한다.

#### 가정

* 모든 프로세스의 수행속도는 0보다 크다.
* 프로세스들 간의 상대적인 수행속도는 가정하지 않는다.

#### 첫번째 알고리즘 - turn 변수 사용

* mutual exclusion 은 충족하지만, progress 는 충족하지 못한다.
* 과잉보호가 발생하게 됨. 반드시 교대로 한번씩 들어가야 하는데, 그가 turn 을 내 값으로 바꿔줘야만 내가 들어갈 수 있다.
* 특정 프로세스가 더 빈번히 critical section 을 들어가야 한다면 어떻게 될까?

<figure><img src="../../.gitbook/assets/image (15) (2) (1).png" alt=""><figcaption></figcaption></figure>

#### 두번째 알고리즘 - 플래그

* 플래그가 동시에 켜질 때, 서로 플래그가 켜진 상태기 때문에 아무도 while 문 안으로 진입하지 못하는 문제가 발생할 수 있다.

<figure><img src="../../.gitbook/assets/image (1) (1) (3) (1).png" alt=""><figcaption></figcaption></figure>

#### 세번째 알고리즘 - 피터슨 알고리즘 - 플래그와 turn 변수 모두 사용

* mutual exclusion, progress, bounded waiting 의 세 가지 조건을 모두 만족하는 알고리즘이다.
* 둘다 critical section 에 들어가고자 할 때, turn 을 따진다.
* 둘다 들어가고 싶지 않다면, turn 대로 들어간다.
* 발생할 수 있는 문제점 : busy waiting = spin lock

<figure><img src="../../.gitbook/assets/image (4) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Synchronization Hardware

* 고급언어에서는 필연적으로 instruction 이 여러 개로 나누어지기 때문에 이런 알고리즘들을 고민해야한다.
  * 데이터를 읽는 명령과 쓰는 명령을 동시에 수행할 수 없기 때문에 발생하는 문제들이다.
* 하지만 하나의 instruction 으로 데이터을 읽고 쓰는 것을 동시에 수행할 수 있다면, 사실 고민할 문제가 아니다.
* 하드웨어적으로 test & modify 를 atomic 하게 수행할 수 있도록 지원하는 경우, 앞의 문제는 간단히 해결될 수 있다.
* test and set a : a의 값을 읽고 쓰는 것을 동시에 수행하는 명령

<figure><img src="../../.gitbook/assets/image (6) (4).png" alt=""><figcaption></figcaption></figure>

> 하지만 프로그래머가 이런 일을 계속 프로그램 내에서 하는 것은 굉장히 불편한 일이다. 이때 우리는 semaphore 를 이용할 수 있다.
