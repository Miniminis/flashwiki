# File Systems Implementation

### Allocation of file data in disk

* contigous allocation
* linked allocation
* indexed allocation

### contigous allocation

<figure><img src="../../.gitbook/assets/image (10) (5).png" alt=""><figcaption></figcaption></figure>

* directory 는 파일의 메타데이터를 가지고 있다.
* 위의 예를 통해서 보면, count 파일은 시작점 0번부터 길이가 2기 때문에 0, 1 에 위치하게 된다.
* 단점
  * external fragment 외부조각이 발생한다. 외부의 남는 공간이 발생한다.
  * 파일이 수정되었을 때, 파일 크기가 커지는 것에 대비하기가 어렵다.
    * 미리 파일이 커질 수 있는 것에 대비하여 최대 공간만큼 차지하도록 한다.
      * 얼마나 큰 hole 을 할당할 것인가…?
      * 공간 할당이 되지만 쓰이지 않는 내부 조각이 발생할 가능성이 있다.
* 장점
  * 빠르다
    * 한번의 seek/rotation 으로 많은 바이트 transfer
    * realtime file 용으로, 또는 이미 run 중이던 process 의 swapping 용도로 쓰인다.
      * swapping : 공간 효율성보다는 속도가 훨씬 중요한 경우
  * direct access (=randome access) 가 가능하다.
    * 앞에 선행하는 파일들을 모두 거치지 않아도 내가 보고 싶은 특정한 파일만 볼 수 있다.

### linked allocation

<figure><img src="../../.gitbook/assets/image (4) (3).png" alt=""><figcaption></figcaption></figure>

* 파일의 시작위치만 디렉토리가 가지고 있고, 그 다음 위치들은 해당 파일 내에 접근하여 확인할 수 있다.
* 장점
  * continous allocation 연속할당과 다르게 파일의 길이가 안맞아 사용되지 못하는 외부조각이 발생하지 않는다.
* 단점
  * 직접접근이 불가하다.
    * 4번째 블록의 내용이 궁금하면 1→2→3→4 순서대로 탐색을 하여 내용을 확인해야 한다.
    * 즉, 건너 뛰는 것이 불가하다.
  * 순차접근에 대해서 탐색 시간이 오래 소요된다.
  * 안정성 측면에도 문제가 있다. reliability
    * 한 sector 에 문제가 생기면 그 이후 포인터의 위치를 알 수 없기 때문에 파일 전체를 접근하지 못하게 된다.
  * 다음 포인터의 위치를 저장하느라고 파일 블록 공간을 차지하기 때문에 온전히 공간을 사용할 수 없다는 문제가 있다.
    * 512bytessector
    * 4bytes/pointer
* 단점의 보완
  * file-allocation table, FAT 파일 시스템
    * 포인터를 별도의 위치에 보관하여 reliability 와 공간 효율성 문제를 해결한다.

### indexed allocation

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

* 디렉토리 내에서 index block 의 위치를 가지고 있고
* 해당 인덱스로 접근하면 모든 파일들의 내용에 접근 가능하다.
* 장점
  * 순차접근이 아니다. index 만 접근하면 접근하고자 하는 위치에 바로 접근할 수 있다.
  * 외부 공간이 발생하지 않는다.
* 단점
  * 작은 공간이라고는 하나 index를 위한 블록이 별도로 필요하다. 작은 파일일 경우, 공간낭비가 발생할 수 있다. 그리고 실제로 많은 파일들은 그 크기가 작다.
  * 또 너무 큰 파일의 경우는 하나의 블록으로 index 를 저장하기에 부족하다.
    * linked scheme 을 사용한다. index 공간이 부족해지만 가장 마지막 줄에 2번째 index 의 위치를 남긴다. 그러면 순차적으로 다음 index 로 접근이 가능하다.
    * multi level index 를 사용한다. 2단계 페이징 테이블처럼 index 안에 다른 index 의 위치를 나열한다.

> 위의 세 가지 할당 방법은 모두 이론적인 것이다. 그렇다면 실제로는 어떤 식으로 파일 데이터를 저장하고 보관하고 있을까

### UNIX 파일 시스템의 구조

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

* boot block
  * 모든 컴퓨터의 파일시스템에 동일하게 첫번째로 존재한다.
  * 부팅에 필요한 정보, bootstrap loader
* super block
  * 파일 시스템에 관한 총체적인 정보를 담고 있다.
    * inode 의 길이, 시작점 등
