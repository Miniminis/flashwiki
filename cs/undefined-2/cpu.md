# CPU Scheduling

<figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (104) (1).png" alt=""><figcaption></figcaption></figure>

### 프로세스의 특성 분류

* I/O bound process
  * CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 작업
* CPU-bound process
  * 계산 위주의 job

### CPU scheduler & Dispatcher

* CPU scheduler
  * ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.
  * 운영체제 내부에서 CPU 스케줄링을 하는 기능(운영체제 커널의 코드)을 의미한다. 특정하고 독립된 하드웨어나 소프트웨어가 하니다.
* Dispatcher
  * CPU 제어권을 CPU scheduler 에 의해 선택된 프로세스에게 넘기는 운영체제의 커널 코드이다.
  * 이 과정을 context switch, 문맥교환이 라고 한다.
* CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.
  1. running → blocked : 예를 들면, I/O 요청하는 시스템 콜 등
  2. running → ready : 할당시간만료로 timer interrupt 가 발생하는 경우 등
  3. blocked → ready : I/O 작업이 완료된 후 인터럽트하는 경우
  4. terminate
* 1, 4에서의 스케줄링은 nonpreemptive 강제로 빼앗기지 않고 자진 반납하는 경우이고
* 모든 다른 스케줄링은 preemptive 강제로 빼앗은 경우이다.
