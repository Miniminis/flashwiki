# SELECT

> 기존에 몰랐거나 다시 기억하고 싶은 내용만 정리&#x20;

## Microsoft 공식문서에서 확인하는 SELECT 문법&#x20;

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

## 주의&#x20;

1. 필요한 컬럼만 조회하여 불필요한 데이터를 조회하지 않도록 한다.&#x20;
2. 데이터베이스 제조사별로 대소문자 구분 여부가 상이하다. 미리 자신의 환경을 숙지하고 있자.&#x20;

> 링크드 서버
>
> * 서버와 서버를 연결해놓은 것&#x20;
> * 하나의 서버에만 접속해도 다른 서버에 있는 데이터도 조회가 가능하다.&#x20;
> * 보안에 취약할 수 있음&#x20;



## 사용하기&#x20;

> 기본적인 것 제외하고 주의할 것만 기록

```sql
select * from nasdaq_company where ipo_year <> 2021; 	-- 2021년이 아닌 데이터만 조회
select * from nasdaq_company where ipo_year != 2021;	-- 이렇게 써도 된다! 

```



## 연산자의 우선순위&#x20;

> 우선순위에 따라서 연산이 적용된다.&#x20;

<figure><img src="../../.gitbook/assets/image (14) (6).png" alt=""><figcaption></figcaption></figure>

### 괄호를 사용하여 우선순위를 부여한다.&#x20;

```sql
select * from nasdaq_company
	where (sector = 'Technology' or sector = 'Customer Service')
    and symbol in ('MSFT', 'AMD', 'AMZN');

# 아래도 같은 결과임 
select * from nasdaq_company
	where sector in ('Technology', 'Customer Service')
    and symbol in ('MSFT', 'AMD', 'AMZN');
```



## 논리 연산자 종류&#x20;

* AND : 둘다 true&#x20;
* OR : 하나만 true&#x20;
* IN : 특정 요건 내에 있는 것 &#x20;
* NOT IN : 특정 요건 내에 있는 것을 제외한 것&#x20;
* BETWEEN : 기간, 범위 사이에 있는 것&#x20;
* ALL : 모든 비교집합이 true&#x20;
* SOME : 비교 집합 중 일부가 true&#x20;
* ANY : 비교 집합 중 하나라도 true&#x20;
* EXISTS : 값이 존재할 경우 검색&#x20;
* LIKE : 패턴이 일치하면 true&#x20;



## NULL 조회하기&#x20;

* NULL 은 빈값도 아니고 공백도 아니다. 그냥 값 자체가 없는 경우이다.&#x20;
* NULL의 경우는 IS NULL or IS NOT NULL 등을 이용하여 조회한다.&#x20;

## % 와일드카드&#x20;

* LIKE '%\[value]%' : 앞뒤로 어떤 문자열이 와도 value 만 포함하면 검색됨&#x20;
  * %A&#x20;
  * A%
  * %A%
* \_ : 어떤 글자라도 한 글자만 허용한다. 글자의 길이를 고려하여 검색하고 싶을 때 이용한다.&#x20;
* \[text] : text 안에 적힌 글자만 허용&#x20;
* \[^text] : text 안에 적힌 글자를 제외하고 허용&#x20;

```sql
with cte(col1) as (
	select 'A%BC' UNION ALL 
    select 'A_BC' UNION ALL 
    select 'ABC'
)
select * from cte where col1 like '%#%%' escape '#';	-- escape 뒤에 지정한 문자열을 문자로 사용하기 때문에 지정하기 나름 
select * from cte where col1 like '%%%';	--  모든 케이스를 다 와일드카드 적용하여 검색됨. '%' 를 포함하는 데이터만 조회되지 않는다. 
```

### WITH, CTE

> Common Table Expression&#x20;
>
> * 없는 테이블을 임시로 만들어서 1회성으로 사용할 수 있다.&#x20;

```sql
with cte(col1) as (
    select 'A%BC' UNION ALL 
    select 'A_BC' UNION ALL 
    select 'ABC'
)
select * from cte;
```

## 정렬

```sql
select * from nasdaq_company order by ipo_year limit 10, 10;
select * from nasdaq_company order by ipo_year limit 10 offset 10;
```

## 그룹화&#x20;

* GROUP BY \~ : 뒤의 컬럼 순서에 따라서 우선순위&#x20;
* HAVING : 그룹화된 데이터에 대해서 조건 부여&#x20;
* ORDER BY 는 항상 모든 쿼리에서 마지막에 실행된다.&#x20;
* DISTINCT : 중복 제거. 하지만 집계는 불가.&#x20;
