---
title: spring boot 에서 특정 매소드만 transaction 처리하기 
date: 2022-01-06 17:35:53
category: spring
---

## 배경

보통 spring 에서 transaction 처리를 할 때, 1) aop 방식으로 한 클래스에서 일괄적으로 설정을 해주거나 2) `@Transactional` 어노테이션을 이용해서 각 매소드 혹은 클래스 범위로 transaction 처리를 한다. 

1번을 사용할 경우에는 굳이 transaction 이 필요없는 API 의 경우도 일괄적으로 적용되어버려서 우리 서비스의 경우, 2번을 사용하여 보통 post 매소드의 경우, controller 단위로 transaction 을 처리해왔었다. 그런데 이번에 관리자 사이트 작업을 진행하면서, 아래와 같은 요구사항이 있었다. 

1. 여러 건에 대해서 update 요청을 한다.  
2. 예를 들어 10 건의 업데이트 요청이 왔을 경우, 5번째에서 실패한다면 그 건만 transaction 을 통해 롤백처리한다. 
3. 나머지 9건의 경우에는 정상적으로 update 처리를 한다. 

흔히 은행권이나 주식 서비스의 경우 여러 건의 요청을 보내면 n건 성공, n건 실패 등으로 결과값이 표시되는데, 이와 같은 것을 의미하는 것이었다. 

이를 위해서 아래와 같은 처리가 필요하다고 판단했다.
1. 여러 건들 동시에 업데이트 시키는 `updateAll()` 말고, 반복을 통해서 `update()` 를 여러 번 실행시켜줘야한다. 
2. transaction 을 컨트롤러 단위가 아니라 데이터가 삽입되거나 변경되는 `insert`, `update`, `delete` 건들에 대해서만 적용한다. 

1번의 경우, 여러 건들을 가져와서 반복문을 통해 반복시켜 처리해주면 되었으므로 이는 문제가 없었다. 문제는 2번이었다.

## 문제내용
### 매소드에 적용된 `@Transactional` 동작 안함
스프링에 따르면 `@Transactional` 은 클래스 혹은 매소드 단위로 적용 가능하다고 알고 있었다. 그래서 단순하게 생각했을 때에는 아래처럼 처리하면 될 것 같았다. 
1. 하나의 트랜젝션으로 처리되어야 하고 데이터 삽입이나 변경이 발생하는 부분은 별도의 매서드로 분리한다. 
2. 기존에 controller 단에 적용되어있었던 `@Transactional` 어노테이션을 해당 매서드로 옮겨주어 매소드로 transaction 범위를 한정시킨다. 

그런데 이게 웬걸, 위와 같이 처리를 하니 매소드 단위로 transaction 처리가 동작하지 않았다.

## 원인/해결방법
### 한 클래스 내 @Transactional 이 설정되어 있지 않은 메서드에서 @Transactional 이 설정된 메서드를 호출할 경우
어떤 부분이 문제인지 몰라 열심히 구글링을 해봤는데, demonic_님이 써주신 [@Transactional 작동 안할때 확인해봐야 할 것](https://lemontia.tistory.com/878) 포스팅을 통해 문제를 발견할 수 있었다. 글에서 @Transactional 어노테이션이 동작하지 않는 여러가지 케이스를 설명해주셨는데, 나의 경우 2번 케이스에 해당한다는 것을 알 수 있었다. 나는 단순히 controller 단에 있는 어노테이션을 -> 매소드 단으로 옮기기만 했을 뿐, 이 매소드를 호출하는 쪽에서는 @Transactional 이 설정되어있지 않았던 것이다.

이를 해결하기 위해서는 두 가지 방법을 쓸 수 있었는데, 그는 다음과 같았다.
1. 트랜잭션을 호출하는 매서드에 @Transactional 어노테이션을 붙여준다.
2. class 혹은 bean 을 분리한다.

2번과 같이 처리하기 위해서 본래의 service 클래스에서 transaction 처리를 위한 service 를 별도로 분리했고 transaction 이 필요한 매소드들만 해당 클래스에서 관리하도록 처리하였다.

## 적용결과
> 정리하자면, transaction 이 필요한 부분은 별도의 class 로 분리하여 해당 클래스 내부에서 만든 매소드를 외부에서 호출하는 방식으로 처리한다.

### MainService
```java
@Slf4j
@Service
@RequiredArgsConstructor
public class MainService {

    private final TransactionalService transactionalService;
    
    public BaseResponseEntity<ResponseResDto> update(Long userId, ExampleReqDto exampleReqDto) {
            ... [중략] ...

                Response response = transactionalService.updateSomething(userId, exampleReqDto);

            ... [중략] ...
    }

}
```

### TransactionalService
```java
@Slf4j
@Service
@RequiredArgsConstructor
@Transactional          //transaction 이 필요한 부분은 별도로 class 로 분리하여 해당 클래스 내부에서 만든 매소드를 외부에서 호출하는 방식으로 처리 
public class TransactionalService {

    private final UserRepository userRepository;

    public Response updateSomething(Long userId, ExampleReqDto exampleReqDto) {
            ... [중략] ...

            return userRepository.updateSomething(userId, exampleReqDto);
    }
```

## 느낀점
- spring transaction 에 대해서 정확히 모르고 사용했다는 것을 깨달았다. 역시 공부, 공부, 공부...! 
- 비즈니스 요구사항이 한층 더 디벨롭 될 수록 이렇게 공부하고 몰랐던 부분을 알게되는 계기가 된다. 문제를 맞닥뜨릴때에는 골치아프지만, 이렇게 새로운 내용들을 공부하게 될수록 더 성장하는 느낌이라 꽤 나쁘지 않다. 


## 기타 참고하면 좋은 내용
### transaction 기본 설명
- https://velog.io/@ha0kim/Transaction

### update/delete 의 경우 transactional 없으면 오류 리턴
update/delete 등 데이터를 변경하는 쿼리의 경우, @Transactional 어노테이션이 없다면 오류를 리턴한다.
```text
org.springframework.dao.InvalidDataAccessApiUsageException: Executing an update/delete query; nested exception is javax.persistence.TransactionRequiredException: Executing an update/delete query
```
- [두드림의 잡동사니](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=ydkun2&logNo=221706359126)

### Transaction 제어방법
트랜잭션을 제어하는 여러가지 방법이 있다. 
- https://kapentaz.github.io/spring/Spring-Transaction-적용-범위-제어-방법/#
- https://interconnection.tistory.com/122
- https://rlawls1991.tistory.com/entry/spring-transaction-roll-back-처리
