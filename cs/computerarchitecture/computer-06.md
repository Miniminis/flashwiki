---
title: 중앙처리장치 - 프로그래밍 언어와 실행
date: 2021-12-07 10:18:22
category: computer-architecture
---

> [패스트캠퍼스에서 컴퓨터 구조 강의](https://storage.googleapis.com/static.fastcampus.co.kr/prod/uploads/202104/161058-24/[패스트캠퍼스]-교육과정소개서-올인원-패키지---컴퓨터-공학-전공-필수.pdf) 를 듣고 스스로 공부한 내용을 정리하고 있습니다. 

## 프로그래밍의 구현 architecture
프로그램이 만들어지는 과정을 보자.

1. 인간인 개발자가 고급 언어 프로그램 high level language 을 작성한다. -> 문제중심 
2. 개발자가 작성한 프로그램은 에셈블리어 프로그램 assembly language 으로 변환되고 -> 처리중심
3. 변환된 에셈블리어 프로그램은 다시 기계어 프로그램 machine language 으로 변환되어 컴퓨터에 의해 처리된다. -> 처리중심

### 소프트웨어 개발 패러다임
소프트웨어 개발 패러다임은 다음과 같이 변해왔다. 

1. 1960-1970 에 소프트웨어의 위기현상이 발생한다.
   1. 기존까지는 구조적 기법으로 프로그램을 작성했었다.
   2. 구조적기법에서는 데이터와 함수를 분리하여 작성했었고 이 함수들의 모음으로 프로그램을 작성했었다. 
   3. 하지만, 이 기법은 개발과 유지보수에 많은 비용이 든다는 문제점이 있었다. -> 소프트웨어의 위기!
2. 객체지향 기법
   1. 캡슐화 : 객체(Object) 에 데이터와 처리기능(매소드)를 단위화하고
   2. 다형성(polymorphism) : 각 기능의 상속(inheritance)으로 기능을 재사용 가능하게 한다.
   3. 대표적으로 C++, Java 와 같은 언어들이 있다.
3. 컴포넌트 기법
   1. 컴포넌트 기법에서는 외부 인터페이스를 통해서 동작을 구현한다.
   2. 시스템의 단순성을 높인다. 
   3. 집약성이 높은 프로그램 덩어리를 조합해서 더 큰 프로그램을 만든다. 
4. 분산객체 프레임워크
   1. 다른종류의 분산된 환경에서 여러 종류의 어플리케이션을 쉽게 통합하기 위한 통합방식이다.


## 컴퓨터 네트워크 환경

### Wireshark 를 이용해서 패킷 분석
- 인터넷 웹 브라우저인 explorer 가 운영체제의 한 축을 담당하게 된 순간부터 컴퓨터는 이미 통신기계로 분류되고 있다. 
- 통신기계로서 hexadecimal 표현은 더욱 더 그 진가를 발휘하고 있음! 

![img.png](/.gitbook/assets/computer-06-01.png)<br>
_출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_

## 고급언어로 작성된 소스 -> 에셈블리어 번역 과정 확인하기

### 참고 
- [Dev C++ - 고급언어 프로그래밍 툴](https://sourceforge.net/projects/orwelldevcpp/)
- [x64dbg - 고급언어로 작성된 소스를 에셈블리 언어로 확인 가능한 툴](https://sourceforge.net/projects/x64dbg/)
- [wireshark - 패킷 분석 툴](https://www.wireshark.org/download.html)

### 1. 고급 프로그래밍 언어로 두 수 더하는 프로그램 작성하기
![img.png](/.gitbook/assets/computer-06-02.png)<br>
_출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_

- C 언어로 두 수를 더하는 프로그램을 작성한다. 
- 프로그램을 실행하면 컴파일 되어 원본 코드의 실행파일이 생성된다.

### 2. 실행파일 내부 살펴보기
![img_1.png](/.gitbook/assets/computer-06-03.png)
_출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_

- [x64dbg](https://sourceforge.net/projects/x64dbg/) 프로그램을 설치 후, 실행파일을 연다. 
- 에셈블리어로 번역된 내용을 확인한다. 

### 3. wireshark 로 패킷 내부 살펴보기
![img.png](/.gitbook/assets/computer-06-01.png)<br>
_출처: 패스트 캠퍼스 컴퓨터 구조 강의 이승주 강사님 강의자료_

- NO : 패킷 수집 순번
- Time : 패킷 수집 시간
- Source : 패킷의 출발지
- Destination : 패킷의 도착지