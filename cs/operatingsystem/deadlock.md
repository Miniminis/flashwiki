# 데드락, Deadlock

### Deadlock

* 일련의 프로세스들이 서로가 가진 자원을 기다리며 block 된 상태
* resource, 자원
  * 하드웨어, 소프트웨어 등을 포함하는 개념
  * I/O device, CPU cycle, memory space, semaphore 등
  * 프로세스가 자원을 사용하는 절차
    * request : 자원 요청
    * allocate : 자원할당
    * use : 자원 사용
    * release : 자원 해제
  * deadlock example
    * 시스템에 2개의 tape drive 가 있다.
    * 프로세스 p1과 p2 각각이 하나의 tape drive 를 보유한 채, 다른 하나를 기다리고 있다.
  * deadlock example 2
    * binary semaphores A and B
    * P0 : P(A); P(B);
    * P1 : P(B); P(A);

### Deadlock 이 발생하는 4가지 조건

1. mutual exclusion : 상호배제
   1. 매 순간 하나의 프로세스만이 자원을 사용할 수 있다.
2. No Preemption : 비선점
   1. 프로세스는 자원을 스스로 내어놓을 뿐, 강제로 빼앗기지 않는다.
3. Hold and wait : 보유대기
   1. 자원을 가진 프로세스가 다른 자원을 기다릴 때, 보유자원을 놓지 않고 계속 가지고 있다.
4. Circular wait : 순환대기
   1. 자원을 기다리는 프로세스 간에 사이클이 형성되어야 한다.
   2. 프로세스 P0 \~ Pn 이 있을 때,
      1. P0은 P1의 자원을 기다리고
      2. P1은 P2의 자원을 기다리고
      3. P3은 P4의 자원을 기다리고
      4. … 반복…
      5. Pn은 P0의 자원을 기다린다.

### resource allocate graph

* Dealock 발생 여부를 알기 위해서 `자원 할당 그래프`를 그려볼 수 있다.
* P1은 R1 자원을 원하지만, R1자원은 지금 P2에게 할당되어있다.
* R2자원은 P1과 P2모두에게 할당되어있다.
* 일단, 그래프에 cycle 이 없으면, deadlock 은 아니다.
* 그래프에 cycle 이 존재할 때도, 자원이 하나 이상 있다면, deadlock 이 아니다.
* **즉, 그래프에 cycle 이 존재하고, 자원이 하나밖에 없을 때, deadlock 이 발생한다.**

<figure><img src="../../.gitbook/assets/image (1) (1) (2) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (6).png" alt=""><figcaption></figcaption></figure>

### Deadlock 의 처리방법

> 위로 갈수록, 더 강한 처리방법이다.

1. deadlock prevention
   1. 자원 할당시 deadlock 의 4가지 필요조건 중, 어느 하나가 만족되지 않도록 하는 것이다.
2. dedlock avoidance
   1. 자원 요청에 대한 부가적인 정보를 이용해서 deadlock 의 가능성이 없는 경우에만 자원을 할당한다.
   2. 시스템 state 가 원래 state 로 돌아올 수 있는 경우에만 자원을 할당한다.
3. deadlock detection and recovery
   1. deadlock 발생은 허용하되, 그에 대한 detection 루틴을 두어 deadlock 발견시 recover
4. deadlock ignorance
   1. deadlock을 시스템이 책임지지 않는다.
   2. unix 를 포함한 대부분의 OS 가 이 방법을 채택했다. 사용자가 알아서 액션을 취하는 방식이다.

### Deadlock Prevention

* mutual exclution
  * 공유해서는 안되는 자원의 경우, 반드시 성립해야한다.
* hold and wait : 다른 자원을 기다릴 때, 현재의 자원을 쥐고 있어서 발생하는 문제
  * 프로세스가 자원을 요청할 때, 다른 어떤 자원도 가지고 있지 않아야 한다.
  * 방법 1 : 프로세스 시작 시, 모든 필요한 자원을 할당받게 하는 방법
  * 방법 2 : 자원이 필요한 경우, 보유 자원을 모두 놓고 다시 요청하는 방법
* no preemption : 가지고 있는 자원을 빼앗지 못해서 발생하는 문제
  * process 가 어떤 자원을 기다려야하는 경우, 이미 보유한 자원이 선점됨
  * 모든 필요한 자원을 얻을 수 있을 때, 그 프로세스는 다시 시작된다.
  * state 를 쉽게 save 하고, restore 할 수 있는 자원에서 주로 사용할 수 있다. (CPU, memory)
    * 중간 지점을 기억할 수 없는 프로세스의 경우, 문제가 발생할 수 있다.
* circular wait
  * 모든 자원 유형에 할당 순서를 정해서 정해진 순서대로만 자원을 할당한다.
  * 예를 들면, 순서가 3인 자원을 보유중인 프로세스가 순서가 1인 자원을 할당받기 위해서는 우선 순서가 3인 자원을 release 해야한다.

> 하지만 위의 방법들은 발생하는 빈도수가 많지 않은 deadlock 상황을 지나치게 대비하여 자원 이용률이 저하되고, starvation 문제가 생길 수 있다.

### Dedlock avoidance

* 프로세스가 시작될 때, 필요한 모든 자원을 미리 전부 조사하여 deadlock 가능성이 없는 안전한 경우에만 자원을 할당한다.
* 가장 단순하고 일반적인 모델은 프로세스들이 필요로하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법이다.
* safe state
  * 시스템 내의 프로세스들에 대한 `safe sequence` 가 존재하는 상태
