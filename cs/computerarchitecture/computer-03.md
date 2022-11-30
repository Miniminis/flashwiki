---
title: 컴퓨터이해 - 컴퓨터 구성요소의 기능 및 이해
date: 2021-11-28 05:24:00
category: computer-architecture
---

> [패스트캠퍼스에서 컴퓨터 구조 강의](https://storage.googleapis.com/static.fastcampus.co.kr/prod/uploads/202104/161058-24/[패스트캠퍼스]-교육과정소개서-올인원-패키지---컴퓨터-공학-전공-필수.pdf) 를 듣고 스스로 공부한 내용을 정리하고 있습니다. 

## 인지와 기능의 조합
컴퓨터는 인지(중앙처리장치) 와 기능(주변장치)의 조합이다.

중앙처리장치
- CPU/MPU
- 사물인터넷 디바이스 H/W 플랫폼 종류

주변장치
- 기억장치
- 보조기억장치
- 입출력장치

### 중앙처리장치 (Central Processing Unit = CPU)
#### CPU
- 실행 프로그램의 명령을 해석하고, 실행한다.
- 마더보드
  - 데이터의 전달 통로가 디자인 되어있는 메인 보드
    ![img.png](/.gitbook/assets/cpu.png)<br>
    _출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_

- 아래와 같이 구성되어있음
  - 산술논리연산장치(ALU) + 제어장치(CU) 는 같은 칩 내에 구성되어있다.
    ![img.png](/.gitbook/assets/cpu-02.png)<br>
    _출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_

#### MPU (Micro Processor Unit)
- CPU를 고밀도 집적회로(LSI)화 한 일종의 통합장치이다.
  - 쉽게 말하자면 여러개의 CPU 묶음!
- 그 종류는 다양한데, 아래와 같은 세 가지를 이야기해보자. 
  - CISC : Complex Instruction Set Computer. 반복적인 명령어를 하드웨어로 처리한다.
  - RISC : Reduced Instruction Set Computer. CISC 와 반대되는 개념이다. 
  - Bit Slice MPU : CISC + RISC. 위의 두 종류를 섞어 놓은 개념. 

#### 사물인터넷 디바이스 H/W 플랫폼 종류
아두이노
- 대표적으로는 우리에게 이미 많이 친숙한 아두이노(arduino)가 있다.
- 아두이노는 2005년, 이탈리아에서 탄생했다.
- 대표적인 오픈소스 H/W 플랫폼
  - 기존의 전자기기에 CPU 를 결합시키는 개념!
  - 예를들면, 토스트기에 오늘의 날씨와 기온이 표시된다...!
- Atmel 사의 AVR 이나 Coretex-M3 를 탑재한 마이크로 컨트롤러 보드이다.
- ![아두이노](https://kocoafab.cc/data/141104041529.png)

기타
- 아두이노 이후로 라즈베이파이, [갈릴레오, 에디슨](http://www.noteforum.co.kr/news/index.htm?nm=33242) 등 다양한 플랫폼들이 등장했다. 
  - 라즈베이파이
    - 영국에서 초등생 저학년을 대상으로 코딩 교육을 원활하게 할 수 있게 만든 것으로
    - 코딩 결과가 즉시 반영되어서 코딩을 익히기에 아주 좋다. 
- ![라즈베이파이](https://www.devicemart.co.kr/data/goods/1/2020/05/12553062_tmp_04bcfbb41cc5d6e2eeec27fd431d04f09718large.png)


### 주변장치 (Peripheral Device)
#### 기억장치
- RAM 
  - Random Access Memory
  - 그 종류에는 DRAM(Dynamic RAM) 과 SRAM(Static RAM) 등 이 있다.
  - 컴퓨터 재부팅시에는 사라진다.
  - ![DRAM and SRAM](https://t1.daumcdn.net/cfile/blog/2747144B51E787F417)
- ROM
  - Read Only Memory
  - 외부에 있는 데이터, 프로그램을 불러들이기 위한 메모리
  - 계속 저장되며 읽기만 가능하다. 
- RAM vs ROM
  - ![RAM vs ROM](https://lh3.googleusercontent.com/proxy/o0Xou1vZ7_NlTrUWeSIZXderwMXqsdgRR2j9iF6sSyT-V3A2dhnpbB9jegXUvSXklD9rDkYp6hss0fXmin1qKKizJyNTZQo28Nw2vQNpu1KQKDtmd9BxMFYIjXIsf1Hm-10wQJom-C9aX4DaVjJZug)

#### 보조기억장치
![img.png](/.gitbook/assets/peripheraldevice.png)<br>
_출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_

하드디스크
- 속도는 느리지만
- 가격은 저렴하고 
- 대용량의 데이터를 저장할 수 있는 장점이 있다.

주 기억장치와 보조기억장치의 관계
1. 컴퓨터 전원이 부팅되면 CPU 는 자동으로 ROM 에 있는 프로그램을 실행한다.
2. 보조기억장치(하스디스크 등)에서 RAM 에 데이터가 적재되면
3. CPU 는 RAM 으로부터 실행할 명령어와 데이터를 가지고 와서 처리한다. 
4. RAM 에서 처리한 데이터는 보조기억장치 등에 저장된다. 

![img.png](/.gitbook/assets/ram-rom.png)<br>
_출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_


#### 입출력장치
- 키보드
- 마우스
- 스캐너
- 터치스크린, 조이스틱, 광학 마스크 판독기(ORM)
- 바코드 판독기 등이 있다. 