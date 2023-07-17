# 문자열 자르기 SUBSTR(column, startIdx, length)

```sql
SELECT 
    SUBSTR(PRODUCT_CODE, 1, 2), count(*)
    from PRODUCT
    group by 1
    order by 1;
```

* PRODUCT\_CODE 컬럼에서 값의 첫번째 인덱스부터 2개의 글자만 가져오도록 한다.&#x20;
* 주의할점
  * SQL 에서는 Index 가 1부터 시작한다. 맨 처음에 0부터 시작하는 것으로 했더니 아무 결과값도 나오지 않았다;;&#x20;
