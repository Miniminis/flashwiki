# Memory Allocation

### 물리적 메모리의 할당

* 메모리는 일반적으로 두 영역으로 나뉘어 사용된다.
  * OS 상주 영역에는 아주 낮은 주소 영역에 interrupt vector 와 함께 존재한다.
  * 사용자 프로세스 영역에는 높은 주소 영역을 사용한다.
* 이 사용자 프로세스 영역에 메모리를 할당하는 방법에는 다시 또 두 가지가 있다.
  * continuous allocation은 각각의 프로세스가 메모리의 연속적인 공간에 적재가 된다.
    * fixed partition allocation
    * variable partition allocation
  * noncontinuous allocation 은 하나의 프로세스가 메모리의 여러 영역에 나뉘어 올라간다.
    * paging
    * segmentation
    * paged segmentation

### 1) continuous allocation

* fixed partition allocation 고정분할방식
  * fixed partition allocation 을 따르면 메모리의 공간이 몇개의 영구적 partition으로 분할된다.
  * 파티션의 크기가 모두 동일한 방식과 서로 다른 방식이 존재하며
  * 파티션당 하나의 프로그램이 적재될 수 있다.
  * 융통성이 없어서 동시에 메모리에 로드되는 프로그램의 수가 제한적이게 되며, 최대 수행 가능한 프로그램의 크기도 제한된다.
  * 프로그램이 파티션과 맞지 않다면, 외부조각 external fragment 이나 내부 조각 internal fragment이 생기게 된다.
* variable partition allocation 가변분할방식
  * 프로그램이 실행될 때마다 차곡차곡 메모리에 올려두는 방식으로
  * 프로그램의 크기를 고려해서 할당된다. 분할의 크기와 개수가 동적으로 변한다.
  * 이 방법을 위해서는 기술적으로 관리 기법이 갖추어져야한다.
  * 가변분할방식도 프로그램 실행에 따라서 외부조각이 발생할 수 있는데 예를 들면 아래와 같은 상황이다.
    * 프로그램 B의 수행이 끝나도 프로그램D가 B가 있던 공간에 들어가지 못하기 때문에, C뒤에 위치하게 된다. 이때 외부조각이 발생할 수 있다.

<figure><img src="../../.gitbook/assets/image (5) (3) (2).png" alt=""><figcaption></figcaption></figure>

#### hole

* 가용메모리 공간
* 다양한 크기의 hole 들이 여러 메모리 공간에 흩어져 있다.
* 프로세스가 도착하면 수용 가능한 hole 을 할당한다.
* 따라서 운영체제는 다음의 정보를 항상 숙지하고 있다. → 1) 할당 공간 2) 가용공간 hole

<figure><img src="../../.gitbook/assets/image (15) (2) (2).png" alt=""><figcaption></figcaption></figure>

#### dynamic storage allocation problem

* 가변분할 방식에서 size 가 n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
* 해결방법에는 3가지가 있다.
  * first fit : size n을 만족하는 hole 중 가장 최초로 찾아지는 것에 할당하는 방법
  * best fit : size n을 만족하는 hole 중 가장 작은 것에 할당하는 방법
    * hole들의 리스트가 크기순으로 정렬되지 않는 경우, 모든 hole 들을 탐색해야한다.
    * 많은 수의 아주 작은 hole 들이 생성될 수 있다.
  * worst fit : size n을 만족하는 가장 큰 hole에 할당하는 방법
    * 모든 리스트를 탐색해야하며
    * 큰 hole 들이 생성된다.
* 당연하게도 first fit 은 시간적으로, best fit 은 공간 이용률 측면에서 각각 가장 유리하며, worst fit은 가장 비효율적인 것이 실험 결과 나타났다.

#### compaction

* external fragmentation 외부조각 문제를 해결하기 위해서 사용중인 메모리 영역을 한 군데로 몰고 hole 들을 다른 한 곳으로 모아 아주 큰 hole 을 만드는 방법이다.
* 비용이 매우 많이 들며, 최소한의 이동으로 메모리를 compaction 하는 방법은 매우 복잡하다.
* 당연하지만, compaction 기법은 프로세스의 주소가 실행되는 시간에 동적으로 재배치가 가능한 경우에만 사용 가능하다.

### 2) noncontinuous allocation

