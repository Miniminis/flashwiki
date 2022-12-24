# Disk Management & Scheduling

<figure><img src="../../.gitbook/assets/image (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

### 디스크의 구조

* logical block
  * 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간 들
  * 주소를 가진 1차원 배열처럼 취급된다.
  * 정보를 전송하는 최소 단위
* sector
  * logical block 이 물리적인 디스크에 매핑되는 위치
  * sector 0 은 최외곽 실린더의 첫 트랙에 있는 첫번째 섹터이다.
    * 부팅과 관련된 정보가 저장된다.

### 디스크 운영

* physical formatting
* partitioning
* logical formatting
* booting

#### physical formatting

* 디스크 컨트롤러가 실제로 읽고 쓸 수 있도록 sector 단위로 나누는 과정을 의미한다.
* 각 섹터는 header + 실제 데이터 + trailer 로 구성되어있다.
* header + trailer 에는 sector number 및 ECC(Error-Correcting Code) 등의 정보가 저장되고 controller 가 직접 접근하고 운영한다.

#### partitioning

* 디스크를 하나 이상의 실린러 그룹으로 나누는 과정
* 운영체제는 이것을 독립적인 disk 로 취급한다. → logical disk

#### logical formatting

* 디스크 내 파일 시스템을 만드는 것
* FAT, inode, free space 등의 구조를 포함한다.

#### booting

* disk 에 있는 small bootstrap loader 를 실행한다.
* 그러면 boot sector 인 sector 0이 로드된다.
* sector 0에는 full bootstrap loader program 이고 부팅에 필요한 정보가 담겨있다.
* 이를 통해 운영체제 커널을 디스크에 load 하여 실행할 수 있다.

### 디스크 스케줄링

* access time
  * seek time : 헤드를 해당 실린더로 움직이는데 걸리는 시간
    * 가장 바깥쪽부터 안쪽까지 어디 트랙에 위치하는지 탐색하는 시간
  * rotational latency : 헤드가 원하는 섹터에 도달하기까지 걸리는 회전 지연 시간
    * 보통 seek time 의 10분의 1
  * transfer time : 실제 데이터의 전송 시간
    * 엄청 작은 시간
* disk bandwidth : 단위 시간당 전송된 바이트의 수
* disk scheduling
  * 결국 디스크 내 정보에 대한 접근 시간은 seek time 이 가장 많은 비율을 차지하게 된다.
  * 따라서 seek time 을 최소화하는 것이 목표가 된다.
  * seek time = seek distance

### 디스크 스케줄링 알고리즘

* FCFS
* SSTF
* SCAN
* C-SCAN
* N-SCAN
* LOOK
* C-LOOK

> 큐에 다음과 같은 실린더 위치의 요청이 존재하는 경우, 디스크 헤드 53번에서 시작한 각 알고리즘의 수행 결과는 어떻게 되겠는가…? (실린더 위치의 범위는 0-199)

* 98 → 183 → 37 → 122 → 14 → 124 → 65 → 67

#### FCFS

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

#### SSTF

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

#### SCAN

<figure><img src="../../.gitbook/assets/image (5) (2).png" alt=""><figcaption></figcaption></figure>

* 기본적으로 디스크의 스케줄링 알고리즘은 scan 이다.
* disk head 의 이동거리 측면에서 효율적이고 다른 알고리즘보다 공정하다.
* 문제점
  * 가운데 실린더는 대기 시간이 가장자리의 실런더들보다 짧다.

#### C-SCAN

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

* 비록 head 의 이동거리는 길어질 수 있지만, 큐 내부의 요청들의 대기 시간이 균일하고 공정해진다.

#### N-SCAN

* SCAN 의 변형 알고리즘
* 일단 arm 이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job 은 되돌아올 때 처리한다.
* 즉, 일단 스캔 루트가 한번 정해지면 가는 동안에 들어오는 요청들은 그 다음 턴에 처리하도록 한다.

#### LOOK

* SCAN의 변형
* SCAN 방법으로 처리하되, 특정 실린더 이후에 남아있는 요청이 없을 경우에는 굳이 끝까지 가지 않고 해당 지점에서 바로 턴을 한다.

#### C-LOOK

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

* C-SCAN 의 변형인데 역시 특점 지점 이후로 남아있는 요청이 없으면 바로 그자리에서 다른 방향으로 턴을 한다.

### 디스크 스케줄링 알고리즘의 결정

* SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있다.
* file 의 할당 방법에 따라 디스크 요청이 영향을 받는다.
* 디스크 스케줄링 알고리즘은 필요할 경우, 다른 알고리즘으로 쉽게 교체할 수 있도록 OS 와 별도의 모듈로 작성되는 것이 바람직하다.

### Swap-space management

#### disk 를 사용하는 이유

* memory 는 전원이 꺼지면 데이터가 모두 날아가는 휘발성을 가지고 있기 때문에 파일 시스템에 저장하는 것이 필요하다.
* 프로그램 실행을 위한 memory 공간이 부족하다보니, swap space, swap area 용도로 필요하다.

#### swap-space

* virtual memory system 에서는 디스크를 메모리의 연장 공간으로 사용한다.
* 파일 시스템 내부에 둘 수도 있으나 별도로 파티션을 구분하여 사용하는 것이 일반적이다.
* swap space 는 공간 효율성보다는 속도 효율성이 훨씬 중요하다. 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조되기 때문이다.
* 그래서 블록의 크기 및 저장 방식이 일반 파일 시스템과 다르다.

<figure><img src="../../.gitbook/assets/image (8) (2).png" alt=""><figcaption></figcaption></figure>

### RAID

* rebundant array of independent disks
* 여러개의 디스크를 묶어서 사용한다.
* 사용목적
  * 디스크 처리 속도가 향상을 위해서
    * 여러 디스크에서 블록의 내용을 분산 저장한 뒤,
    * 이를 병렬적으로 읽어오기 때문에 속도가 상당히 빠르다. → interleaving, striping
  * reliability 를 향상시키기 위해서
    * 동일 정보를 여러 디스크에 중복적으로 저장하기 때문에 하나의 디스크가 고장났을 때, 다른 디스크에서 읽어올 수 있다. → mirroring, shadowing
    * 단순한 중복 저장이 아니라 일부 디스크에 parity 를 저장하여 공간의 효율성을 높일 수 있다.
      * parity : 단순히 중복하여 저장하는 것이 아니라, 중복을 최소화하고 문제가 생겼을 때 복구할 수 있을 정도로만 저장해주는 방법