* safe sequence
  * 프로세스의 sequence 가 safe 하려면 프로세스 별 자원요청이 `가용자원 + 모든 프로세스의 보유자원`에 의해 충족되어야 한다.
  * 조건을 만족하면, 다음 방법으로 모든 프로세스의 수행을 보장한다.
    * 프로세스의 자원요청이 즉시 충족될 수 없으면, 모든 프로세스들이 종료될 때까지 기다린다.
    * Pi-1가 종료되면, Pi의 자원요청을 만족시켜 수행한다.
* safe 와 unsafe 의 의미
  * unsafe state 에 있다고해서 반드시 deadlock 이 발생하는 것은 아니다. 발생할 가능성이 생기는 것이다.
  * safe state 에 있다면, 무조건 deadlock 이 발생하지 않는다.

<figure><img src="../../.gitbook/assets/image (16) (3).png" alt=""><figcaption></figcaption></figure>

#### 자원이 1개밖에 없는 경우 - resouce allocation graph algorithm

<figure><img src="../../.gitbook/assets/image (1) (2) (3).png" alt=""><figcaption></figcaption></figure>

#### 자원이 여러개 있는 경우 - bankers algorithm

* Need 만큼의 양이 확보되지 않는 이상 자원 요청을 받아들이지 않는다.
* 굉장히 보수적이고 엄격한 알고리즘이다.
* 예를 들어 지금 상황에서 P0가 A 자원 1개를 요청할 경우, 받아들여지지 않는다. 최대 7개까지 요청할 가능성이 있기 떄문이다.
* P1의 경우, A자원을 최대 1개 더 요청할 수 있는데, 이는 available 한 자원 내의 범위이다. 따라서 P1이 A자원을 요청할 경우에는 무조건 받아들여진다.
* 결국, 프로세스들 사이에 sequence 가 생기게 되는데, 이 경우, 절대 deadlock 이 생기지 않게 된다.
* 이 알고리즘에 따른 방법은 deadlock 을 예방할 수 있지만, 굉장히 비효율적이게 된다. 자원이 남아돌아도 할당하지 않는 경우가 많이 발생하기 때문이다.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

#### 문제 풀어보기

<figure><img src="../../.gitbook/assets/image (2) (2) (2) (1).png" alt=""><figcaption></figcaption></figure>

> 위의 두 방법은 deadlock 을 예방할 수 있는 방법이고,\
> 아래의 두 방법은 deadlock 이 발생하도록 두고, 시스템에 문제가 생겼을 때만, 조치를 취하는 방법이다.\
> → deadlock 은 거의 발생하지 않기 때문에 이 방법이 효율적이긴 하다.

### Dedlock detection and recovery

* 모든 알고리즘에 대해서 그래프를 그리는 것보다 bankers 알고리즘처럼 테이블을 그려서 계산하는 것이 훨씬 간단하고 효율적이다.
* 자원할당 그래프에 대해서 프로세스+자원의 조합으로 이루어진 버전을 `프로세스 간 그래프(wait-for graph)`로 바꾸면 훨씬 간단해진다.
  * 그래프를 그려서 cycle 이 발생하는지 점검하면 된다.
  * 이 방법은 최대 O(n^2)의 시간이 소요된다.
    * n개의 프로세스가 존재하고
    * 각 프로세스로부터 뻗어나올 수 있는 그래프의 개수는 n-1개이니까
    * 최대 n \* (n-1) 개의 그래프를 탐색하면 된다. → n^2의 시간 소요

<figure><img src="../../.gitbook/assets/image (1) (1) (4).png" alt=""><figcaption></figcaption></figure>

#### 아래의 경우, sequence 가 존재하기 때문에 deadlock 이 발생하지 않는다.

<figure><img src="../../.gitbook/assets/image (4) (2) (2).png" alt=""><figcaption></figcaption></figure>

#### 만약 P2가 자원 C를 하나 더 요청한다면?

* deadlock 이 발생하게 된다.
* deadlock 발생 가능성을 확인하는 방법
  * 가용 자원의 개수를 확인하고, 요청하지 않은 것은 가용자원으로 치고
  * 가용 자원으로 프로세스를 해결 가능한지 확인한다.

<figure><img src="../../.gitbook/assets/image (16) (2) (1).png" alt=""><figcaption></figcaption></figure>

#### deadlock 발생시 recovery 방법

1. deadlock 에 연루된 프로세스를 강제 종료시킨다. 여기에도 두 가지 방법이 있다.
   1. 모든 프로세스를 강제 종료시키는 방법과
   2. deadlock 상황이 해결될 때까지 프로세스를 하나씩 죽이면서 종료시키는 방법이다.
2. deadlock 에 연루된 프로세스로부터 자원을 빼앗는 방법
   1. 최소한의 비용으로 자원을 뺏을 수 있는 희생양 victim 을 선정하고
   2. safe state 로 rollback 하여 프로세스를 다시 시작한다.
   3. 이때, starvation 문제가 발생할 수 있다.
      1. 동일한 프로세스가 계속해서 victim 으로 선정되는 경우, 해당 프로세스는 계속 자원을 받지 못하고 대기하게 된다.
      2. rollback 횟수도 비용으로 같이 산정되어야 한다.
   4. 따라서 비용 뿐만아니라, 몇번 자원을 빼앗겼는지 등을 같이 종합적으로 고려하는 것이 좋다.

### Dedlock ignorance

* deadlock 이 발생하지 않는다고 가정하고 아무일도 안하는 방법이다.
* 애초에 deadlock 은 매우 드물게 발생한다. 따라서 이에 대비하여 어떠한 조치를 미리 취하는 것이 오히려 더 큰 overhead 라고 판단한다.
* 이 방법에 따르면, deadlock 상황을 인지한 사용자가 비정상적임을 느끼고 직접 process 를 kill 하여 문제상황을 해결한다.
* unix, windows 등 대부분의 OS들이 채택한 방법이기도 하다.