* inode list
  * inode = index node
  * 파일 하나 당 1개의 inode 가 할당된다.
  * 실제 파일 시스템에서는 디렉토리가 모든 파일의 메타데이터를 가지고 있지는 않는다. 실제 파일의 메타데이터들은 별도의 공간에서 관리를 하고 있는데, 그 공간이 바로 inode list 이다.
  * 파일 이름을 제외한 파일의 모든 메타데이터는 이곳에 저장되어있다.
  * 적은 inode 공간으로 효율적인 파일 데이터 접근 관리
    * direct block : 바로 파일데이터로 접근 가능
    * single indirect : 한번 index 를 거쳐서 데이터에 접근 가능
    * double indirect
    * triple indirect
* data block
  * 파일의 실제 내용을 보관한다.
  * 파일의 메타데이터 중, 제목을 가지고 있다.
  * directory file : \<file 이름 + inode 번호> 형태로 이루어져있다.

### FAT file system

<figure><img src="../../.gitbook/assets/image (8) (6).png" alt=""><figcaption></figcaption></figure>

* boot block
* FAT
  * 파일의 메타데이터 중 위치정보를 FAT 이 가지고 있다.
  * data block 의 개수만큼 존재한다.
* root directory
* data block
  * 파일의 메타데이터 중 위치정보를 제외한 모든 정보를 directory 가 가지고 있다.
  * directory file : \<file 이름 - FAT 시스템 번호>
* 장점
  * 직접접근이 가능하다. FAT 파일 시스템 내부의 번호 연결만 따라가면 직접 data block 에 접근하여 열어보지 않아도 직접 원하는 파일에만 접근이 가능하다.
  * 한 sector 가 고장나서 pointer 가 유실되더라도 FAT 내부에 위치정보가 있으니 reliability 가 보장된다.

> 위의 두 가지 파일시스템의 구조만 보았지만, 실제로는 다양한 구조들이 존재하고 변형되어 쓰인다.

### Free-space management

* bit map/bit vector
* linked list
* grouping
* counting

#### bit map/bit vector

* 0, 1로 블록이 사용중인지 표시한다.
* 단점 : 디스크 내부의 부가적인 공간을 필요로 한다.
* 장점 : 연속적인 n개의 free block 을 찾는데 효과적이다.

<figure><img src="../../.gitbook/assets/image (7) (3).png" alt=""><figcaption></figcaption></figure>

#### linked list

* 모든 free block 들을 링크로 연결한다. free list
* 연속적인 가용 공간을 찾는 것이 쉽지 않다.
  * 따라서 현실에서 사용하기에는 쉽지 않다.
* 하지만 공간의 낭비가 없다.

<figure><img src="../../.gitbook/assets/image (6) (3).png" alt=""><figcaption></figcaption></figure>

#### grouping

* linked list 방법의 변형
* 첫번째 free block 이 n개의 포인터를 가진다.
  * n-1 pointer 는 free data block 을 가리킨다.
  * 마지막 pointer 가 가리키는 block 은 또다시 n pointer 를 가진다.

#### counting

* 프로그램들이 종종 여러개의 연속적인 block 을 할당하고 반납한다는 성질에 착안한다.
* (first free block, 연속적인 free blocks) 쌍 형태로 관리한다.

### directory implementation, 디렉토리는 어떻게 구현되는가

* linear list
* hash table

### linear list

* \<file name, file metadata> 의 list
* 구현이 간단하다.
* 디렉토리 내에 파일이 잇는지 찾기 위해서는 linear search 가 필요하다.
  * time-consuming

#### hash table

* linear list + hashing
* hash table은 파일 이름을 이 파일의 linear list 의 위치로 바꾸어준다.
* search time 을 없앴다.
* collision 이 발생할 가능성이 있다.
  * 서로 다른 결과값이 같은 entry 로 매핑되는 문제
  * hash 함수에서 흔히 발생하는 문제

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

#### file metadata 보관위치

* 디렉토리 내에 직접 보관하는 방법과
* 디렉토리에는 포인터를 두고 다른 곳에 보관하는 방법
  * inode, FAT 등

#### Long file name 지원

* \<file name, file의 메타데이터>의 list 에서 각 entry 는 일반적으로 고정 크기
* file name 이 고정 크기의 entry 길이보다 길어지는 경우, entry 의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법
* 이름의 나머지 부분은 동일한 directory file 의 일부에 존재한다.

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

### VFS and NFS

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

#### VFS, virtual file system

* 서로 다른 다양한 file system 에 대해 동일한 시스템 콜 인터페이스 api 를 통해 접근할 수 있게 해주는 OS layer

#### NFS, network file system

