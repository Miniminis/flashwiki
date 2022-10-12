# Virtual Memory 2

### 다양한 caching 환경

* caching 기법
  * 한정된 빠른 공간 (=캐시)에 요청된 데이터를 저장해두엇다가 후속 요청시 캐쉬로부터 직접 서비스 하는 방식
  * paging system 외에도 cache memory, buffer caching, web caching 등 다양한 분야에서 사용된다.
* cache 운영 시간 제약
  * 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우, 실제 시스템에서 사용할 수 없다.
  * buffer caching 이나 web caching 의 경우, O(1) \~ O(log n) 정도 까지만 허용된다.
  * paging system 의 경우
    * page fault 인 경우에만 OS 가 관여한다.
    * 페이지가 이미 메모리에 존재하는 경우, 참조시각 등의 정보를 OS 가 알 수 없다.
    * O(1) LRU 의 list 조작 조차 불가능하다.

<figure><img src="../../.gitbook/assets/image (2) (6).png" alt=""><figcaption></figcaption></figure>



### paging system 에서는 그럼 replacement 를 위해 어떤 알고리즘을 사용하는가

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

#### Clock algorithm

* LRU 와 유사한 알고리즘
* 여러 명칭으로 불리고 있다.
  * second change algorithm
  * NUR(Not Used Recently) or NRU(Not Recently Used)
* 동작방식
  * reference bit 을 사용해서 교체 대상 페이지를 선정한다. → circular list
  * CPU가 페이지를 읽어들여 참조하게 되면 하드웨어는 reference bit 을 1로 바꾼다.
  * 운영체제는 circular list 을 돌면서 reference bit 이 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동한다.
  * 포인터는 이동하면서 reference bit 1 → 0으로 바꾼다.
  * reference bit = 0 인 것을 찾으면 그 페이지를 내보낸다 (교체한다)
  * 한 바뀌 되돌아와서도 (=second chance) reference bit 가 여전히 0이면, 그때는 replace 당한다.
  * 자주 사용되는 페이지라면 second chance 가 올 때 1로 바뀌어있을 것이다.
    * 결국 reference bit = 1 은 최근에 참조된 페이지임을 뜻한다.
* 이 방법으로 어느정도 LRU 와 비슷한 효과를 낼 수 있다.
* 개선방법
  * reference bit 과 modified bit (dirty bit)을 함께 사용한다.
  * reference bit = 1 : 최근에 참조된 페이지
  * modified bit = 1 : 최근에 변경된 페이지 (I/O를 동반하는 페이지) / write 된 페이지
    * 메모리에 올라온 이유로 적어도 CPU 에서 한번은 수정이 가해진 상태이다.
    * 이 페이지가 replace 대상이 될 경우, 그냥 메모리에서 내쫓는 것이 아니라 backing store 에 저장을 한 후, 내쫓아야한다.

### page frame 의 allocation

* 각 프로세스에 얼만큼의 페이지 프레임을 할당할 것인가? 라는 질문에서 출발한다.
* allocation 의 필요성
  * 메모리 참조 명령어 수행시, 명령어, 데이터 등 여러 페이지를 동시에 참조하기 때문
    * 명령어 수행을 위해서 최소한 할당되어야하는 frame 의 수가 있다.
  * loop 를 구성하는 페이지들은 한꺼번에 allocate 되는 것이 유리하다.
    * 최소한의 allocation 이 없으면 매 loop 마다 page fault 가 나게 된다.
* allocation scheme
  * equal allocation : 모든 프로세스에 똑같은 개수를 할당한다.
  * proportional allocation : 프로세스 크기에 비례하여 할당한다.
  * priority allocation : 프로세스의 priority 에 따라 다르게 할당한다.

### glocal vs. local replacement

* gloval replacement
  * replace 시에 다른 프로세스에 할당된 프레임을 빼앗아 올 수 있다.
  * 프로세스별로 할당량을 조절하는 또 다른 방법이 된다.
  * FIFO, LRU, LFU 등의 알고리즘을 global replacement 로 사용시에 해당한다.
  * 할당 효과가 있는 `working set`, `PFF` 알고리즘을 사용한다.
* local replacement
  * 자신에게 할당된 frame 내에서만 replacement 를 한다.
  * FIFO, LRU, LFU 등의 알고리즘을 process 별로 운영할때 사용한다.

