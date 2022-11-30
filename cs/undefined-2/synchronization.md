# Process Synchronization Problem

### 데이터의 접근

* 보통 프로그램은 다음과 같은 과정의 반복일 것이다.
  1. storage box 에 데이터가 저장되어있고
  2. 연산할 데이터를 읽어와서
  3. 연산을 진행한 다음
  4. 연산 결과를 다시 storage 에 저장한다.
* 이때, 바로 syncronization 문제가 발생하게 된다. → `Race Condition` 경쟁상태의 가능성!
  * 같은 storage 에 여러 실행 박스들이 접근하는 경우
* 운영체제 커널 레벨에서 충분히 발생할 수 있는 문제이다.

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

### OS 운영체제에서 race condition 경쟁상태는 언제 발생하는가

#### 1. 커널모드를 running 할 때, interrupt 가 발생하여 인터럽트 처리루틴이 수행되는 경우

* count ++ 를 수행하는 경우, load, inc, store 등 여러 단계에 걸쳐서 연산이 수행되는데,
* 연산 도중에 인터럽트가 발생할 경우, 연산 결과에 오염이 발생할 수 있다.
* 해결방법
  * kernel 모드에서 연산 수행할 때, 인터럽트를 막는 방법이 있다.

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

#### 2. process 가 system call 을 하여 kernal mode 로 수행중인데 context switch 가 발생하는 경우

<figure><img src="../../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

#### 3. multiprocessor 에서 shared memory 내의 kernal data

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

### Process Synchronization 문제

* 공유 데이터의 동시접근은 데이터의 불일치 문제를 발생시킬 수 있다.
* 일관성 유지를 위해서는 협력 프로세스 간의 실행 순서를 정해주는 매커니즘이 필요하다.
* race condition
  * 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
  * 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라지게 된다.
* race condition 을 막기 위해서는 concurrent process 는 동기화 synchronize 되어야 한다.

#### Race condition

* 프로세스간 context switch 자체가 문제가 아니라 shared data 를 접근하는 경우가 문제가 되는 것이다.

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

### The Critical-Section Problem

* n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
* 각 프로세스의 code segment 에는 공유 데이터를 접근하는 코드인 critical section 이 존재한다.
* 문제점
  * 하나의 프로세스가 critical section 에 있을 때, 다른 모든 프로세스는 critical section 에 들어갈 수 없어야 한다.

<figure><img src="../../.gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>
