# 메모리 관리

### 메모리 주소

메모리 주소는 아래와 같이 세 가지의 다른 종류가 있다.

1. symbolic address
2. logical address
3. physical address

#### symbolic address

* 이 주소는 프로그래머가 변수나 매소드를 정의하는 행위를 할 때, 그 변수와 매소드 자체를 의미한다.
* 프로그래머가 실제 코딩할 때에는 메모리주소를 숫자로 특정하며 코딩하지 않는다. 다만 변수나 매소드를 정의하고 그 변수나 매소드를 호출할 뿐이다.
* 이때, 이 변수나 매소드를 심볼링 주소라고 한다.

#### logical address

* 논리적 주소는 다른 말로 virtual address 라고도 불린다.
* 프로세스마다 독립적으로 가지는 주소 공간이며
* 각 프로세스마다 0번지에서 시작한다.
* **CPU가 바라보고 있는 주소가 바로 이 논리주소이다.**

#### physical address

* 물리적 주소는 말 그대로 메모리에 실제로 올라가는 위치를 의미한다.

#### 주소바인딩

* 결국 데이터가 메모리에 올라가는 과정을 살펴보면 다음과 같은 흐름을 지닐 것이다.
* symbolic address → (**컴파일**) → logical address → (**실행시작**) → physical address
  * 그렇다면, `logical address → physical address` 이 시점은 도대체 언제인가?

### 주소바인딩, address binding

주소 바인딩에서는 아래와 같이 세 가지 방법이 있다.

1. compile time binding
2. load time binding
3. runtime binding

<figure><img src="../../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

#### 1. compile time binding

* 컴파일 시점 바인딩의 경우, 이미 컴파일 시점에 물리적 메모리 주소도 같이 결정된다. 즉, logical address 가 곧 physical address 인 셈이다.
* 따라서 소스 코드 내 변경이 있거나 시작 위치가 변경될 경우에는 무조건 컴파일을 다시 해서 새로운 물리적 메모리주소를 받아야 한다.
* 이때, 컴파일러가 생성하는 코드는 컴파일부터 프로그램의 실행 이후에도 변하지 않으므로 절대코드 absolute code 라고 한다.

#### 2. load time binding

* 로드타임 바인딩의 경우, 프로그램이 실행되는 시작점에 메모리 주소가 결정된다.
* loader 의 책임 하에 물리적 메모리주소가 부여되는 것이다.
* 컴파일러가 재배치가능한 코드 relocatable code 를 생성한 경우 가능하다.

#### 3. runtime binding

* 프로그램이 실행이 된 이후에도 계속 메모리 주소를 바꿀 수 있다.
* 이는 CPU가 주소를 참조하기 전에 address mapping table 로 binding 상태를 점검하기 때문이다.
* 이 런타임 바인딩은 하드웨어적으로 MMU가 갖추어져야 가능하다.

### MMU, Memory Management Unit

* logical address → physical address 로 주소로 매핑해주는 하드웨어이다.
* MMU 내부에는 두 개의 register 가 존재한다.
  * relocation address(=base register) : 접근할 수 있는 물리적 메모리 주소의 최소값
  * limit address : 논리적 주소의 범위
* MMU 는 MMU scheme 에 따라서 동작한다. MMU scheme은 아래 그림처럼 동작한다.
  * MMU 내부에 relocaltion register 와 limit register 가 존재한다.
  * CPU가 logical address 346번지의 데이터를 요청하면,
  * MMU 내부의 `limit register` 는 먼저 요청된 logical address 가 virtual memory 범위 이내의 주소값인지를 점검한다. 만약에 메모리 범위를 벗어나는 주소값일 경우, trap 을 발생시켜 CPU 주도권을 운영체제에게 넘긴다. (그럼 운영체제는 trap이 왜 일어났는지 파헤치기 시작함….)
  * 만약 virtual memory 범위 이내의 주소값일 경우에는 physical memory에서 해당 logical address 가 차지하는 공간의 시작점인 14000을 `relocation register` 로 두고, 그 값을 더해준다.
  * 그렇게하면, logical address 346가 실제 메모리 공간에서 위치하는 주소값인 14346 을 얻게 된다.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

