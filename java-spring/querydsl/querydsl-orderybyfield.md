---
title: querydsl 에서 mysql order by field() 사용하기
date: 2022-01-06 05:32:08
category: querydsl
---

현재 운영중인 우리 서비스에서는 이사하고 싶은 지역구를 최대 5개 선택하는 기능이 있다. 해당 내용들은 신청정보와 같이 전달되어 중개사분들이 방을 찾는데 중요한 정보 중 하나가 된다. 그런데 기존에는 지역구를 선택하면 선택 순서와 상관 없이 지역구 코드 순서대로 정렬이 되었던 것을 사용자가 선택한 순서대로 정렬되어 보이도록 수정해달라는 요청이 들어왔다. 

우선 확인해야할 포인트는 다음 두 가지 인 것 같았다. 
1. 특정 컬럼 기준으로 단순히 오름차순, 내림차순 정렬을 하는 것이 아니라, 사용자가 입력한 데이터로 정렬이 가능한가
2. 해당 쿼리를 querydsl 상에서도 구현 가능한가


## 1. mysql in() 절 내부의 리스트 순서대로 정렬하기
해답을 찾아 구글링을 하던 중, [MySQL IN 절 안에 있는 순서대로 정렬하기](https://steady-snail.tistory.com/100) 포스팅을 발견하였고, 다음과 같은 형태로 쿼리를 구성하여 사용자가 입력한 정보 순서대로 정렬이 가능하다는 것을 알게 되었다. 

```
select * from area 
    where area_code in (1112, 1114, 1116, 1113, 1115) 
    order by field(area_code, 1112, 1114, 1116, 1113, 1115);
```

이제 문제는 해당 쿼리를 querydsl 에서도 쓸 수 있느냐 이다. 

## 2. querydsl 에서 order by field() 함수 사용하기

이래저래 고민을 하던 중, 지난 번 mysql 함수를 쓰기 위해서 사용했었던 string template expression 을 사용하면 될 것 같았다. 그 결과, 다음과 같이 코드를 짤 수 있었다. 

```java
public List<Response> findAreaNamesByAreaCodes(List<String> areaCodeList) {
    return jpaQueryFactory.from(area)
            .where(area.areaCode.in(areaCodeList))
            .select(Projections.constructor(Response.class,
                    area.sigungu,
                    area.dong
                    ))
            .orderBy(orderByFieldList(areaCodeList))
            .fetch();
}

private OrderSpecifier<?> orderByFieldList(List<String> areaCodeList) {
    return Expressions.stringTemplate("FIELD({0}, {1})", area.areaCode, areaCodeList)
            .asc();
}
```

### 실행된 쿼리
```sql
where
    area0_.area_code in (
        ? , ? , ? , ? , ?
    )
order by
    FIELD(area0_.area_code,
    ?,
    ?,
    ?,
    ?,
    ?) asc
```

## 느낀점
- `Expressions.stringTemplate()` 를 통해서 웬만한 mysql 함수는 다 사용할 수 있을 것 같다.
- mysql 을 좀 더 깊게 처음부터 찬찬히 살펴보는 공부가 필요할 것 같다. 이런 숨겨진 수많은 기능들, 함수들을 놓치고 고생하고 있는 것 같다. 