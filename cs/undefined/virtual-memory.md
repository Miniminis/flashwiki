# Virtual Memory

### Demand Paging

* 실제로 필요할 때 page 를 메모리에 올리는 것
* 이렇게 하면 뭐가 좋을까?
  * I/O 양이 감소하게 되고
  * 당장 필요없는 데이터는 메모리에 올리지 않으므로 메모리 사용량도 감소하게 된다.
  * 시스템 전체적으로 보았을 때, 퍼포먼스가 향상하므로 더 빠른 응답을 할 수 있고
  * 더 많은 사용자를 수용할수도 있다.
* valid/invalid bit 의 사용
  * invalid 의 의미
    * 사용되지 않는 주소 영역인 경우
    * 페이지가 물리적 메모리에 없는 경우
  * 처음에는 모든 page entry 가 invalid 로 초기화 된다.
  * address translation 시에 invalid bit 이 set 되어 있으면 page fault

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>



### page fault

* invalid page 에 접근하면 MMU 가 trap 을 발생시킨다. → page fault trap
* kernal mode 로 들어가서 page fault handler 가 invoke 된다.
* 다음과 같은 순서로 page fault 를 처리한다.
  1. invalid reference 인지 체크한다.
     1. 요청 주소가 잘못되거나 문제가 있다면 프로세스를 종료시킨다.
  2. empty frame 을 가져온다. 없다면 다른 프로세스로부터 뺏어온다. (replace)
  3. 해당 페이지를 disk 에서 memory 로 읽어온다.
     1. 이 과정 동안에 프로세스는 CPU 를 preepmt 당한다. → block
     2. disk read 가 끝나면 page tables entry 에 기록하고 valid/invalid bit 를 valid 로 바꾼다.
     3. ready queue 에 process 를 insert 한다. → dispatch later
  4. 이 프로세스가 CPU 를 잡고 다시 running 한다.
  5. 아까 중단되었던 instruction 을 재개한다.

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>



> 하지만 page fault 시에 디스크에서 값을 읽어오는 작업은 매우 시간이 오래걸리는 작업이다.

* page fault rate 0 ≤ p ≤ 1.0
  * if p = 0, no page faults
  * it p = 1, 모든 참조가 fault 가 발생했음을 의미한다.
  * 실제 컴퓨터에서 나는 page fault 비율을 조사해보니, 0.x 정도로 거의 1에 가까웠음을 알 수 있다.
* effective access time
  * page fault 가 안나는 경우 + page fault 가 나는 경우 = 총 걸린 시간
  * (1 - p) \* memory access
  *
    * p (OS & HW page fault overhead + \[swap page out if needed] + swap page in + OS & HW restart overhead)

### free frame 이 없는 경우, page replacement

* page replacement
  * 어떤 frame 을 빼앗아올지 결정해야한다.
  * 곧바로 사용되지 않을 page 를 쫓아내는 것이 좋다.
  * 동일한 페이지가 여러번 메모리에서 쫓겨났다가 나시 들어올 수도 있다.
* replacement algorithm
  * page-fault rate 을 최소화하는 것이 목표이다.
  * 알고리즘의 평가
    * 주어진 page reference string 에 대해 page fault 를 얼마나 내는지 조사를 한다.
    * reference string의 예시 : 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

<figure><img src="../../.gitbook/assets/image (5) (5).png" alt=""><figcaption></figcaption></figure>



### optimal algorithm

* replacement algorithm 중 가장 최적의 알고리즘
* MIN(OPT) 가장 먼 미래에 참조되는 page 를 replace 한다.
* 그렇다면 가장 먼 미래에 참조되는 page 는 어떻게 알 수 있는가?
  * 미래에 참조되는 페이지(page reference string)들을 미리 다 안다고 가정한다.
  * offline optimal algorithm
* 다른 알고리즘의 성능에 대한 upper bound 를 제공한다.
  * belady’s optimal algorithm, MIN, OPT 등으로 불린다.



<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

* 7번 케이스의 경우, 1-2-3-4 가 이미 메모리에 올라와있는 상태에서 5번을 넣어야 할 경우, 4번이 가장 미래에 참조되므로 4번을 5번으로 replace 한다.

> 결국, optimal algorithm 은 실제로는 사용할 수 없기 때문에 가장 최적의 알고리즘 기준점으로 두고, 다른 알고리즘을 평가할 때 사용될 수 있다.

> 그렇다면, 실제로 사용되는 알고리즘에는 어떤 것들이 있을까?

### FIFO, First In, First Out algorithm

* 먼저 들어온 것을 먼저 내쫓는다.
* 메모리 크기를 3→4 로 늘렸는데 오히려 page fault 가 더 늘어나 성능이 더 안좋아지는 경우가 발생할 수 있다.
  * FIFO anomaly (belady’s anomaly)
  * more frames ≠ less page faults

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>



### LRU, Least Recently Used algorithm

* 가장 오래 전체 참조된 것을 지운다.



<figure><img src="../../.gitbook/assets/image (8) (5).png" alt=""><figcaption></figcaption></figure>

* 7번째 케이스에서 가장 오래전에 참조된 3번을 5번으로 replace 한다.

> 💡 미래를 예측할 수 없다면 과거를 보면 된다

### LFU, Least Frequently Used algorithm

* 가장 참조횟수가 적은 페이지를 지운다.
* 최저 참조횟수인 page 가 여러개가 있는 경우는 그럼 어떻게 할까?
  * LFU 알고리즘 자체에서는 여러 page 중 임의로 선정한다.
  * 성능 향상을 위해 가장 오래전에 참조된 페이지를 지우게 구현할 수도 있다.
* 장점
  * LRU 처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 떄문에 page 의 인기도를 좀 더 정확히 반영할 수 있다.
* 단점
  * 참조 시점의 최근성을 반영하지 못한다.
  * LRU 보다 구현이 복잡할 수 있다.

### LRU와 LFU 알고리즘의 예제

* LRU 는 가장 오래전체 참조된 것을 지우기 때문데 1번 페이지를 삭제할 것이다.
* LFU 는 가장 참조 횟수가 적은 것을 replace 대상으로 삼기 때문에 4번 페이지를 삭제한다.

<figure><img src="../../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>



### LRU 와 LFU 의 구현

* LRU : linked list 형태로 구현된다.
  * O(1) 의 복잡도를 가진다. 매번 쫓아낼 대상이 명확하여 계산할 필요가 없다.
  * 신규 페이지는 아래로 붙이고 쫓아낼 때에는 제일 첫번째 페이지를 내보내면 된다.
* LFU : linked list 형태 → heap 으로 구현된다.
  * LFU - MFU 형태로 구현되는데, 실제로는 참조 횟수에 따라서 기타 페이지들이 밀리거나 앞당겨지는 등 위치이동이 있을 수 있다.
    * O(N)
  * HEAP : 최상단에 가장 참조횟수가 적은 것을 두고, 아래로 갈수록 참조횟수가 높은 페이지들이 위치한다.
    * 이진 트리 형태로 구현되기 때문에 하단의 자식 2개와 비교하여 참조횟수가 많으면 더 밑으로 내려가면 된다.
    * 최악의 경우, 최상단에서 최하단까지 이동할 수도 있는데, 그렇다고 하더라도 log n의 시간정도만 소요되므로 매우 효율적이라고 할 수 있다.
    * O(log N)



<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>



