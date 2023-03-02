# semaphore 와 monitor 로 synchronization 해결하기

### Synchronization 에서 Semaphore를 이용해서 해결할 수 있는 전형적인 문제들

1. 생산자-소비자 문제, bounded-buffer problem
2. readers and writers problem
3. 식사하는 철학자 문제, dining-phillosophers problem

### Semaphores

* 앞의 방식들을 추상화시킨다.
* Semaphore S
  * interger value
  * P(S) : S가 양수일 경우, 이를 감소시키면서 자원을 획득하는 연산과정
  * V(S) : 자원 반납의 연산 과정
  * 값이 1로 초기화 : lock
  * 값이 1이 아니면 : 남아있는 자원의 개수를 세는 역할을 함

#### Block and wakeup 방식으로 구현됨

<figure><img src="../../.gitbook/assets/image (10) (3) (1).png" alt=""><figcaption></figcaption></figure>

### 문제 1. Bounded Buffer Problem

* 버퍼의 크기가 유한한 환경에서 생산자-소비자 문제가 발생할 수 있음
* 복수의 생산자 프로세스와 복수의 소비자 프로세스가 있음
* 발생할 수 있는 문제
  * 배타적 접근의 문제 : lock을 걸고 풀어야 하는 문제
  * 버퍼 가용 자원의 수를 관리하는 문제 : 생산자와 소비자의 수가 불균형한 문제
    * 버퍼가 full 상태일때 버퍼를 비우는 소비자가 나타날떄까지 생산자는 기다려야한다.
    * 버퍼가 empty 상태일때 버퍼를 채워주는 생산가자 나타날때까지 소비자는 기다려야한다.
* 해결방법
  * 생산자와 소비자는 각각 5가지 정도의 작업을 순차적으로 수행하여 문제를 예방한다.

<figure><img src="../../.gitbook/assets/image (9) (4) (1).png" alt=""><figcaption></figcaption></figure>

#### sudo code

* 변수
  * full 개수
  * empty 개수
  * mutex : lock
* P 연산 : 자원획득
* V 연산 : 자원 반환

<figure><img src="../../.gitbook/assets/image (1) (3) (1).png" alt=""><figcaption></figcaption></figure>

### 문제 2. readers and writers problem

* 한 프로세스가 DB 에 write 중일 때, 다른 process 가 접근하면 안된다.
* read는 동시에 여럿이 해도 된다.
* 해결방법
  * writer 가 db에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 reader 들을 다 DB 에 접근하게 해준다.
  * writers는 대기중인 reader 가 하나도 없을 때 DB 접근이 허용된다.
  * 일단 writer 가 db에 접근 중이면 reader 들은 접근이 금지된다.
  * writer 가 db에서 빠져나가야만 reader의 접근이 허용된다.
* shared data
  * db 자체
  * readcount : 현재 db에 접근 중인 reader의 수
* synchronization variables
  * mutex : 공유변수 readcount를 접근하는 코드(critical section)의 mutext exclution 을 보장하기 위해 사용한다.
  * db : reader와 writer 가 공유 db 자체를 올바르게 접근하게 하는 역할
* `if (readcount==1) P(db)` : 최초의 reader 만 writer 를 막기 위해서 lock 을 걸고, 나머지 reader 에 대해서는 접근을 허용해준다.
* starvation 문제 발생의 가능성이 있다.
  * 만약에 도착 순서가 : reader → writer → 100 개의 readers 라면,
  * 첫번째 reader 에 의해서 db 는 lock 될 것이고, writer 는 우선 기다릴 것이다.
  * 이후에 도착한 reader 들은 계속 db 접근이 가능하므로 읽을 것이다.
  * 하지만 writer 가 기다리는 동안 계속 해서 reader 가 도착한다면, writer 의 대기시간은 무한정 길어질 수 있다.
* starvation에 대한 해결 방법이 있을까?
  * reader queue 에 우선순위를 두어, 어느정도 선까지 도착한 reader 만 허용하고, db 자원을 반납해 writer 가 들어올 수 있도록 해준다.
  * 마치 신호등이 있어서 끊임없이 오는 차들을 잠시 막고 사람들이 틈틈히 건너갈 수 있도록!

<figure><img src="../../.gitbook/assets/image (19) (3).png" alt=""><figcaption></figcaption></figure>

### 문제 3. 식사하는 철학자 문제, dining-phillosophers problem

