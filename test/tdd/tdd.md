# TDD, 리팩토링이란?

## 1. 테스트 주도 개발, Test Driven Development&#x20;

* 프로덕션 코드는 프로그램 구현을 담당하는 부분으로 사용자가 실제로 사용하는 소스 코드를 의미한다.&#x20;
* 테스트 코드는 프로덕션 코드가 정상적으로 동작하는지 확인하는 코드이다.&#x20;

## 2. TDD?&#x20;

* Test First Development + Refctoring&#x20;
* 켄트백의 TDD by Example 에 따르면,
* TDD란 프로그래밍 의사결정과 피드백 사이의 간극을 의식하고, 이를 제어하는 기술이다.&#x20;
* TDD의 아이러니 중 하나는 테스트 기술이 아니라는 점이다. TDD 는 분석기술이며, 설계 기술이기도 하다.&#x20;

## 3. TDD를 하는 이유&#x20;

* 디버깅 시간을 줄여준다.&#x20;
* 동작하는 문서 역할을 한다.&#x20;
* 변화에 대한 두려움을 줄여준다.&#x20;

## 4. TDD 사이클&#x20;

1. 실패하는 테스트를 구현한다.&#x20;
2. 테스트가 성공하도록 프로덕션 코드를 구현한다.&#x20;
   1. 이 때 작성하는 코드는 최대한 빠르게 성공하는 코드를 짜는 것을 목표로 한다.&#x20;
3. 프로덕션 코드와 테스트 코드를 리팩토링한다.&#x20;

## 5. TDD 원칙&#x20;

* 원칙 1 : 실패하는 단위 테스트를 작성할 때까지 프로덕션 코드를 작성하지 않는다.&#x20;
* 원칙 2 : 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.&#x20;
* 원칙 3 : 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.&#x20;