* paging
  * 프로그램을 구성하는 주소 공간을 페이지 단위로 자른다. 물리적 메모리의 공간도 같은 단위로 자른다.
  * 주소 변환이 더 복잡해지기 때문에 address binding 이 더 복잡해진다.
* segmentation
  * 의미있는 단위로 자르는 것
* paged segmentation

#### paging

<figure><img src="../../.gitbook/assets/image (9) (5).png" alt=""><figcaption></figcaption></figure>

* 프로세스의 virtual memory 를 동일한 사이즈의 page 단위로 나눈다.
* virtual memory 의 내용이 page 단위로 noncontinous 하게 저장된다.
* 일부는 backing storage 에, 일부는 physical memory 에 저장된다.
* basic method
  * physical memory 를 동일한 크기의 frame 으로 나눈다.
  * local memory 를 동일한 크기의 page 로 나눈다. → frame 과 같은 크기!
  * 모든 가용 frame 들을 관리한다.
  * page table 을 사용하여 logical address를 physical address 로 변환한다.
    * page 단위로 나누어지기 때문에 더이상 두 개의 register 로는 불가하다.
  * external fragment 는 발생하지 않지만, 여전히 internal fragmentation 이 발생할수는 있다.
    * 페이지 단위로 나누게 되면, 페이지 범위를 다 채우지 못해 남는 공간이 발생할 수 있다는 뜻.
* page table 은 physical memory 에 상주한다.
  * page table base register 가 page table 을 가리키고 있고,
  * page table length register 는 테이블의 크기를 보관한다.
  * 따라서 모든 메모리 접근 연산에는 2번의 memory access 가 필요한데, 한번은 page table 에 접근하기 위해서, 1번은 실제 데이터와 인스트럭션에 접근하기 위해서 필요하다.

<figure><img src="../../.gitbook/assets/image (4) (2) (1).png" alt=""><figcaption></figcaption></figure>

* 속도 향상을 위해서 `associative register` OR `translation look-aside` buffer 라고 불리는 고속의 lookup hardware cache 를 사용한다.
  * associative resiters (TLD) : parallel search 가 가능하다. page table 테이블 중 일부만 존재한다. 없으면 page table 로 이동함
  * address translation 과정
    * page table 중 일부가 associative regieter 에 보관되어있다.
    * 만약 해당 page 번호가 존재하는 경우, 곧바로 frame 번호를 얻게 된다.
    * 하지만 존재하지 않은 경우, main memory 에 있는 page table로부터 frame 번호를 얻는다.
    * TLB 는 context switch 때 flush (remove old entiries) 된다. → 프로세스별로 별도로 존재하기 때문에

<figure><img src="../../.gitbook/assets/image (29) (1) (1).png" alt=""><figcaption></figcaption></figure>

* 실제 메모리에 접근하는 시간은?
  * 상당히 빠르다!

#### 2단계 페이징 테이블 - two level paging table

<figure><img src="../../.gitbook/assets/image (5) (4).png" alt=""><figcaption></figcaption></figure>

* 사용하는 이유
  * 컴퓨터에서 어떤 시스템이 존재할 때, 그 존재의 이유는 보통 두 가지이다.
    * 시간을 빠르게 하거나
    * 공간을 효율적으로 하거나
  * two level paging table 의 경우, 속도는 중간 과정이 추가 되기 때문에 오히려 더 늘어날 것이라는 것을 알 수 있다. 그래서 아마 `공간을 효율적`으로 하지 않을까, 추측할 수 있다.
* 현대의 컴퓨터는 32bit, 64bit 단위이다. 그렇다면, 프로그램마다 가지고 있는 Maximum 주소체계는 몇 개나 되겠는가? → 컴퓨터의 32bit, 64bit 에 따라서 나누어진다.
  * 32bit 컴퓨터의 경우, 0번부터 2^32번까지의 메모리 주소를 가질 수 있다.
  * 그런데 2^10 = K 이고, 2^20 = M이고, 2^30 = G이므로, `2^32 = 2^2 * G = 4GB` 를 의미하게 된다.
  * 이때, 페이지 사이즈가 4K라면, 1M개의 page table entry 가 필요하게 된다.
  * 각 page entry가 4B일 때에는, 프로세스 당 4M의 page table 이 필요하다.
  * 그러나 대부분의 프로그램은 4G의 주소공간 중에서 지극히 일부분만 사용하므로, page table 공간이 심하게 낭비된다.
