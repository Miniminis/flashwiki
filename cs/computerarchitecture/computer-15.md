---
title: 입출력 구조 - 입출력(I/O) 연결과 주소 지정
category: computer-architecture
date: 2021-12-17 08:07:02
---

> [패스트캠퍼스에서 컴퓨터 구조 강의](https://fastcampus.co.kr/dev_online_computer) 를 듣고 스스로 공부한 내용을 정리하고 있습니다.

## 입출력 주소 지정 
컴퓨터 시스템에는 다양한 입출력 장치와 통신포트가 연결된다. 이를 서로 구분하기 위해서 각 입출력장치 또는 통신포트에 고유한 주소를 할당한다. 

하나의 입출력 장치에 그 상태나 데이터의 입출력등 용도에 따라 다수의 주소 할당이 가능하다. 
- 일반 저장공간에 할당된 주소에 비해서 입출력 장치의 개수는 작기 때문! 

### 메모리 맵 입출력
- 메모리에 부여된 주소공간의 일부를 입출력 주소공간으로 활용하는 방법이다. 
- 기존 시스템과 동일한 주소선과 제어선을 사용하여 입출력 관리하는 것이 가능하다. 
- 시스템은 지정된 주소 값에 의해서 데이터 저장 공간과 I/O 장치를 구분하게 되고 
- 모든 CPU 에 대해서 구현이 가능하지만, 하드웨어적 주소값에 따라 접근 구현이 필요하다.

### 입출력 맵 입출력
- 메모리 주소공간과 입출력 주소공간을 따로 분리
- 메모리와 입출력 주소를 구분하는 제어선을 사용한다. 
- 상호 구분되는 명령어가 필요하다. 
- 제어신호를 통해서 메모리 / 입출력 장치에 접근이 가능하도록 하드웨어적 구현이 필요하다. 

### 비교 
- 메모리 맵 입출력은 모든 CPU 에서 구현이 가능하지만 입출력 맵 입출력은 I/O 용 별도의 명령어와 이를 외부 제어선이 있는 CPU 에서만 구현이 가능하다. 
  - PC 에서는 양자 모두 구현이 가능하다고 알려져 있다. 
- 결국 차이점은 메모리 주소공간과 입출력 주소공간을 같이 쓰느냐 vs. 따로 쓰느냐 에 따라서 둘이 구분된다.

## 입출력 버스 할당 

### 중앙 제어 버스 vs. 분산 제어 버스 
![img_4.png](/.gitbook/assets/computer-1501.png)
- 중앙 제어 버스 
  - 구현 및 관리가 용이
  - 중앙에서 관리하기 때문에 제어기에 이상이 발생할 경우, 버스 중재 및 활용에 결함이 발생할 수 있다. 
- 분산 제어 버스 
  - 버스 상의 모든 장치가 제어 기능을 나누어서 실행한다. 
  - I/O 버스는 주로 중앙제어버스 시스템을 적용하며 
  - 시스템 버스로는 분산 제어버스 시스템을 선호한다. 

### 동적 중재 알고리즘 
- 정적 우선순위 알고리즘에 대해서 시스템이 동작하고 있는 동안 장치들의 우선순위를 변경 시킬 필요가 있을 경우 적용 가능한 알고리즘을 `동적우선순위 알고리즘`이라고 한다. 
  - 작업의 우선순위가 높은 것이 등장하는 등 
- 시간분할 알고리즘
  - 연속적으로 주어지는 버스 시간을 고정된 길이의 시간분할로 나누고 이를 라운드 로빈 방식으로 각 프로세스에 할당 
  - 버스에서의 위치에 관계없이 모든 요소들이 동등한 서비스를 받게 된다. 
- 폴링 버스 시스템 
  - 폴 라인으로 버스 승인 신호를 대체하여 모든 장치에 연결 
  - 버스 제어기는 이신호를 이용하여 각 장치의 주소를 결정한다. 
  - 지정된 순서에 따라서 버스를 할당받는다. 
  - 프로그램으로 우선순위를 변경하는 것이 가능하다. 
- LRU : Least Recently Used
  - 가장 오랫동안 버스 서비스를 사용하지 못한 장치에게 우선권을 주여한다. 
  - 몇 번의 버스 사이클마다 LRU 알고리즘에 의해 각 장치의 우선순위가 계속 변경된다. 
  - 모든 프로세스가 버스에 접근할 수 있는 기회를 동등하게 부여받을 수 있다. 
- First come First served
  - 버스 제어기는 각 프로세스의 버스 요구 도착시간별 저장이 가능한 규를 이용해서 도착한 순서대로 서비스를 제공한다.
- 회전 데이티 체인 방식
  - 모든 장치를 연결하여 차례로 버스 서비스를 제공하는 방식

### 입출력 수행의 분류 
### 동기 버스에 의한 수행 
![img_5.png](/.gitbook/assets/computer-1502.png)

### 비동기 버스에 의한 수행 
![img_6.png](/.gitbook/assets/computer-1503.png)
