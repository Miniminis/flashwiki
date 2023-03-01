# 프로세스 시스템 콜과 프로세스간의 협력, System call and Interprocess Communication

### copy on write (cow)

* 프로세스가 생성될 때, 부모 프로세스가 자식 프로세스를 생성한다.
* 원칙적으로는 자원을 부모와 전혀 공유하지 않지만, 리눅스와 같은 일부 운영체제에서는 부모와 자식이 완전히 동일한 프로세스일 경우, 자원을 공유하도록 하고 있다.
  * 부모와 자식의 내용이 달라지는 순간부터 분리된다.

### 프로세스 생성과정

* fork() 로 먼저 이전의 프로세스를 복제한 후,
* exec() 를 통해서 새로운 내용을 덮어씌운다.

### fork() 시스템 콜

<figure><img src="../../.gitbook/assets/image (12) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 부모 프로세스에서는 program counter 가 fork() 시점을 가리키고 있기 때문에 복제된 자식 프로세스는 fork() 를 한 이후 코드부터 실행된다.
  * 화면상 fork() 이전의 printf() 문은 부모 프로세스에서만 출력된다.
* 부모와 자식은 fork() 함수의 return 값이 다르다.
  * 부모 : pid > 0
  * 자식 : pid == 0

### exec() 시스템 콜

<figure><img src="../../.gitbook/assets/image (3) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 한번 execlp() 함수가 호출되면, 돌이킬 수 없다.
* 꼭 fork() 이후에만 써야하는 것은 아니다. execlp() 만 단독으로 실행할 수도 있다.
* execlp() 이후의 코드는 실행되지 않는다.
  * 위의 그림에서 화면에 출력되는 것은 1→ hello 3 뿐일것이다.

### wait() 시스템 콜

<figure><img src="../../.gitbook/assets/image (18) (1) (1) (1).png" alt=""><figcaption><p>자식 프로세스가 끝날때까지 부모 프로세스는 대기하게 된다.</p></figcaption></figure>

### exit() 시스템 콜

* 프로세스의 종료
* 자발적인 종료
  * 마지막 statement 수행 후, exit() 시스템 콜을 통해
  * 프로그램에 명시적으로 적어주지 않아도 main() 함수가 리턴되는 위치에 컴파일러가 넣어준다.
* 비자발적인 종료
  * 부모 프로세스가 자식 프로세스를 강제 종료시킨다.
    * 자식 프로세스가 한계치를 넘어서는 자원을 요청하는 경우
    * 자식에게 할당된 태스크가 더 이상 필요하지 않은 경우
  * 키보드로 kill, break 등을 친 경우
  * 부모가 종료하는 경우
    * 부모 프로세스가 종료하기 전에 자식들이 먼저 종료된다.

### 프로세스와 관련한 시스템 콜

* fork() : 자식을 생성
* exec() : 새로운 내용을 덮어 씌움
* wait() : 자식이 종료될때까지 기다림
* exit() : 프로세스를 종료시킴

### 프로세스 간 협력

* 독립적 프로세스
  * 프로세스는 각자의 주소 공간을 가지고 수행되므로, 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.
* 협력 프로세스
  * 프로세스 협력 매커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있다.
* 프로세스 간 협력 매커니즘
  * IPC : interprocess Communication
  * 메시지를 전달하는 방법
    * message passing : 커널을 통해 메시지를 전달한다.
    * message system : 프로세스 사이에 공유변수(shared variable)를 일체 사용하지 않고 통신하는 시스템
    * 분류
      * direct communication : 어떤 프로세스에게 메시지를 전달하는지 이름을 명시한다.
      * indirect communication : 누가 메시지를 받는지는 모르지만, mailbox(or port)를 통해서 메시지를 간접 전달한다.
  * 주소 공간을 공유하는 방법
    * shared memory
    * 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
    * 최초에 shared memory 공간을 마련하기 위해서 kernel 에게 요청을 해야한다. 그 이후로는 프로세스 간, 공유 메모리 공간을 통해서 공유가 가능하다.
  * thread 간의 협력
    * thread 는 사실상 하나의 프로세스이므로, 프로세스 간 협력으로 보기에는 어렵지만 동일한 process 를 구성하는 thread 들 간에는 주소공간을 공유하므로 협력이 가능하다.

<figure><img src="../../.gitbook/assets/image (9) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (2) (1).png" alt=""><figcaption></figcaption></figure>