* page table 자체를 page 로 구성한다.
* 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 null 이다.

<figure><img src="../../.gitbook/assets/image (3) (6) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (4).png" alt=""><figcaption></figcaption></figure>

#### multi-level paging and performance

* address space 가 더 커지면 2단계 페이징 테이블만으로는 부족하다. 다단계 페이지 테이블이 필요하다.
* 각 단꼐의 페이지 테이블이 메모리에 존재하므로 logical address 의 physical address 변환에 더 많은 메모리 접근이 필요하다.
* TLB 를 통해 메모리 접근 시간을 줄일 수 있다.
* 4단계 페이지 테이블을 사용하는 경우,
  * 메모리 접근 시간이 100ns, TLB 접근 시간이 20ns 이고 TLB hit ratio 가 98%인 경우,
    * 총 5번의 메모리 접근이 필요하다 → 4번은 페이지 주소변환, 1번은 데이터 접근
    * effective memory access time = 0.98 \* 120 + 0.02 \* 520 = 128 nano seconds
    * 98%는 TLB 를 통해서 이루어 질테니, 20ns정도만 주소변환에 소요된다. 이 경우, 메모리 접근시간+주소변환시간 = 120ns 가 된다.
    * 반면 TLB 를 통해 이루어지지 못한 2%의 경우는, 주소변환에만 400ns 가 소요된다. 그리고 TLB 에 없다는 것을 확인하는 작업에 20ns, 실제 메모리에 접근하는 시간으로 다시 100ns 가 소요된다. 즉, 총 520ns 가 소요되는 것이다.
    * 결과적으로 주소 변환을 위해 28ns 만 소요된다.
      * 실제 메모리 접근을 위한 100ns + 주소변환을 위한 28ns

#### memory protection - page table 에서 valid, invalid bit 표현

* page table 의 각 entry 마다 아래의 bit 를 둔다.
* protection bit : page 에 대한 접근 권한 (read, write, read-only)
* valid
  * 해당 주소의 frame 에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함. → 접근 허용
  * 테이블에 매핑된 실제 물리적 메모리 주소로 가면 데이터를 찾을 수 있다.
* invalid
  * 해당 주소의 frame 에 유효한 내용이 없음을 뜻한다. → 접근 불허
  * 프로세스가 그 주소 부분을 사용하지 않거나
  * 해당 페이지가 메모리에 올라와있지 않고 swap area에 있는 경우를 의미한다.

<figure><img src="../../.gitbook/assets/image (28) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### inverted page table

* page table 이 매우 큰 이유
  * 모든 프로세스별로 그 logical address 에 대응하는 모든 page 에 대해 page table entry 가 존재하기 때문이다.
  * 대응하는 page 가 메모리에 있든 아니든 간에 page table 에는 entry 로 존재한다.
  * 그래서 이 낭비되는 공간을 줄이기 위해 등장한 것이 inverted page table!
* inverted page table 에서는 physical memory 에 접근하면 page table 에서 매핑된 logical memory 주소를 거꾸로 되찾아간다.
* 즉, 물리적 메모리 주소에 맞게 논리적 주소를 바꾸는 형식!
* page frame 하나 당, page table 에 하나의 entry 를 둔 것이다.
* 각 page table entry 는 각각의 물리적 메모리의 page frame 이 담고 있는 내용을 표시한다.
  * process id, process logical address
* 단점 : 테이블 전체를 탐색해야한다.
* 해결 : associative register 를 사용한다. (하지만 너무 비싸다;;)

<figure><img src="../../.gitbook/assets/image (2) (5).png" alt=""><figcaption></figcaption></figure>

#### shared page

* shared code
  * re-entrant code = pure code
  * `read-only` 로 하여 프로세스 간에 하나의 code 만 메모리에 올린다.
  * shared code 는 모든 프로세스의 Logical address space 에서 동일한 위치에 있어야 한다.
* private code and data
  * 각 프로세스들은 독자적으로 메모리에 올린다.
  * private data 는 logical address space 의 아무 곳에 와도 무방하다.

<figure><img src="../../.gitbook/assets/image (1) (3) (2) (1).png" alt=""><figcaption></figcaption></figure>

#### segmentation

<figure><img src="../../.gitbook/assets/image (12) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

* 의미단위로 범위를 나눈 것. 예를 들면 메모리 내의 코드, 스택 등의 공간
  * 의미단위로 범위를 나누기 때문에 segment의 크기가 균일하지 않다.
