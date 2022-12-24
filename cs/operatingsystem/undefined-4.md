# 쓰레드, Thread

### Thread

* a thread (or lightweight process) is a basic unit of CPU utilization
  * 프로세스 내부에 쓰레드를 여러개 두는 것이 훨씬 더 가볍기 때문!
* program counter, register set, stack space 으로 구성되어있으며
* thread 가 동료 thread 와 공유하는 부분을 task 라고 하며 code section, data section, OS resources 등이 있다.
* 전통적인 개념의 heavyweight process 는 하나의 thread를 가지고 있는 task 로 볼 수 있다.
* 다중 쓰레드로 구성된 태스크 구조에서는 하나의 서버 쓰레드가 blocked(waiting)상태인 동안에도 동일한 태스크 내의 다른 쓰레드가 실행되어 빠른 처리를 할 수 있다.
  * 구글 검색창에 검색어를 입력했을 때, 네트워크를 통해 웹 페이지를 불러오는데 이 과정은 시간이 오래 걸리는 I/O 과정이다.
  * 하나의 쓰레드로만 구성되어있다면, 웹 페이지를 불러오는 동안, 웹 브라우저는 블록되어 사용자는 아무것도 할 수 없게 된다.
  * 이 때, 다른 쓰레드를 통해 이미 불러온 텍스트라도 화면에 띄워주는 작업을 한다면, 이 블록 시간을 단축하여 빠른 처리를 할 수 있게 된다.
* 동일한 일을 수행하는 다중 쓰레드가 협력하여 높은 처리율(throughput)과 성능향상을 얻을 수 있다.
  * 같은 일을 하는 프로세스별로 독자공간을 차지할 경우, 메모리가 낭비된다.
  * 하나의 프로세스에 쓰레드만 여러개 두면, 이 낭비되는 공간을 최소화할 수 있다.
* 쓰레드를 사용하면 병렬성을 높일 수 있다.

<figure><img src="../../.gitbook/assets/image (8) (3).png" alt=""><figcaption></figcaption></figure>

* 프로세스가 시작되면, 그 프로세스만을 위한 주소공간이 생성되고 운영체제 내에는 PCB를 통해 그 프로세스가 관리된다.
  * 프로그램 카운터에 의해 현재 해당 프로세스에서 어느 부분을 실행하고 있는지를 알 수 있다.
* 하지만 동일한 일을 하는 프로세스가 여러개가 있다고 하면, 그때마다 주소공간이 별도로 생성된다면, 메모리가 낭비되게 된다. 이 경우, 주소 공간을 하나만 띄워두고, 각 프로세스마다 다른 코드 부분을 실행할 수 있도록 하면 된다. 이 개념이 바로 쓰레드이다.
* 이 경우, 운영체제 내 PCB에서는 다른 부분은 모두 공유하되, 현재 프로세스의 실행 위치를 기억하는 program counter, register 등은 분리해서 독립적으로 가지고 있게 된다.

<figure><img src="../../.gitbook/assets/image (2) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 쓰레드의 장점

* responsiveness : 빠른 반응 속도를 누릴 수 있다.
  * multi-thread web : network + display 두 쓰레드에 의해 동시에 가능
* resource sharing : 자원을 효율적으로 써서 낭비를 줄일 수 있다.
* economy
  * adding & `CPU switching thread` vs. creating & process switching
  * CPU를 새로 만들고 프로세스간 스위칭 하는 것보다 쓰레드 내에서 스위칭을 하는 것이 훨씬 더 오버헤드가 적다.
* utilization of MP architectures
  * 각각의 쓰레드가 병렬적으로 일처리가 가능하다.

### 구현방식

* kernel threads
  * 운영체제 커널이 프로세스 내 쓰레드가 여러개 있다는 것을 인지하고 있어서 CPU 스케줄링에 관여한다.
  * windows 95/98/NT
  * solaris
  * digital unix, mach
* user threads
  * 프로세스 내 쓰레드가 여러개 있다는 것을 운영체제(커널)는 모른다. 프로세스 자체에서 관리를 한다.
  * posix pthreads
  * mach c-threads
  * solaris threads
* real-time threads
