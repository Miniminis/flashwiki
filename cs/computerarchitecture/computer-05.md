---
title: 중앙처리장치 - 기본 컴퓨터 프로그래밍
date: 2021-12-07 08:39:00
category: computer-architecture
---

> [패스트캠퍼스에서 컴퓨터 구조 강의](https://storage.googleapis.com/static.fastcampus.co.kr/prod/uploads/202104/161058-24/[패스트캠퍼스]-교육과정소개서-올인원-패키지---컴퓨터-공학-전공-필수.pdf) 를 듣고 스스로 공부한 내용을 정리하고 있습니다. 

## 프로그래밍 일반
컴퓨터는 하드웨어와 소프트웨어로 구성된다. 하드웨어를 잘 동작시킬 수 있도록 제어하고, 지시하는 모든 종류의 프로그램을 소프트웨어라고 하고, 소프트웨어는 또 크게 시스템 소프트웨어와 응용 소프트웨어로 나눌 수 있다.
컴퓨터
- 하드웨어
- 소프트웨어
  - 시스템 소프트웨어
  - 응용 소프트웨어

### 프로그램의 종류 
프로그램의 종류에는 다음과 같은 것들이 있다. 

소프트웨어
- 시스템 소프트웨어
  - 운영체제
    - Job 관리 프로그램
    - 스케줄 관리 프로그램
    - 네트워크 관리 프로그램
    - 입/출력 관리 프로그램
  - 언어번역 프로그램
    - 컴파일러
    - 에셈블러
    - 인터프리터 - SQL, Python
  - 유틸리티 프로그램
    - DBMS
    - VACCINE - 바이러스 진단 프로그램
    - 드라이버 관리 프로그램
- 응용 소프트웨어
  - 어플리케이션, 앱
    - 각종 사무용 프로그램 : 오피스, DRM, DLP
    - 통방 융합용 프로그램 : 멀티미디어용, 게임
    - 의사결정을 지원하는 각종 프로그램
  - 모바일용 어플

### 프로그램의 처리 과정
![img.png](/.gitbook/assets/computer-05-04.png)

우리가 모두 알고 있다시미 컴퓨터는 0과 1로 이루어진 언어를 사용하고 있다. 그런데 이렇게 0과 1로 이루어진 프로그램을 인간이 직접 컴퓨터의 언어로 작성하는 것은 너무나도 힘들고 버거운 작업일 것이다. 그래서 오늘날에는 다음과 같은 과정을 거쳐서 소프트웨어를 만들고 있다. 
1. 프로그래머는 영자숫자 형태의 기호인 일반 고급언어 High level language 로 프로그램을 작성한다. 내가 주로 사용하고 있는 java 도 이 하이레벨 언어의 일종이라고 할 수 있다.
2. 프로그래머가 high level language 로 작성한 프로그램을 컴퓨터가 이해할 수 있도록 이진프로그램으로 번역해주는 과정이 필요한데, 이것에 바로 컴파일이다. 그림에서 보이는 에셈블리 프로그램이 바로 그 역할을 해주고 있다.
3. 기계어로 번역된 프로그램은 컴퓨터에 의해 처리된다.

## 기계어

프로그램은 컴퓨터에게 데이터 처리 업무를 시행시키기 위한 명령어의 묶음이라고 볼 수 있다. 그 종류와 각각의 특징에는 다음과 같은 것들이 있다. 
1. 이진코드
   1. 메모리상에 나타나는 형태의 명령어로서, 이진수 명령어와 피연산자의 시퀀스이다. 
   2. 쉽게 말하자면 0과 1로 이루어진 기호의 모음
   3. 명령어와 연산이 되는 값들 모두 0과 1로 표현된다. 
2. 8/16진수
   1. 이진수 코드를 편의상 8진수 혹은 16진수 형태로 표현한 내용이다.
3. 기호코드
   1. 사용자가 연산 부분, 주소부분 등에 대해서 기호(문자, 숫자, 특수문자 등)을 사용하게 되며, 
   2. 각 기호 명령어는 하나의 이진 코드로 번역이 된다. 이때, 에셈블리어 assembly language 를 이용해서 에셈블러가 번역을 수행한다. 
4. 고급 프로그래밍 언어
   1. 컴퓨터 하드웨어의 동작을 고려하지 않고 문제해결(비즈니스로직)에만 관점을 맞춰서 개발한 인간 중심의 언어이다. 
   2. 내가 사용하고 있는 java 나, C, c# 등이 대표적이다.
   3. 이러한 부류의 언러를 컴파일러형 언어 compiler language 라고 한다. 

그림을 통해서 좀 더 자세히 이해해보자. 두 개의 숫자를 더하는 프로그램이 있다고 가정해보자.

![img_3.png](/.gitbook/assets/computer-05-01.png)
- 개발자는 인간의 언어와 닮은 고급 프로그래밍 언어를 사용하여 다음과 같이 프로그램을 작성할 것이다. 
- 개발언어를 딱히 모르더라도, a=83이고, b=-23이며 이 두 수를 더한 것이 바로 c가 되는 것을 알 수 있다. 

![img_2.png](/.gitbook/assets/computer-05-02.png)
- 위에서 개발자가 작성한 프로그램은 에셈블러를 통해서 고급프로그래밍 언어 -> 기호코드 에셈블리어로 번역이 된다.

![img_1.png](/.gitbook/assets/computer-05-03.png)
- 에셈블리어로 번역된 프로그램은 에셈블러를 통해서 컴퓨터가 읽을 수 있는 이진코드로 번역이 된다.
- 이 상태가 되서야 컴퓨터는 개발자가 작성한 프로그램을 이해하고 읽을 수 있으며 계산할 수 있는 상태가 된다. 


## 에셈블리 언어
에셈블리어 assembly language 는 일정한 규칙에 의해서 정의되므로 작성된 프로그램이 정확히 번역되기 위해서는 그 언어의 형식 규정을 잘 지켜야 한다. 모든 컴퓨터는 각자의 에셈블리 언어를 가지고 있고, 이는 컴퓨터 제조업체로부터 출판이 된다. 
- [위키백과 - 에셈블리어](https://ko.wikipedia.org/wiki/어셈블리어)

### 언어규칙
에셈블리어의 언어 규칙은 제조사마다 상이하지만 대표적으로는 다음과 같은 구성을 지닌다. 

- 라벨 필드
  - 기호주소를 나타내거나 빈칸이 될 수도 있다. 
  - 기호주소의 경우는 세 개 이하의 영자숫자들로 구성되며 첫자는 문자여야 한다. 
- 명령어 필드
  - 기계 명령어나 슈도 명령어를 기술
  - 메모리 참조 명령어인 MRI
  - 레지스터 참조 또는 입출력 명령어인 non-MRI
  - 슈도 명렁어
    - 예) `ORG 0` : 단순히 시작위치를 나타내는 명령어
- 코멘트 필드
  - 명령어에 대한 주석이나 해설을 하거나 불필요한 경우 생략도 가능하다.

![모토로라 MC6800 어셈블리어](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f3/Motorola_6800_Assembly_Language.png/800px-Motorola_6800_Assembly_Language.png)