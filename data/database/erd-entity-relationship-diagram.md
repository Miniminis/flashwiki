# ERD, entity relationship diagram

## 1. 개념&#x20;

* 데이터베이스를 구축할 때 가장 기초적인 뼈대 역할
* 릴레이션 간의 관계들을 정의한 것이다.&#x20;
* 서비스를 구축할 때, 가장 먼저 신경쓰는 부분.&#x20;
* 시스템의 요구사항을 기반으로 작성되며, ERD 를 기반으로 데이터베이스를 구축한다.&#x20;

## 2. ERD 살펴보기&#x20;

<figure><img src="../../.gitbook/assets/image (3) (10).png" alt=""><figcaption></figcaption></figure>

* 직원들은 1명 이상의 고객들을 담당한다.&#x20;
* 직원들은 1명 이상의 직원들을 담당한다.&#x20;
* 사무실은 1명이상의 고객들로 이루어진다.&#x20;
* 고객은 1개 이상의 결제를 진행한다. &#x20;
* 고객은 1개 이상의 주문을 진행한다.&#x20;
* 주문은 1개 이상의 주문 상세로 이루어진다.&#x20;
* 상품은 1개 이상의 주문상세를 갖는다.&#x20;
* 프로덕트 생산 라인은 1개 이상의 프로덕트를 갖는다.&#x20;

### 2-1. 복합키&#x20;

* orderdetails : 복합키&#x20;
  * 주문 + 상품코드를 결합하여 유일성과 최소성을 만족한다.&#x20;