# 특정 값을  ORDER BY 특정 값 우선 정렬 하기 (ORDER BY FIELD)

{% embed url="https://jabstorage.tistory.com/30" %}

* 원리는 FIELD 함수를 사용하면 첫번째 파라미터의 값과 같은 값이 그 이후 파라미터의 몇번째에 있는지 index값을 반환한다.
* 이후 파라미터의 첫번째에 있는 'ETH'는 1이 반환되고, 두번째에 있는 'BTC'는 2가 반환이 된다. 같은 값이 없는 나머지는 0이 반환된다.
* 이것을 DESC정렬하여 우선적으로 정렬되게 한다.

## 개를 고양이보다 먼저 나오도록 순서를 정렬할 때,&#x20;

```sql

SELECT  
   ANIMAL_TYPE, count(*) 
   from ANIMAL_INS 
   group by ANIMAL_TYPE
   order by field(ANIMAL_TYPE, 'Dog') desc;   
-- Dog 는 1, 지정하지 않은 나머지는 0 값을 부여받게 되고 desc 순으로 정렬하니, 값이 있는 Dog 부터 정렬된다. 
```

