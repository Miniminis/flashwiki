---
title: 병렬 컴퓨터 구조와 성능 분석 - 시스템 성능 분석과 개선
category: computer-architecture
date: 2021-12-19 20:45:29
---

> [패스트캠퍼스에서 컴퓨터 구조 강의](https://fastcampus.co.kr/dev_online_computer) 를 듣고 스스로 공부한 내용을 정리하고 있습니다.

## 프로세서간 중재
컴퓨터 시스템에서는 각 요소들 사이에 정보를 주고 받기 위해서 다양한 단계의 버스가 존재한다. 
CPU 내에서도 레지스터와 ALU 사이의 정보 전송을 위해서 여러 내부 버스가 존재한다. 

### 시스템버스
- CPU, IOP(I/O Processor), Memory 와 같은 주요 요소를 연결하는 연결체계

### 로컬버스 
서브 요소들과 연결됨
- 메모리버스 : 데이터, 주소, 읽기/쓰기 정보를 전송한다. 
- IO 버스 : 입출력 장치로 정보를 전송한다. 
- 통신버스 : 특정 포트 정보, 해당 프로토콜 정보등을 송/수신

### 공유 메모리 멀티 프로세서 시스템에서
각 자원을 효율적으로 분배하고 상호 충돌이 발생하는 것을 예방할 수 있는 중재프로세스가 필요한데, 
바로 이 중재 프로세스가 전체 시스템 성능 향상에 지대한 영향을 미친다. 

### 메모리 공유 멀티 프로세서를 위한 시스템 버스 구조 
![img_7.png](/.gitbook/assets/computer-1801.png)
- CPU, IOP, Local memory 와 연결되어있는 것은 로컬 버스이고 
- 이 로컬 버스는 시스템 버스 컨트롤러를 통해서 시스템 버스와 연결된다. 
- 각각의 버스들은 공유메모리를 공유하며 데이터를 송수신한다. 

### 중재 알고리즘
일반적으로 시스템버스는 100여개의 신호라인으로 구성이 되어있다. 
이 명령라인에 의해서 중재 논리가 성립된다.

CPU 가 여러개 있는 멀티 프로세서 시스템의 경우에는 항상 아래와 같은 중재 알고리즘들을 고려하여 문제를 해결한다. 
- 직렬 중재 절차 : daisy-chain 
- 병렬 중재 논리 
- 동적 중재 논리 

## 프로세서간 통신과 동기화 
- 멀티 프로세서 시스템에 있는 다양한 프로세서들은 공통의 입출력 채널을 통해서 서로 통신을 한다. 
- 공통메모리의 역할 
  - 공유 메모리 멀티 프로세서 시스템에서 메모리의 일부를 모든 프로세서가 접근할 수 있도록 할당한 공간이다. 
  - 기본적으로 우편함처럼 프로세서들 사이에서 오가는 메시지의 집중지 역할을 하며 
  - 송신 프로세서가 요구사항이나, 메시지, 프로시저 등을 저장하고
  - 상태비트를 통해 수신 프로세스에서 지정하거나 의미있는 정보의 유무를 공지한다. 
  - 수신 프로세서가 이를 폴링 방식에 의해서 주기적으로 점검하고 받아들이는 것이다. 
  - 물론, 가장 효율적인 방법은 인터럽트 신호를 통하여 송신 프로세서에서 수신 프로세서로 직접 전달하는 것이다. 

### 멀티 프로세서를 위한 운영체제 종류 
1. 주종모드 
   1. master - slave 모드 
   2. 주 프로세서가 OS 기능을 수행하고 
   3. 종 프로세서는 필요할때마다 주에게 인터럽트 요청을 보낸다. 
2. 분리 운영체제 
   1. 모든 프로세서가 자신의 운영체제를 가지고 운영된다. 
   2. 느슨한 결합 loosely coupled 시스템에 적합한 형태!
3. 분산 운영체제 
   1. 부동 운영체제
   2. OS 의 특정 기능은 한 순간에 하나의 프로세서에서만 작동된다. 
   3. 즉, 운영체제가 여러 프로세서에 분산되어 있다. 

### 프로세서 간 동기화
- 멀티 프로세서의 명령어 집합에는 함께 동작하는 프로세서와의 통신과 동기화를 구현하는 기본 명령어가 포함된다. 
- 동기화는 왜 필요할까?
  - 처리될 작업들 간 정확한 순서가 유지되어야 하기 때문에 : C->B->A
  - 공용 기록 가능한 변수에 대해서 상호배제적 접근을 보장해야하기 때문에 
- 그래서 멀티 프로세서 시스템에서 자원들 동기화 하기 위해 가장 많이 활용하는 기업은 하드웨어적 접근 방법인 이진 세마호를 이용하는 방법이다. 
  - 이진 세마포?
    - 주어진 값을 변경하면 안된다는 세트값을 엣지값에 전달한다. 
    - 예를들면, 플래그처럼 1을 주면 바꿀 수 없게 블로킹이 되고 0을 주면 바꿀 수 있게 된다. 

## 캐시의 일관성 
![img_8.png](/.gitbook/assets/computer-1802.png)

- 공유메모리 멀티 프로세서 시스템에서도 각 프로세서는 로컬 메모리를 운영한다. 
- 하지만 이 경우에 여러개의 프로세서가 동일한 내용의 데이터를 각각 로컬 메모리에 가지고 있게 되는데, 이 내용들이 모두 동일하게 유지되어야 한다는 것이 바로 `캐시의 일관성 정책`이다. 
- 일반적으로는 메모리에 스기 동작을 할 때 갱신하는 정책 두 가지가 있다. 
  - write-through 정책
    - 쓰기 동작의 발생 시 cache 와 주 기억장치 모두를 갱신 
  - write-back 정책 
    - cache 만 갱신하고 다음에 메모리로 전달할 수 있도록 위치를 표시한다.

### 각 정책의 장단점 
하지만 위의 그림에서 보이는 것과 같이, 각각의 정책은 장단점을 가지고 있다. 
- write-through
  - 신규 데이터로 로컬 메모리를 갱신할 때, 주기억장치도 새로운 값으로 갱신한다. 
  - p1 과 주기억장치는 서로 같은 최신의 데이터를 가지고 있지만, 나머지 프로세스에는 공유되지 못해서 일관성이 깨지게 된다.  
- write-back 
  - 신규 데이터로 로컬 메모리만 갱신하고 그 위치만 기록해두기 때문에 
  - p1 을 제외하고 나머지 프로세스와 주기억장치는 모두 캐시 데이터 일관성을 유지할 수 있다.  

### 해결책
1. 각 프로세서에 개별적인 캐시를 허용하지 않고 주기억 장치에 공용 캐시 메모리를 둔다. 
   - 캐시의 가장 중요한 성질인, 근접성 원리에 위배될 수 있다. 
   - 평균 접근 시간도 증가하게 된다. 
2. 캐시 가능
   - 비 공유적이고 읽기 전용 데이터에 한해서만 캐시에 저장되도록 하는 방안이다. 
   - 하지만 근접성을 최우선으로 두는 캐시에 일부 기능을 제한하는 것도 완벽한 방법이 아닌 것 같다. 
3. 스누피 캐시 제어기 
   - 기본적으로 버스에 부착된 모든 캐시에 대해서 버스-감시 기능을 유지하도록 설계된 하드웨어장치 
   - 계속 캐시가 업데이트 되는 상황을 모니터링하는 컨트롤러가 존재하고 업데이트 되면 일관되게 모든 캐시를 업데이트 시킨다. 
   