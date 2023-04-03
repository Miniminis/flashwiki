# 프로그램의 실행, Program Execution

### 동기식 입출력(synchronous I/O)과 비동기식 입출력(asynchronous I/O)

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

| 구분     | 동기식 입출력(synchronous I/O)                                                           | 비동기식 입출력(asynchronous I/O)                               |
| ------ | ---------------------------------------------------------------------------------- | -------------------------------------------------------- |
| 동작방식   | I/O 요청 후, 입출력 작업이 완료되고나서야 제어가 사용자 프로그램으로 넘어간다.                                     | I/O 가 시작된 후, 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어간다. |
| 구현방법 1 | I/O 가 끝날때까지 CPU를 낭비시킨다. → 매 시점 하나의 I/O만 일어날 수 있음                                   |                                                          |
| 구현방법 2 | I/O가 완료될 때까지 해당 프로그램에게서 CPU 를 빼앗음 → I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움 → 다른 프로그램에 CPU를 줌 |                                                          |
| 공통점    | 두 경우 모두, I/O가 종료되었다는 것은 인터럽트를 이용해서 알려준다.                                           |                                                          |
| 예시     |                                                                                    | 보통 데이터를 삽입하는 write 의 경우, 비동기식으로 진행된다.                    |

### I/O 의 여러가지 방법

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

| 구분 | special instruction                                       | memory mapped I/O                                |
| -- | --------------------------------------------------------- | ------------------------------------------------ |
| 특징 | 메인 메모리에 접근하는 방법과 별도로 I/O 디바이스에 접근하기 위한 주소와 명령어가 별도로 존재한다. | 메인 메모리에 접근하는 것과 같은 방식으로 I/O디바이스에도 주소와 명령어로 접근한다. |

### 저장장치의 계층구조

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

| 구분     | Primary(Executable)        | Secondary              |
| ------ | -------------------------- | ---------------------- |
| CPU 접근 | CPU에서 직접 접근 가능             | CPU에서 접근 불가            |
| 접근 단위  | 바이트 단위로 접근                 | 섹터 단위로 접근              |
| 속도     | 빠르다                        | 느리다                    |
| 휘발성    | 컴퓨터 전원이 꺼지면 날라간다 → 휘발성이 높다 | 컴퓨터 전원과 상관없이 오래 남아있는다. |
| 비용     | 비싸다                        | 저렴하다                   |

* Cashing

### 프로그램의 실행(메모리 load)

<figure><img src="../../.gitbook/assets/image (8) (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 프로그램이 실행되면 각 실행파일별로 별도의 virtual memory 공간이 생기며 각각의 프로세스는 해당 공간 내에서 저장되고 조회된다.
* physical memory 영역에서는 virtual memory 의 모든 것을 저장하지 않는다. 메모리가 낭비될 수 있기 때문에 최대한의 효율을 위해 현재 실행되고 있는 부분의 데이터만 저장한다.
* 당장 사용하지 않는 데이터는 메인 메모리의 연장영역인 swap area 에 저장해둔다.

### 커널 주소 공간의 내용

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### 사용자 프로그램이 사용하는 함수

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 사용자 정의 함수
  * 자신의 프로그램에서 정의한 함수
* 라이브러리 함수
  * 자신의 프로그램에서 정의하지 않고 가져다가 쓴 함수
  * 자신의 프로그램의 실행파일에 포함되어 있다.
* 커널 함수
  * 운영체제 프로그램의 함수
  * 커널 함수의 호출 = 시스템콜

### 프로그램이 실행되는 과정

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>