* logical address 는 다음의 두 가지로 구성된다.
  * segment-number
  * offset → d
* segment table
  * 모든 테이블은 base 와 limit 값을 가지고 있다.
  * base : starting physical address of segment. 즉, 물리적 메모리에서 세그먼트의 시작점을 나타낸다.
  * limit : lenght of the segment. 세그먼트의 길이를 나타낸다.
* segment table base register - STBR
  * 물리적 메모리에서의 segment table의 위치
* segment table limit register - STLR
  * 프로그램이 사용하는 segment의 수
  * segment number s < STLR 이어야 한다.
* 페이징 기법과 다른 점
  * 페이징 기법의 경우, 모든 페이지의 단위가 같기 때문에 물리적 메모리 내부에 fragment 위치를 table 에 매핑하고 있으면 되었지만,
  * 세그먼트 기법의 경우, 모든 세그먼트의 단위가 다르기 때문에 테이블에는 정확한 물리적 메모리의 byte 주소를 가지고 있어야 한다.
* 장점
  * protection
    * 각 세그먼트별로 protection bit 가 존재한다.
    * 각각의 entry 가 아래를 만족함
      * valid bit = 0 이면, illegal segment 라고 판단한다.
      * read, write, execution 권한 bit → 의미단위로 의미를 나누기 때문에 세그먼트별로 권한 분할이 가능하다.
  * sharing
    * shared segment
    * same segment number
    * segment 는 의미 단위이기 때문에 공유와 보안에 있어서 paging 보다 훨씬 유리하다.
  * paging 기법과 비교해보았을 때, 훨씬 페이징 테이블 개수가 적어서 메모리 낭비가 적다.
    * paging : 100만개
    * segment : 5개
* 단점
  * allocation : first fit, best fit
    * 의미 단위로 길이가 유동적인 세그먼트들이 쌓이기 때문에 가장 첫번째로 쌓인 것이 잘 쌓이게 된다.
  * external fragmentation 이 발생한다.
    * segment 길이가 동일하지 않으므로, 가변 분할 방식에서와 동일한 문제점들이 발생하게 된다. → hole
    * allocation 문제가 발생할 수 있다.

<figure><img src="../../.gitbook/assets/image (1) (1) (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (4).png" alt=""><figcaption></figcaption></figure>

#### paged segmentation

* paging + segmentation
* paging 기법과 segmentation 기법의 장점을 가져와 융합해놓은 방법으로
* segment table 에는 segment 자체의 base address 가 아니라, segment 를 구성하는 page table 의 base address 를 가지고 있다.
* 실제 컴퓨터에는 segment 로만 이루어진 경우는 거의 없고 사실상 paging 기법을 같이 사용하고 있다.

<figure><img src="../../.gitbook/assets/image (2) (2) (2).png" alt=""><figcaption></figcaption></figure>

### 지금까지…

* 지금까지 우리는 다양한 물리적 메모리의 관리 기법에 대해서 알아보았다.
* 물리적 메모리 관리에서 가장 핵심이 되는 것은 결국 논리적 주소를 물리적 메모리 주소로 변환하는 과정이었다. → `주소변환`
* 하지만 이 모든 과정에서 운영체제가 하는 일은 거의 없었다. 모두 하드웨어가 수행하는 일들이었다.
* 왜 하드웨어에 의지할 수 밖에 없을까?
  * 하나의 프로세스가 CPU를 가지고 논리적 메모리 주소에서 물리적 메모리 주소로 변환을 하는 과정에는 사실상 운영체제가 끼어들 필요가 없다.
  * 운영체제의 개입이 필요하다는 말은 프로세스가 사용자 → 운영체제로 넘어간다는 것을 의미하는데, 메모리를 참조할 때마다 운영체제와 사용자 사이를 이동해야한다는 것은 말이 안된다.
  * 따라서 이 모든 과정은 하드웨어적으로 이루어질 수 밖에 없다.
  * 운영체제가 끼어들어야 하는 케이스는 바로 I/O 접근 때였다.
* 그렇다면 운영체제는 무엇을 하는가…?
  * 물리적 메모리 주소 변환에서는 사실상 운영체제가 하는 일은 없다.
  * 하지만 virtual memory 관리에서는 운영체제의 역할이 늘어난다.
