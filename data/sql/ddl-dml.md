# DDL, DML

## 개념&#x20;

* 데이터베이스는 데이터를 저장하는 최상위 단위이다.&#x20;
  * 구조화된 데이터의 특정 집합이 저장되는 테이블의 모음이다.&#x20;
* 데이터베이스 내에는 스키마라는 하나 이상의 개체소유권 그룹이 있다.&#x20;
* 스키마는 테이블, 뷰, 프로시저 등과 같은 데이터베이스 개체가 포함되어있다.&#x20;

## 테이블&#x20;

* 행 : 레코드, 튜플&#x20;
* 열 : 필드, 속성&#x20;
  * 각 테이블에서 컬럼은 1024 개까지만 생성이 가능하다.&#x20;

## 제약조건의 종류&#x20;

* NOT NULL : NULL 값은 입력할 수 없다.&#x20;
* UNIQUE : 다른 row 와 중복된 값을 입력할 수 없다.&#x20;
* PRIMARY KEY : NOT NULL + UNIQUE&#x20;
* FOREIGN KEY : 다른 테이블의 컬럼을 조회해서 무결성 검사한다.&#x20;
  * 다른 테이블에 있는 정보와 함께 조회할 때 키로 사용&#x20;
* CHECK : 조건으로 설정된 값만 허용한다.&#x20;
  * [https://www.w3schools.com/mysql/mysql\_check.asp](https://www.w3schools.com/mysql/mysql\_check.asp)

## 다른 데이블에 검색 결과 입력하기&#x20;

> 검색한 결과를 테이블로 저장하는 방법&#x20;

```sql
select * from nasdaq_company where ipo_year = 2021;
# 1. 이미 생성된 테이블에 넣는 방법
insert into nasdaq_company_ipo_2021 select * from nasdaq_company where ipo_year = 2020;	-- select 문에 맞는 데이터만 조회하여 기존 테이블에 데이터 삽입 

# 2. 새로운 테이블을 생성하여 넣는 방법
CREATE TABLE nasdaq_company_ipo_2021 AS SELECT * FROM nasdaq_company where ipo_year = 2021;	-- 2021 데이터만 조회하여 새로운 테이블 생성 
```

### 임시 저장하기&#x20;

* 지역 임시 테이블, local temporary table
  * 테이블 생성시 앞에 # 붙인다.&#x20;
  * 한 세션 내에서만 유효함. 세션 종료되면 자동으로 삭제됨&#x20;
* 전역 임시 테이블, global temporary table
  * 테이블 생성시 ## 붙여서 생성한다.&#x20;
  * 전체 데이터베이스에서 공유 가능&#x20;
  * 역시 세션 내에서만 유효. 종료되면 자동 삭제됨 &#x20;
* 데이터베이스의 별도 저장 공간에 저장된다. &#x20;

