# Sub Query

* 서브쿼리는 쿼리 안에 포함된 또 다른 쿼리이다.&#x20;
* 조인하지 않은 상태에서 다른 테이블과 일치하는 행을 찾을 수 있다.&#x20;
* 메인 쿼리가 서브 쿼리를 포함하는 종속적 관계이다.&#x20;
* 여러번의 쿼리를 수행해야 얻을 수 있는 결과를 하나의 중첩된 문장으로 간편하게 조회할 수 있다.&#x20;

## 특징&#x20;

* 반드시 소괄호로 감싸서 사용한다.&#x20;
* 주 쿼리를 실행하기 전에 1번만 실행된다.&#x20;
* 비교 연산자에 서브 쿼리를 사용하는 경우, 서브 쿼리를 오른쪽에 기술해야한다.&#x20;
* 내부에는 정렬 구문인 order by 문을 사용할 수 없다.&#x20;
* 여러 개의 서브 쿼리 중첩이 가능하다.&#x20;

## 사용 가능한 곳&#x20;

* select&#x20;
* from&#x20;
* where&#x20;
* having&#x20;
* order by
* insert 문의 values&#x20;
* update 의 set 절 등&#x20;