### 관련 용어 살펴보기

1. dynamic loading
2. overlays
3. swapping
4. dynamic linking

#### 1. dynamic loading, 동적 로딩

* 프로세스 전체를 메모리에 올려놓는 것이 아니라 해당 루틴이 불려질 때, 메모리에 로드하는 것을 의미한다.
* 프로그램은 대부분 거의 발생하지 않는 루틴들이 있다. (오류처리나 예외적 케이스 등)
* 오류나 예외적 상황처럼 자주 발생하지 않는 루틴들을 대비하여 모든 내용을 메모리에 올려두는 것은 명백한 낭비가 된다.
* 따라서 dynamic loading 을 이용하면 메모리 활용도가 높아지게 된다.
* 가끔씩 사용되는 많은 양의 코드의 경우에 유용하게 사용될 수 있다.
* 이 로딩 방법은 운영체제의 지원 없이 프로그램 자체적으로도 구현이 가능하고, OS 는 라이브러리를 통해 지원 가능하다.
* 참고로 loading 은 메모리로 올려놓는 것을 의미한다.

#### 2. overlays

* 메모리에 실제 필요한 정보만을 올려두는 것이다.
* 프로세스의 크기가 메모리보다 클 때 유용하게 사용될 수 있다.
* 운영체제의 지원 없이 사용자에 의해 구현된다.
* 주로 초창기 시스템에서 메모리 크기가 너무 작았을 때 많이 사용되던 방법으로 모든 과정을 프로그래머가 직접 구현해야한다고 해서 manual overlay 라고도 불렸다. 당연히! 프로그래밍은 매우 복잡했다.

#### 3. swapping

* 프로세스를 일시적으로 메모리에서 backing store 로 내쫗는 것이다.
* 여기서 backing store 는 disk 를 의미한다.
  * 많은 사용자들의 프로세스 이미지들을 담을만큼 충분히 크고 빠른 저장공간
* swap in/out
  * swap in : 메모리에서 backing store 로 프로세스를 내보내는 것
  * swap out : backing store 로부터 메모리로 프로세스를 불러들이는 것
  * 일반적으로는 중기 스케줄러 swapper 에 의해서 swap out 시킬 프로세스가 선정된다.
  * priority based CPU scheduling algorithm
    * priority 가 낮는 프로세스를 swap out 시키고
    * priority 가 높은 프로세스를 swap in 시킨다.
  * complie binding 이나 load time binding 에서는 원래의 메모리 주소로 swap in 해야하며, runtime binding 에서는 빈 곳 아무데나 로드하면 된다.
  * swap time 은 대부분 swap 되는 양에 비례한 transfer time 이다.

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

#### 4. dynamic linking

* linking : 프로그래머는 프로그램을 작성하고 컴파일 한 다음 link 하여 하나의 실행파일로 만든다. 이것을 linking 이라고 한다.
* 이때 static linking 은
  * 라이브러리가 프로그램의 실행파일 코드에 포함된다.
  * 따라서 실행파일의 크기가 커지게 되며
  * 같은 라이브러리를 각각의 프로세스가 메모리에 올리게 되므로 메모리의 낭비가 발생하게 된다.
* 반면, dynamic linking은
  * 이 linking 을 실행시간까지 미루는 기법이다.
  * 라이브러리가 실행파일에 포함되지 않고, 실행이 되는 시점에 연결link 된다.
  * 라이브러리를 호출하는 부분이 stub 이라는 작은 코드를 두고 라이브러리 루틴의 위치를 관리한다.
    * 라이브러리가 메모리에 존재하면 stub을 뒤져서 그 루틴의 주소로 바로 찾아가고,
    * 없으면 디스크에서 읽어온다.
  * 이 방법 역시 하드웨어적으로 뒷받침이 되어야 가능하다.
  * shared library 라고 불리기도 한다.
