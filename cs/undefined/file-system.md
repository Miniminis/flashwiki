# File System

### File and File System

#### File

* a name of collection of related information
* 일반적으로 휘발되지 않는 보조기억장치에 저장된다.
* 운영체제는 다양한 저장장치를 file 이라는 동일한 논리적 단위로 볼 수 있도록 해줌
* operation 관련 연산들
  * open, close, create, write, delete, reposition(lseek)

#### File attribute

* file의 metadata
* 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
  * 파일 이름, 유형, 저장된 위치, 파일 사이즈
  * 접근권한 - 읽기, 쓰기, 실행
  * 시간 - 생성, 변경, 사용
  * 소유자 등

#### File system

* 운영체제에서 파일을 관리하는 부분
* 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
* 파일의 저장 방법 결정
* 파일 보호

### Directory and Logical Disk

* directory 역시 file 이다.
* 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일이다.
* 그 디렉토리에 속한 파일 이름, 파일 attribute emf
* operation
  * search for a file
  * create a file
  * delete a file
  * list a directory
  * rename a file
  * traverse the file system : 파일 시스템 전체를 탐색하는 연산

#### partition(=logical disk)

* 하나의 (물리적) 디스크 안에 여러 파티션을 두는 것이 일반적이다.
* 여러개의 물리적인 디스크를 하나의 파티션으로 구성하기도 한다.
* (물리적) 디스크를 파티션으로 구성한 뒤, 각각의 파티션에 file system 을 깔거나 swapping 등 다른 용도로 사용할 수 있다.

### Open()

* open() 은 파일을 여는 연산 명령으로 시스템 콜의 일종이다. 따라서 CPU 제어권은 모두 운영체제에게 있다.
* 일단 open(경로) 를 통해서 시스템 콜이 호출되면, CPU 제어권이 운영체제로 넘어가고, 운영체제는 기본적으로 가지고 있는 root 의 metadata 를 통해서 파일 시스템에 접근한다.
* metadata 내의 위치를 통해 file 의 콘텐츠를 가져오면, root 내부의 a 의 메타데이터를 확인한다.
* a 역시 디렉토리이므로 메타데이터가 존재한다. 역시 a의 메타데이터를 따라가 content 를 가져온다.
* a의 콘텐츠를 통해 b의 메타데이터를 가져오게 된다.
* b의 fd를 가져오고 나면 read(fd) 연산을 통해 b의 콘텐츠를 읽어온다.
* 이 때, 기존의 paging system 에서와는 다르게 file system의 경우, 명령어가 시스템콜로 이루어져있어서, 운영체제에 의해 파일 시스템 접근이 이루어지게 된다. 그렇기 때문에 LRU, LFU 등의 알고리즘도 사용이 가능하게 된다.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### File Protection

* memory에 대한 protection
  * read, write 권한이 있는가? 정도였다.
* file protection
  * 여러 사용자, 여러 프로그램이 같이 사용할 수 있다보니, 각 파일에 대해 1) 누구에게 2) 어떤 유형의 접근(read, write, execution)을 허용할 것인가를 같이 고민해야한다.
* Access control 3가지 방법
  * access control metrix
    * 사용자와 file 들에 대해서 행렬 형태로 권한을 표기해놓은 방법
    * 하지만 이 방법의 경우, 모든 파일들에 대해 이렇게 표시하면 메모리 낭비가 심하게 된다.
    * access control list : 파일 별로 누구에게 어떤 접근권한이 있는지 표시한다.
    * capability list : 사용자별로 자신이 접근 권한을 가진 파일 및 해당 권한을 표시한다.
    * 하지만 위의 두 방법 역시 오버헤드가 너무 심하다.
  * grouping
    * 일반적인 운영체제에서 선택하는 방법이다.
    * 전체 user를 owner, group, public 의 세 그룹으로 구분한다.
    * 각 파일에 대해 세 그룹의 접근 권한을 3비트씩으로 표시한다.
      * rwx/r—/r— : owner, group, public
      * 9개의 비트면 충분히 표현이 가능하다.
  * password
    * 파일마다 password 를 두는 방법이다. 디렉토리 파일에 두는 방법도 가능하다.
    * 모든 접근 권한에 대해 하나의 password : all-or-noting
    * 접근 권한 별 password 를 둘 경우, 암기문제, 관리문제 등이 발생할 수 있다.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



### File system 의 mounting

* disk1 은 root file system 이므로 모든 파일에 접근이 가능하다. 하지만 이 곳에서 disk2와 disk3는 어떻게 접근할까?
* 이 때 사용되는 것이 mounting 이다. root file system 내부에 있는 하나의 디렉토리에 다른 disk 의 file 를 mounting 하면 그것으로 다른 disk 의 root directory 를 탐색할 수 있으므로, 접근이 가능해진다.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Access method, 파일을 접근하는 방법

시스템이 제공하는 파일 정보의 접근 방식이다.

#### 순차접근

* sequential access
* 카세트 테이프를 사용하는 방식처럼 접근한다.
* 읽거나 쓰면 offset 은 자동적으로 증가하게 된다.

#### 직접접근

* direct access, random access
* LP 레코드 판과 같이 접근하도록 한다.
* 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있다.