* 다섯명의 철학자가 원탁에 둘러 앉아 토론을 하고 있다.
* 그들이 하는 일은 두가지이다. 생각하거나 밥을 먹거나.
* 각 철학자들은 배가 고픈 시점이 제각각이다.
* 밥을 먹기 위해서는 젓가락 두쪽을 모두 잡아야 한다. 젓가락은 왼쪽과 오른쪽 두 곳으로 나누어져 있다.
* 발생할 수 있는 문제점
  * deadlock 가능성이 있다.
  * 모든 철학자가 동시에 배고파서 왼쪽 젓가락을 동시에 잡았다면, 아무도 오른쪽 젓가락을 잡을 수 없어서 무한 대기하는 상황이 발생하게 된다.
* 해결방안
  * 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다.
  * 젓가락을 두개 모두 잡을 수 있을 때에만 젓가락 잡을 수 있게 한다. → 권한
  * 비대칭 : 짝수 철학자는 왼쪽 젓가락부터 잡고, 홀수 철학자는 오른쪽 젓가락부터 잡을 수 있도록 한다.
* 식사하는 철학자의 경우, semaphore 보다 monitor 코드가 더 이해하기에 쉽다.

<figure><img src="../../.gitbook/assets/image (12) (3) (1).png" alt=""><figcaption></figcaption></figure>

#### 권한을 두어 해결하는 방법의 예시 코드

* 일반적인 semaphore 코드는 자원의 개수를 세고 초기값을 두는경우가 많은데, 아래의 코드는 0의 상태에서 진행된다. → semaphore 코드답지 않다고 느끼는 이유

<figure><img src="../../.gitbook/assets/image (26) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Monitor

#### semaphore 의 문제점

* 코딩하기 힘들다
* 문제가 생겼을 때, 버그잡기가 쉽지 않다.
* 한번 실수가 모든 시스템에 치명적인 영향을 끼친다.
* 정확성의 입증이 어렵다.
* 자발적 협력이 필요하다.

<figure><img src="../../.gitbook/assets/image (27) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### monitor

* 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high level synchronization construct
* 모니터 내부에 공유데이터와 공유데이터에 대한 프로시저를 정의하고 프로시저를 통해서만 접근을 가능하게 한다.
* 모니터 내에서는 한번에 하나의 프로세스 만 활동이 가능하다.
* 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없다.
  * semaphore 는 lock 를 걸고 해제하는 작업을 프로그래머가 직접 해줬어야 하는데, monitor 는 설계 자체가 동시접근이 안되도록 되어있기 때문에 개발자가 할 작업이 줄어든다.
* 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해서 condition variable 을 사용한다.
  * condition x, y;
* condition variable 은 wait() 와 signal 연산에 의해서만 접근이 가능하다.
* x.wait() 을 invoke 한 프로세스는 다른 프로세스가 x.signal() 을 invoke 하기 전까지는 suspend 된다.
* x.signal() 은 정확하게 하나의 suspend 된 프로세스를 resume 한다. suspend 된 프로세스가 없으면 아무일도 일어나지 않는다.
* waiting 을 하는 줄이 하나의 목적에 따라서 여러갈래로 나뉜다.

<figure><img src="../../.gitbook/assets/image (4) (1) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 비교 1. bounded-buffer problem : semaphore vs. monitor

<figure><img src="../../.gitbook/assets/image (13) (2) (1) (1).png" alt=""><figcaption><p>bounded-buffer problem in semaphore version</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (2) (1) (1).png" alt=""><figcaption><p>bounded-buffer problem in monitor version</p></figcaption></figure>

### 비교 2. 식사하는 철학자 문제 : semaphore vs. monitor

<figure><img src="../../.gitbook/assets/image (22) (2).png" alt=""><figcaption><p>식사하는 철학자 문제 in semaphore code</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (3) (1).png" alt=""><figcaption><p>식사하는 철학자 문제 in monitor code</p></figcaption></figure>

### 비교 : semaphore vs. monitor

> 사실 semaphore 와 monitor 버전의 코드는 상호 변환이 용이하다.

|      | semaphore                             | monitor                                |
| ---- | ------------------------------------- | -------------------------------------- |
| 값    | 있음                                    | 없음                                     |
| 동시접근 | 자원을 획득하기 위해 프로그래머가 P연산, V연산을 해주어야 한다. | monitor 차원에서 지원해주어 프로그래머가 신경쓰지 않아도 된다. |
