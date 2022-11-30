# CPU Scheduling Algorithm

### CPU 스케줄링 알고리즘

* FCFS : first come first served
* SJF : shortest job first
* SRTF : shortest remaining time first
* Priority Scheduling
* RR : round robin
* Multilevel Queue
* Multilevel Feedback Queue
* Multiple-Processor Scheduling
* Real-time scheduling
* thread scheduling

#### FCFS : first come first served

* 프로세스의 도착 순서대로 스케줄링된다.
* 단점은 짧은 수행시간을 가진 프로세스라도 늦게 도착하면 오랜 시간을 대기해야한다는 것이다.

<figure><img src="../../.gitbook/assets/image (3) (4).png" alt=""><figcaption></figcaption></figure>

#### SJF : shortest job first

* 각 프로세스의 다음번 CPU burst time 을 가지고 스케줄링에 활용한다.
* CPU burst time 이 가장 짧은 프로세스를 제일 먼저 스케줄한다.
* 종류
  * non-preemptive
    * 일단 CPU 를 잡으면 이번 CPU burst 가 완료될 때까지 CPU 를 선점당하지 않는다.
  * preemptive
    * 현재 수행중인 프로세스의 남은 burst time 보다 더 짧은 CPU burst time 을 가지는 새로운 프로세스가 도착하면, CPU 빼앗긴다.
    * 이 방법을 SRTF 하고 한다. → SRTF : shortest remaining time first
* optimal
  * 주어진 프로세스들에 대해서 minimum average waiting time 을 보장한다.
* 다음 CPU burst time은 어떻게 알 수 있는가?
  * 추정만 가능하다.
  * 과거의 CPU burst time 을 이용해서 추정한다.

<figure><img src="../../.gitbook/assets/image (2) (3).png" alt=""><figcaption></figcaption></figure>

#### Priority Scheduling

* 우선순위 번호가 각 프로세스에게 할당된다.
* 가장 높은 우선순위를 가진 프로세스에게 CPU 를 할당한다.
* smallest integer = highest priority
  * preemptive
  * nonpreemptive
* SJF 는 일종의 priority scheduling 이라고 할 수 있다.
  * priority = predicted next CPU burst time
* 문제점
  * starvation : 낮은 우선순위를 가진 프로세스들은 아마 시작도 못해볼 것이다.
* 해결책
  * aging : 대기 시간이 흐를수록 우선순위를 높여주는 방법

#### RR : round robin

* 각 프로세스는 동일한 크기의 할당시간 time quantum 을 가진다. 일반적으로는 10-100 milliseconds.
* 할당 시간이 지나면 프로세스는 선점 preempted 당하고, ready queue 의 제일 뒤에 가서 다시 줄을 선다.
* n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit 인 경우, 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n 을 얻는다.
* 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않는다.
* 퍼포먼스
  * q large → FCFS
  * q small → context switch 오버헤드가 커진다.
* 할당 시간이 넘어가서 다시 queue의 제일 뒤로 가야할 때, 다시 마지막 시점부터 정확하게 시작할 수 있다면, 굉장히 효과적인 방법이 될 수 있다.

#### Multilevel Queue

* ready queue 를 여러개로 분할한다.
  * foreground - interactive
  * background - batch, no human interaction
* 각 큐는 독립적인 스케줄링 알고리즘을 가진다.
  * foreground - RR
  * background - FCFS
* 큐에 대한 스케줄링이 필요
  * fixed priority scheduling
    * foreground 를 항상 먼저 serve 하고 background 를 나중에 하는 방법
    * 단점 : background 는 오랜 시간 대기해야한다.
  * time slice
    * 각 큐에 CPU time 을 적적한 비율로 할당한다.
    * foreground : 80%, RR
    * background : 20%, FCFS
* 줄마다 우선순위가 다르다.
* 우선순위대로 나열하자면 다음과 같다.
  * system → interactive → interactive editing → batch → student

<figure><img src="../../.gitbook/assets/image (17) (2).png" alt=""><figcaption></figcaption></figure>

#### Multilevel Feedback Queue

* 프로세스가 다른 큐로 이동이 가능하다.
* 에이징을 이와 같은 방식으로 구현할 수 있다.
* multilevel feedback queue scheduler 를 정의하는 파라미터들
  * queue 의 수
  * 각 큐의 scheduling algorithm
  * process 를 상위 큐로 보내는 기준
  * process 를 하위 큐로 내쫗는 기준
  * 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
* 보통은 처음 들어오는 프로세스를 첫 queue 에 할당하고 밑으로 갈수록 시간을 길게 둔다.
  * CPU 이용 시간이 짧은 프로세스는 첫줄에 들어와서 바로 끝날 수 있도록하고
  * 이용 시간이 길수록 낮은 queue 로 이동하여 좀 더 기다리도록 한다.
  * CPU 버스트가 짧은 프로세스에게 운영시간을 많이 주는 방식이다.
* 스케줄링
  * 새로운 job은 제일 윗쪽 queue, Q0에 들어가게 된다.
  * CPU 를 잡아서 할당시간 8 millisec 안에 수행된다.
  * 할당 시간안에 다 끝내지 못했다면, 그 아래 queue 로 내려간다.
  * Q1에 줄서서 기다렸다가 CPU 를 잡아서 16ms 동안 수행이 되고
  * 그 안에도 다 못끝냈다면 Q2로 쫓겨난다.

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

#### Multiple-Processor Scheduling

* cpu 가 여러개인 경우, 스케줄링은 더욱 복잡해진다.
* homogeneous processor
  * queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
  * 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더욱 복잡해진다.
* load sharing
  * 일부 프로세서에 job 이 몰리지 않도록 부하를 적절히 공유하는 메커니즘이 필요하다.
  * 별개의 큐를 두는 방법 vs. 공동 큐를 사용하는 방법
* symmetric multiprocessing, SMP
  * 각 프로세서가 각자 알아서 스케줄링 결정
* asymmetric multiprocessing
  * 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따른다.
  * CPU가 여러개이고, 하나의 CPU가 책임을 진다.

#### Real-time scheduling

* hard real time systems
  * 정해진 시간안에 반드시 끝내도록 스케줄링해야한다.
  * 보통 periodical 하게 수행되는 경우가 많다.
* soft real time systems
  * 일반 프로세스에 비해 높은 priority를 갖도록 해야한다.

#### Thread scheduling

* local scheduling
  * user level thread
  * 운영체제가 thread의 존재를 모름
  * 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지를 결정한다.
* global scheduling
  * kernel level thread
  * 운영체제가 thread의 존재를 알고 있음
  * 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정한다.

### 그렇다면, 어떤 알고리즘이 가장 적절할까?

#### Algorithm Evaluation

* queueing models
  * 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산한다.
* implementation & measurement
  * 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 비교한다.
  * 하지만 이 방법은 수행하기에 어렵다.
* simulation
  * 알고리즘을 모의 프로그램으로 작성 후, trace(시뮬레이션 프로그램에 input으로 들어갈 데이터)를 입력으로 하여 결과를 비교한다.

<figure><img src="../../.gitbook/assets/image (3) (3).png" alt=""><figcaption></figcaption></figure>