### Thrashing

* 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당받지 못한 경우에 발생한다.
* 발생과정
  * multiprogramming 정도를 계속 높이 경우, 메모리 사용량이 계속 증가하므로, page fault rate 이 매우 높아진다.
  * 그렇게 되면 CPU는 딱히 할일이 없어서 그 사용률이 매우 낮아지게 되고,
  * CPU 가 놀고 있는 것을 본 운영체제 OS 는 다시 multi programming degree MPD 를 높여야한다고 판단하게 된다.
  * 또 다른 프로세스가 시스템에 추가되면 multi-programming degree MPD 가 더 높아지고
  * 프로세스당 할당된 frame 수가 더욱 감소하게 되면서
  * 프로세스는 page의 swap in / swap out 으로 매우 바쁘게 된다.
  * 대부분의 시간에 CPU는 한가하게 된다.
  * low throughtput
* 예방하는 방법
  * multi-programmind degree 를 조절해주어야 한다.
  * 이를 위해 working-set, PFF 알고리즘을 사용한다.

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>



### Working-Set Model

* locality of reference
  * 프로세스는 특정 시간동안 일정 장소만을 집중적으로 참조한다.
  * 집중적으로 참조되는 대상 page 들의 집합을 locality set 이라고 한다.
* working-set model
  * locality 에 기반하여 프로세스가 일정 시간동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 잇어야 하는 page 들의 집합을 `working set` 이라고 정의한다.
  * working set 모델에서는 process 의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우, 모든 frame 을 반납한 후 swap out (suspend)
* 이 모델을 통해서thrashing 을 방지할 수 있고
* multi-programming degree 를 결정할 수 있다.

#### working-set 은 그러면 어떻게 결정할 수 있을까

* working set window 를 통해서 알아낸다.
* working set 단위를 정한다. A 라고 가정해보았을 때,
* 시각 t에서의 working set WS(t) 는
  * time inverval \[t-A, t] 사이에 참조된 서로 다른 페이지들의 집합이 된다.
* working set 에 속한 page 는 메모리에 유지하고, 속하지 않는 것은 바로 버린다.
* 다시 말하자면, 참조된 후 A만큼의 시간동안 해당 page 를 메모리에 유지한 후 버리게 된다.

<figure><img src="../../.gitbook/assets/image (4) (4).png" alt=""><figcaption></figcaption></figure>



#### working set algorithm

* process 들의 working set size 의 합이 page frame 보다 큰 경우,
  * 일부 프로세스들을 swap out 시키고 남은 process 의 working set을 우선적으로 충족시켜 준다. → MPD 를 줄인다.
* working set 을 다 할당하고도 page frame 이 남는 경우라면
  * swap out 되었던 프로세스에게 working set 을 할당한다. MPD 를 키운다.
* window size A
  * working set 을 제대로 탐지하기 위해서는 window size 를 잘 결정해야한다.
  * A값이 너무 작으면 locality set 을 모두 수용하지 못할 우려가 있고
  * 너무 크면 여러 규모의 locality set 을 수용하게 된다.
  * A 값이 무한대면, 전체 프로그램을 구성하는 page 를 working set 으로 간주하게 된다.

### PFF, Page Fault Frequency scheme

<figure><img src="../../.gitbook/assets/image (7) (4).png" alt=""><figcaption></figcaption></figure>



* page-fault rate 의 상한값과 하한값을 둔다.
  * page fault rate 이 상한값을 넘으면 frame 을 더 할당한다.
  * page fault rate 이 하한값 이하이면 할당 frame 수를 줄인다.
* 빈 frame 이 없으면 일부 프로세스를 swap out 한다.

### Page Size 의 결정

* 페이지 시스템에서는 동일한 크기로 virtual, physical memory 를 자른다.
* page size 를 감소시키면
  * 페이지의 수가 증가할 것이고
  * 페이지 테이블의 크기 역시 증가할 것이다.
  * 하지만 내부에 남는 빈 공간인 internal fragmentation 는 감소할 것이며
  * disk transfer 의 효율성 역시 감소할 것이다.
    * seek/rotation vs. transfer
  * 필요한 정보만 메모리에 올라와 메모리 이용이 효율적이게 될 것이다.
    * locality 활용 측면에서는 좋지 않다.
* trend
  * larger page size



