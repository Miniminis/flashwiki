# 프로세스

### 프로세스의 개념

* 문맥
  * 프로그램이 특정 시점을 봤을 때, 어디까지 실행을 했는지
  * CPU 수행상태를 나타내는 하드웨어 문맥
    * Program Counter 가 어디있는가
    * 각종 register 가 현재 어떤 값을 가지고 있는가
  * 프로세스의 주소공간
    * code data, stack에 어떤 내용이 들어있는가
  * 프로세스 관련 커널 자료구조
    * PCB(Process Control Block)
    * Kernel stack
      * 여러 프로세스들이 공유하는 공간이기에 프로세스별로 별도로 관리된다.

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>



### 프로세스의 상태

* 프로세스는 상태가 변경되며 수행된다.
* Running : 현재 CPU를 잡고 instruction 을 수행중인 상태
* Ready : 메모리 등 다른 모든 조건을 만족하고 CPU를 기다리는 상태 (CPU가 오기만 하면 되는 상태)
* Blocked (wait, sleep)
  * CPU를 주어도 당장 instruction 을 수행할 수 없는 상태
  * Process 자신이 요청한 event (I/O) 가 즉시 만족되지 않아 이를 기다리는 상태
    * 예를 들면, 디스크에서 file 을 읽어와야 하는 경우
* New : 프로세스가 생성 중인 상태
* Terminated : 수행이 끝난 상태

#### 한 장으로 보는 프로세스의 문맥 변경 과정

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>



### Process Control Block, PCB

* 운영체제가 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보
* 다음과 같이 구성되어있다.
  * OS 가 관리상 사용하는 정보
    * process state, process id, scheduling information, priority
  * CPU 수행 관련 하드웨어 값
    * program counter, registers
  * 메모리 관련
    * code, data, stack 의 위치정보
  * 파일 관련
    * open file descriptors

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>



### 문맥교환, context switch

* CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
* 다른 프로세스로 넘어갈 때, 운영체제는 다음의 과정을 수행한다.
  * 현재 실행중인 프로세스의 상태를 그 프로세스의 PCB에 저장한다. (다시 돌아왔을 때 어디서부터 시작해야하는지 기억하기 위해서)
  * 새롭게 실행이 될 프로세스의 상태를 PCB 에서 읽어온다. (전에 어디까지 했는기 기억하여 거기서부터 시작하기 위해서)
* system call 이나 interrupt 발생할 때, 반드시 context switch 가 일어나는 것은 아니다.
  * 보통은 system call 이나 interrupt 발생 이후에 원래의 프로세스로 CPU가 돌아간다.
  * 하지만 CPU를 공정하게 분배하려는 timer interrupt 가 발생하거나, 시간이 오래걸리는 I/O 요청이 system call 로 온 경우에는 운영체제가 다시 프로세스에게 문맥을 넘겨줄때, 다른 프로세스에게 주게 된다. 이때 발생하는 것이 바로 문맥교환 context switch 이다.
* 비록, (1)의 경우에 문맥교환이 발생하지 않더라도 CPU 수행 정보 등 context 의 일부를 PCB에 저장해야하지만,
* 문맥 교환을 해야하는 (2)의 경우에는 훨씬 더 많은 context 정보를 저장해야하는 부담이 생긴다. (cache memory flush)

&#x20;

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>



### 프로세스를 스케줄링 하기 위한 큐

* job queue : 현재 시스템 내에 있는 모든 프로세스의 집합
* ready queue : 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
* device queue : I/O device의 처리를 기다리는 프로세스의 집합
* 프로세스들은 각 큐들을 오가며 수행된다.

<figure><img src="../../.gitbook/assets/image (9) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (2).png" alt=""><figcaption></figcaption></figure>



### 스케줄러, scheduler

* long-term scheduler : 장기 스케줄러 혹은 job scheduler
  * 시작 프로세스 중 어떤 것들을 ready queue 로 보낼지 결정한다.
  * 프로세스에 memory 및 각종 자원을 주는 문제
  * degree of multiprogramming 을 제어한다. 즉, 메모리에 올라가는 프로그램의 수를 제한한다.
  * 실제로 대부분의 컴퓨터가 사용하는 time sharing system에는 보통 장기 스케줄러가 없고 무조건 ready 하게 된다.
* short-term scheduler : 단기 스케줄러 혹은 CPU scheduler
  * 어떤 프로세스를 다음 번에 running 시킬지 결정한다.
  * 프로세스에 CPU를 주는 문제를 계획한다.
  * millisecond 단위로 충분히 빨라야한다.
* medium-term scheduler : 중기 스케줄러 혹은 swapper
  * 대부분의 컴퓨터에서 사용하는 스케줄러
  * 시작하면서 메모리에 올라갈 프로세스를 결정하는 장기 스케줄러와 달리, 중기 스케줄러는 일단 프로그램이 실행되면 메모리를 무조건 할당한다.
  * 이렇게 실행할 경우, 메모리는 빠르게 여유공간이 없어지는데, 공간 마련을 위해서 프로세스를 통째로 메모리에서 디스크로 쫓아낸다.
  * 이런 방식으로 degree of multiprogramming 을 제어하며 프로세스에게서 memory를 뺏는 문제를 다룬다.

### 중기 스케줄러의 존재로 인해 추가된 프로세스의 상태

* 기존에 존재하던 기본적인 상태 : Running, Ready, Blocked(wait, sleep)
* 새롭게 추가된 상태 : Suspended(stopped)
  * 외부적인 이유(사용자가 프로그램을 일시정지하는 등)로 프로세스의 수행이 정지된 상태
  * 프로세스는 통째로 디스크 swap out 된다.
  * 예를들어 사용자가 프로그램을 일시 정지 시킨 경우, (메모리에 너무 많은 프로세스가 올라와있다면) 시스템이 이 중단되어 사용하지 않는 상태의 프로세스를 잠시 중단시킨다.
* Blocked : 자신이 요청한 이벤트가 만족되면 ready 상태로 변한다.
* Suspended : 외부에서 resume 해주어야 active 된다.

<figure><img src="../../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>
