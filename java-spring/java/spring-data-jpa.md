# Spring Data JPA 는 어떻게 동작할까?

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}



## 1. 의문점&#x20;

* 스프링 데이터 JPA 에서 인터페이스 타입의 인스턴스는 누가 어떻게 만들어주는 것일까?&#x20;
* 우리는 @Repository annotation 이나 기타 다른 어노테이션이 없는 JPARepository 를 extend 하여 인터페이스 내부의 함수들을 유용하게 사용하고 있다.&#x20;

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    //이렇게 선언하기만 해도, UserRepository를 어플리케이션 내에서 사용할 수 있다. 
    //JpaRepository 인터페이스에 정의되어있는 메소드들 역시 모두 사용이 가능하다. 
}

@NoRepositoryBean
public interface JpaRepository<T, ID> extends ListCrudRepository<T, ID>, ListPagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> { 
    ... 
}
```



## 2. 원리는 Proxy&#x20;

1. Spring Data JPA 에서는 Spring AOP 를 사용한다.&#x20;
2. Spring AOP 는 ProxyFactorySupport 를 사용한다.&#x20;
3. ProxyFactorySupport 내부에서는 ProxyFactory 클래스를 통해 Proxy 객체를 만든다.&#x20;
4. 이렇게 만들어진 Proxy 객체가 빈으로 등록되어, @Autowired 된 Repository 들에게 주입된다.&#x20;

