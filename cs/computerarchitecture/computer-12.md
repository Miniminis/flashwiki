---
title: 메모리 구조 - 효율적인 메모리 관리 정책
category: computer-architecture
date: 2021-12-14 06:45:36
---

> [패스트캠퍼스에서 컴퓨터 구조 강의](https://storage.googleapis.com/static.fastcampus.co.kr/prod/uploads/202104/161058-24/[패스트캠퍼스]-교육과정소개서-올인원-패키지---컴퓨터-공학-전공-필수.pdf) 를 듣고 스스로 공부한 내용을 정리하고 있습니다.

## Associative Memory

일반적으로 메모리에 접근할 때에는 주소기반으로 접근을 하는데, associative memory 는 내용을 기반으로 접근하는 메모리 장치이다. 
- 메모리 장치는 결국 자료의 저장과 접근을 용이하게하기 위해 필요로 하는 장치인데
- CPU는 필요한 자료를 얻기 위해서 메모리장치에 탐색을 하게 될 수 밖에 없다. 
- 좀 더 효율적인 탐색이 필요하다는 요구에 의해 만들어진 저장형태가 바로 이 associative memory 이다. 
  - 다른 말로는 내용 지정 메모리 content assdressable memory, CAM 이라고도 한다. 
- 이 방식은 데이터의 내용으로 병렬탐색을 하기 적합하도록 구성되어있다. 
- 탐색은 전체 워드 또는 한 워드 내의 일부만을 가지고 실행될 수 있다. 
- 각 셀이 저장능력 뿐만 아니라 외부의 인자와 내용을 비교하기 위해서 논리회로를 가지고 있다. 
  - 그래서 RAM 보다 훨씬 비쌈
- **탐색시간이 짧아야하고 그것이 반드시 이루어져야하는 중요한 이슈일 경우에만 쓰는 고가의 메모리 장치이다.** 

![img_3.png](/.gitbook/assets/computer-1204.png)
![img_4.png](/.gitbook/assets/computer-1205.png)

- Key Register 의 역할 
  - 인자워드의 특정한 영역이나 키를 선택하기 위한 마스크를 제공한다.  
  - 마스크 비트를 두면 비교 로직을 훨씬 빠르게 처리할 수 있도록 해준다. 

## Cache Memory
- locality of reference : 참고의 국한성
- 프로그램이 수행되는 동안 일반적으로 메모리를 참조할 때에는 메모리 중 일부분에만 집중되는 경향이 있다. 
  - 프로그램 루프와 서브루틴의 빈번한 활용
  - 순차적 프로그램의 실행
  - 데이터 메모리 참조에서도 동일한 경향이 있음을 확인할 수 있다. 
    - 테이블 - 룩업 절차 
    - 공통 메모리와 배열 사용의 예 
- 속도는 빠르고 (거의 CPU 처리속도와 동일) 조그마한 메모리를 이용해서 프로그램을 수행시킬경우, 평균 메모리 접근시간 단축과 그에 따른 전체 프로그램 수행시간의 절약을 담보할 수 있다.

### 캐시 메모리의 동작과 성능
1. CPU 는 우선 캐시 메모리를 체크한다. 
   1. 워드가 cache 에서 발견되면 hit 읽어들이고 
   2. 아닐 경우 miss 주기억장치에 접근한다. 
2. 이 워드를 포함한 블록(1-16워드, 환경에 따라 다름)을 캐시로 전송한다. 

히트율
- 히트 수 / CPU 에 의한 메모리 참조 총 수 = 0.9 에 수렴한다. 거의 90%!!!!  
- 예를들면, 
  - 캐시 접근시간이 100ns 이고, 
  - 주기억장치 접근시간 : 1000ns
  - 히트율이 0.9 이면, 데이터 접근 시간은 200ns 이다. 

### 캐시 메모리의 매핑 프로세스

결국, 효율적인 메모리 관리 = 효과적으로 cache 를 구성한다.
현존하는 메모리 관리 방법 중에서 최고의 방법일 것이다. 

캐시 메모리 사용시 주의할 점은 다음의 두 가지가 있을 것이다. 
1. 히트율을 높여야 한다. 이게 낮아지면 시스템 성능이 뚝 낮아질 수 있다. 
2. 캐시 메모리를 효율적으로 관리한다. 
   1. 다음 사용을 위해서 어떻게 캐시를 재구성 하는가?
   2. 관리를 어떻게 하는가?
   3. 필요없어진 것을 삭제하고 필요해진 것을 상위로 올리는 정책 

이에 대한 고민의 결과, 효과적으로 캐시 메모리 매핑을 하는 방법들은 다음과 같은 것들이 있다.
1. associative mapping
   1. associative memory 이용해서 캐시를 만든다. 
2. direct mapping
   1. SRAM 내부에 캐시를 만든다. 
   2. 하나의 공간에 하나씩 저장 -> 문제점 : 히트율이 낮을 수 있다. 
3. set-associate mapping 
   1. 주어진 공간에 병렬로 여러개씩 저장한다. -> 병렬처리! 