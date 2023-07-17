---
description: SQL Server 기준
---

# 자료형

## 숫자형&#x20;

* 숫자형은 정수, 실수 등의 숫자 데이터를 말한다.&#x20;
* 암시적 형변환은 직접 자료형을 변경하지 않아도 실행 환경에서 자동으로 자료형을 변경하는 것을 의미&#x20;
* 명시적 형변환은 우선순위가 정의된 형식에 따라서 수행된다. 우선순위가 낮은 자료형은 우선순위가 높은 자료형으로 변형될 수 있다.&#x20;
* money : 소수점 두자리까지 표현된다.&#x20;
* 고정 소수점 형태는 자릿수가 고정되는데 전체 자릿수에 따라 저장소 크기가 결정된다.&#x20;

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

### 데이터 형변환시 우선순위&#x20;

* datetime > float, decimal > money > bigint, int, smallint, tinyint > nvarchar, nchar, varchar, char&#x20;
* 날짜 > 소수 > 정수 > 문자 순서로 우선순위를 갖는다.&#x20;
* 암시적, 명시적 형변환 여부를 확인하려면 실행계획을 먼저 확인하면 된다.&#x20;

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## 문자형

* 다양한 문자를 저장할 수 있는 자료형&#x20;
* 고정길이와 가변길이&#x20;
  * 고정길이 : 값을 입력하지 않아도 지정한 만큼의 저장 공간을 사용한다.&#x20;
  * 가변길이 : 실제 입력한 크기 만큼만 저장공간을 사용한다.  &#x20;
* 유니코드는 한글이나 특수문자 등을 저장할 때 사용하며 한 글자에 2바이트이다.&#x20;
* 영문은 알파벳 하나당 1바이트다.&#x20;

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

### 유니코드&#x20;

* 국가마다 고유한 언어와 문자가 있듯, 문자 형식의 데이터를 저장하고 관리할 때 국가별 코드 페이지가 달라서 서로 호환되지 않는 문제가 있다.&#x20;
* 이러한 문제를 해결하고자 유니코드 형식을 사용하는데 전 세계에서 대부분의 문자에서 단일 인코딩하는 방법을 정의한다.&#x20;
* 유니코드로 저장하려면 입력도 유니코드, 저장 자료형도 유니코드여야한다.&#x20;

### 문자 형변환&#x20;

* 문자와 날짜 형식도 형 변환이 가능하다.&#x20;

<figure><img src="../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

## 날짜&#x20;

* 날짜, 시간만 저장하는 타입이다.&#x20;
* 정확도에 따라서 데이터의 크기와 형식이 다르다.&#x20;

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

## 공간 데이터&#x20;

* 기하학적 개체의 물리적 위치나 모양 정보를 나타낸다.&#x20;
* 기하 도형 데이터 형식과 지리 데이터 형식을 제공한다.&#x20;
  * Geometry : 유클리드(평면) 좌표계의 데이터&#x20;
  * Geography : 둥근 표면 좌표계의 데이터&#x20;
* 어디서 사용되나
  * 특정 공간 개체에 가장 가까운 공간 개체를 찾는데 사용된다. 상점 물류 위치 등&#x20;

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>
