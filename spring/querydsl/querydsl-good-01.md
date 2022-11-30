---
title: querydsl 을 쓰는 이유
date: 2022-01-06 06:53:17
category: querydsl
---

querydsl 을 처음 쓸 때에는 query 도 짜고 이 짠 내용을 다시 querydsl 문법에 맞게 변경해주는 작업이 필요했기에 왠지 일을 두 번 하는 느낌이었다. 그런데 이번에 업데이트를 진행하면서 querydsl 의 진가를 확인할 수 있었다.

현재 모든 쿼리를 querydsl 을 통해서 짜려고 노력중이지만, 개발 당시에는 도저히 생각나지 않아서 네이티브 쿼리로 짜둔 것이 하나 있었다. 당시에는 짜놓고 다른 일정들에 치여서 이 부분에 대한 개선을 미처 놓치고 있었는데, 역시나 문제는 그곳에서 발생하였다. 

```java
public interface TourRepository extends JpaRepository<Tour, Long> {

    @Query(value = " ... ", nativeQuery = true)
    List<ResponseDto> findUsersByTourCancelDate(String limitDateStr, Long tourStatus);
    
}
```

네이티브 쿼리가 포함된 곳에서 한 컬럼이 삭제되었는데, querydsl 로 코드를 짰을 때는 컴파일 할때부터 에러를 뿜으니, 바로바로 문제를 인지하고 수정할 수 있었는데, 네이티브 쿼리로 짜두니 이를 런타임에 가서야 발견하게 된 것이다.

이번 일을 계기로 querydsl 을 더 사랑하게 될 것 같다. 🙏
