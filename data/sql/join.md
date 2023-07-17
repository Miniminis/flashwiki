# JOIN

## JOIN&#x20;

* 두개 이상의 테이블이나 데이터베이스를 연결하여 데이터를 검색하는 방법을 말한다.&#x20;
* 검색하고 싶은 컬럼이 다른 테이블에 있을 경우 주로 사용한다.&#x20;
* 여러 테이블을 마치 하나의 테이블인 것처럼 활용할 수 있다.&#x20;

### JOIN 을 사용하는 이유&#x20;

* 데이터를 중복 저장하면 그만큼의 저장공간이 필요하다.&#x20;
* 중복 저장한 열의 데이터를 수정할 경우, 해당 열을 가진 다른 데이터를 찾아서 모두 수정해야한다.&#x20;
* 중복을 최소화하면서 다른 테이블과 조합으로 데이터 셋을 만들기 위해서 조인이 필요하다. &#x20;

### JOIN 의 종류&#x20;

1. inner join&#x20;
2. (left, right, full) outer join&#x20;
3. cross join&#x20;
4. self join&#x20;

## 1 . INNER JOIN

* 조인키에 해당하는 각 테이블의 열 값을 비교해 조건에 맞는 값을 검색한다. &#x20;
* A와 B의 교집합&#x20;

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

### ON vs. WHERE&#x20;

* 둘다 데이터를 필터링 한다는 점에서는 역할이 비슷해보임&#x20;
* ON : 조인할 때 조인 조건을 위해서 사용&#x20;
* WHERE : 조인을 완료한 상태에서 조거에 맞는 값을 필터링하기 위해 사용&#x20;
* ON 구문을 사용해서 WHERE 와 같은 효과를 낼 수는 있지만, 데이터 매칭 과정에서 오차가 발생할 수 있으므로 반드시 성격을 분리해서 사용해야한다.&#x20;

## 2. (left, right, full) OUTER JOIN&#x20;

* 조인하는 여러 테이블에서 한쪽에 데이터가 있고 한쪽에 데이터가 없는 경우, 데이터가 있는 쪽 테이블의 내용을 전부 출력할 때 사용한다.&#x20;
* 조건에 만족하지 않아도 해당 행을 전부 출력하고자 할 때 사용할 수 있다.&#x20;
* 3가지 종류가 있다. - LEFT, RIGHT, FULL&#x20;
* 기준 테이블을 지정해야한다. - LEFT, RIGHT&#x20;

### LEFT JOIN&#x20;

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

### RIGHT JOIN&#x20;

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

### FULL OUTER JOIN&#x20;

* 보통 많이 쓰이지 않는다.&#x20;
* 양쪽 테이블 간 데이터를 비교할 때, 한쪽에는 있고 한쪽에는 없는 데이터를 찾기 위해서 종종 사용할 수 있다.&#x20;

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

## 3. CROSS JOIN&#x20;

* 조인에 포함된 테이블의 카티션 곱을 산출한다.&#x20;
* 각 테이블의 모든 경우의 수를 조합한 데이터가 필요한 경우 사용한다.&#x20;
* 각각의 컬럼을 서로서로 모두 매치하기 때문에 from 구문에 조인 조건이 없다.&#x20;

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### 언제 사용되나

* 예를 들면 모든 회원들에게 쿠폰 2종류씩 뿌릴 때! 회원 테이블 X 임시 쿠폰 테이블을 크로스 조인한 결과를 실제 쿠폰 테이블에 insert 하는 방식으로 사용할 수 있다.&#x20;

## 4. SELF JOIN&#x20;

* 같은 테이블을 사용하는 특수한 조인이다.&#x20;
* 테이블의 행을 같은 테이블 안에 있는 다른 행과 조인한다.&#x20;
* 계층적인 구조를 테이블화 할 경우 사용한다.&#x20;
* 반드시 테이블에 alias 명을 사용해서 질의한다.&#x20;
* SELF JOIN 개념적인 적으로 별도의 JOIN 구문이 없다.&#x20;

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

## 5. JOIN 시 주의사항&#x20;

* outer join 사용시 기준 테이블 순서에 주의한다.&#x20;
* on 절과 where 절을 목적에 맞게 반드시 구분한다.&#x20;
* outer join 과정에서 행 수가 늘어날 수 있음을 인지한다.&#x20;
  * left outer join : 1:1 or N:1 은 문제 없지만&#x20;
  * 1:N 의 경우 기준 테이블의 데이터가 중복되기 때문에 결과 행수가 늘어날 수 있다. &#x20;