* 원격에 저장되어있는 파일 시스템에 접근을 해야하는 경우
* 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있다. NFS는 이러한 분산환경에서 대표적으로 파일을 공유하는 방법이다.
* 서버와 클라이언트 모두 NFS 모듈이 있어야 한다.
  * NFS client
  * NFS server

### page cache vs. buffer cache

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

#### page cache

* virtual memory 의 paging system 에서 사용하는 page frame 을 caching 의 관점에서 설명하는 용어
* memory-mapped i/o 를 쓰는 경우, file 의 i/o 에서도 page cache 를 사용한다.

#### buffer cache

* 파일 시스템을 통한 i/o 연산은 메모리의 특정 영역인 buffer cache 를 사용한다.
* 파일 사용의 locality 를 활용한다.
  * 한번 읽어온 block 에 대한 후속 요청 시, buffer cache 에서 즉시 전달한다.
* 모든 프로세스가 공용으로 사용된다.
* replacement algorithm이 필요하다. LRU, LFU 등

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

#### unified buffer cache

* 최근의 OS 에서는 기존의 buffer cache 가 page cache 에 통합된다.
* buffer cache 를 위한 별도의 공간을 분리해두지 않고, page cache 에서 필요할 때마다 할당해서 쓴다.
* unified buffer cache 를 이용하지 않을 때
  * I/O read() & write() 를 실행하면, buffer cache를 거쳐서 캐시 내 존재하면 바로 응답하고, 없다면, file system 에서 가져와 buffer cache 에 등록한 뒤, 응답해준다.
  * memory-mapped I/O 를 실행하면, page cache 를 일단 검사한 뒤, 없다면 buffer cache 를 검사하고, 그곳에도 없을 때, file system 에 접근하여 가져온다.
* unified buffer cache 를 이용할 때
  * 통합된 버퍼 캐시를 사용할때는 이러한 과정이 보다 단순해진다.
  * page cache 를 buffer cache 로 쓰기 때문에 실행 프로세스가 한결 간단해진다.

<figure><img src="../../.gitbook/assets/image (5) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (6).png" alt=""><figcaption></figcaption></figure>

#### memory-mapped i/o

* file의 일부를 virtual memory 에 mapping 시킨다.
* 매핑시킨 영역에 대한 메모리 접근 연산을 통해 파일의 입출력을 수행할 수 있다.
* 위의 그림에서 보면, 프로세스 내부의 `code` 영역이 memory-mapped i/o 를 사용하는 가장 대표적인 예이다.
  * 프로세스가 실행될 때, 물리적 메모리로 로드되고, 프로세스가 스위칭 되면, 대부분의 데이터는 swap area 로 이동하여 임시 보관된다.
  * 하지만 code 영역은 이미 실행파일 자체에서 모든 것이 쓰여진 완성된 형태의 read only 영역이다. 따라서 프로세스가 스위칭 되는 당시의 상태를 저장하는 swap area 에 저장할 필요가 없이, virtual memory 자체에 매핑되어 읽으면 된다.
  * virtual memory 에 없는 내용일 경우, 페이지 폴트가 발생할 것이다. file system 인 실행파일에서 필요한 코드의 내용을 또 일정부분 불러와 virtual memory 와 매핑시켜 읽으면 된다.

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

> 결국, 사용자가 file system 에 접근하는 방법에는 두 가지 인터페이스가 있다.

1. I/O read() & write() 함수를 이용하는 경우이고,
2. memory-mapped I/O 를 이용하는 경우이다.

#### I/O read() & write() 함수를 이용하는 경우

unifed buffer cache 에서 1번을 이용할 경우에는 physical memory 에 매핑된 데이터 자체가 이미 buffer cache 이므로, 요청된 내용이 존재하면 그 부분을 `copy` 하여 프로세스에게 응답한다. 기존 물리적 메모리에 있는 정보를 매핑하는 것이 아니라 복사해서 전달하기 때문에, 데이터 일관성 문제가 발생할 여지가 없고 운영체제가 중간에 항상 중재를 하기 때문에 관리에 좀 더 수월하다는 장점이 있다.

#### memory-mapped I/O 를 이용하는 경우

2번일 경우에는 physical memory 에 올려져있는 데이터를 `virtual memory 에 매핑`하여 사용한다. 이 경우, 일단 메모리에 올려진 데이터는 운영체제를 시스템 콜로 호출하지 않고 자신의 메모리 영역 내에서 응답할 수 있기 때문에 빠르고 물리 메모리에서 나의 가상 매모리로 데이터를 복사하는 시간이나 노력을 들이지 않을 수 있다.

단점이 있다면, physical memory 를 다른 프로세스들과 공유해서 이용하므로, 데이터의 일관성 문제가 발생할 수 있다.
