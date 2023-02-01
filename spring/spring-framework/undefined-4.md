# 예외

## 예외처리의 안좋은 예

1. 예외를 try \~ catch 로 잡고 아무것도 조치하지 않는 행위 → 예외 블랙홀
2. 구체적인 예외를 특정하지 않고 무조건 Exception 으로 날려버리는 행위 → 무책임한 throws

## 예외 종류와 특징

1. Error
   1. 주로 자바 VM 에서 발생시키는 에러로써, 어플리케이션 코드 단에서 뭔가 처리할 수 있는 것이 없다. 따라서 이 에러에 대해서는 크게 신경쓰지 않아도 된다.
2. Exception 과 체크예외
   1. Exception 과 그를 상속한 서브 클래스들은 개발자의 실수에 의해서 발생하는 에러이다.
   2. Exception
      1. 언체크 예외 : RuntimeException
         1. 명시적으로 예외처리를 강제하지 않는다.
      2. 체크 예외 : ..Exception
         1. 반드시 처리하고 넘어가야 한다.
         2. 초기 java 와 JDK 설계자들은 체크 예외를 발생 가능한 모든 경우에 적용하고자 했음
         3. 이렇게 예외처리를 하는 것을 강제하자, 예외를 제대로 처리하지 않는 try-catch 문이 늘어나거나 무분별하게 throws 던지는 코드들이 많아졌다. 그래서 후에 개발자들로부터 많은 비난을 받았다고 한다.

## 예외 처리 방법

### 1. 예외 복구

* 예외상황을 파악하고 정상적인 상태로 돌려놓는 것
* 만약에 재시도해보았을 때, 예외가 해결될 가능성이 있다면 최대 시도 횟수를 두고 다시 시행되도록 처리하는 방법도 있다.

### 2. 예외 처리 회피

* throws 문으로 예외를 던지거나 try-catch 문으로 잡은 뒤, 다시 에러를 던지는 등의 처리
* 콜백/템플릿처럼 긴밀하게 연관이 있는 경우는 중구난방으로 에러처리를 하는 것이 아니라 ”, 모든 에러처리는 템플릿에서 한다”와 같이 규칙을 정할 수 있다. (대부분의 경우 이렇게 한다.)
* 예외 회피에는 명확한 이유가 있어야 한다. 서로 긴밀하게 연관이 되어 있어서 다른 오브젝트에게 예외처리 책임을 분명하게 지게 하거나, 내가 있는 쪽에서 예외를 다루는게 최선의 방법이라는 확신이 있어야 함

### 3. 예외 전환

예외를 회피하여 매소드 밖으로 던진다는 점에서는 예외처리 회피와 유사하다. 하지만 예외 전환의 경우는 적절한 예외로 바꾸어서 내보낸다는 차이점이 있다. 예외전환은 다음과 같은 경우 사용할 수 있다.

1. 로우 레벨의 예외를 좀 더 의미있고 추상화된 예외로 바꿔주기 위해서
   1. SQLException → DuplicatedUserIdException
2. 체크 예외를 언체크 예외(런타임 예외)로 포장해서 굳이 필요하지 않은 catch/throws 를 줄여주기 위해서

보통 전환하는 예외는 원래 발생한 예외를 담아서 중첩 예외로 처리하는 편이 좋다.

```java
catch(Exception e) {
	...
	throw DuplicatedUserIdException().initCause(e);
}
```

## 예외처리 전략

초기 자바와 JDK 설계자들에 대해서 체크 예외와 같이 예외처리를 강제하도록 한 부분이 개발자들에 의해 많은 비난을 받자, 요즘에는 대부분 예외처리를 강제하지않는 RuntimeException 으로 신규 API 들을 개발하는 경우가 많다고 한다.

### 어플리케이션 예외

1. 리턴 값을 명확하게 코드화한다.
   1. ATM 출금처리하는 API 의 경우, 정상이면 잔액값을 반환하고, 비정상이면 0이나 -1 등을 반환하도록 코드화하는 것
   2. 단점
      1. 리턴값을 명확하게 코드화하고 제대로 관리하지 않으면 혼란스럽다. 표준값이 없다면, 개발자마다 취향대로 코드화를 할 것이기 때문이다.
      2. 결과값을 확인하는 조건문이 자주 등장할 수밖에 없다.
2. 예외 상황에서만 비즈니스적인 의미를 띈 예외를 던지도록 만든다.
   1. 정상 로직일 경우는 그냥 흐르게 두고, 예외 상황에 대해서만 catch 블록을 여러층위로 쌓아서 한번에 예외 케이스들을 확인할 수 있도록 한다.

### JdbcTemplate 내에서 SQLException 의 처리

* jdbcTempalte 내에서는 SQLException 이 모두 런타임 예외인 DataAccesssException 으로 포장되어 반환된다.
* 따라서 DAO 에서는 꼭 필요한 경우만 명시적으로 exception 을 던져서 처리하면 되고 그 이외에는 모두 무시할 수 있다.

## 예외 전환

* JDBC 는 자바 표준 JDK 에서도 가장 많이 사용되는 기능 중 하나이다. DB 를 이용해서 데이터를 저장하고 필요한 정보를 조회하는 것은 거의 모든 프로그램에서 필요하기 떄문
* JDBC 는 자바를 이용해 DB에 접근하는 방법을 추상화된 API 형태로 정의해놓고 각 DB 업체가 JDBC 표준을 따라 만들어진 드라이버를 제공하게 해준다.
  * 내부 구현은 데이터베이스마다 다르겠지만, Connection, Statement, ResultSet 등 표준 인터페이스를 통해 그 기능을 제공해주기 떄문에 JDBC API 사용법만 잘 익히면 데이터베이스 상관없이 잘 쓸 수 있다.

### 1. JDBC 의 한계

* 비표준 SQL
  * 데이터베이스마다 해당 데이터베이스만의 특별한 기능을 사용하기 위한 SQL 비표준 문법이 존재하기 떄문에 데이터베이스마다 쿼리가 달라진다.
  * 따라서 DB 변경에 영향이 없는 유연한 프로그램을 작성하기가 어렵다.
  * 결국에는 DAO 를 DB 별로 만들어 사용하거나 SQL 을 외부에서 독립시켜서 바꿔쓸 수 있게 하는 식으로 개발할 수밖에 없다.
* 호환성 없는 SQL Exception 의 DB 에러정보
  * 데이터베이스마다 발생할 수 있는 exception 의 종류도 다양하다. 따라서 JDBC API 에서는 이러한 에러들을 모두 SQL Exception 으로 뭉뚱그려서 반환해주고 있다.
  * SQL Exception 내부에 에러코드와 SQL 상태정보를 담아서 전해준다. 하지만 이 코드마저 DB 벤더사마다 다르기도 하고, 표준을 잘 지키지 않는 상태코드가 존재하기도 한다.
  * 결국 개발자는 이런 SQLException 에서 전해주는 오류 정보만을 믿고 예외처리를 할 수 없는 상황이다.

### 2. DB 에러코드 매핑을 통한 전환

* DB 가 바뀌더라도 DAO 를 수정하지 않고 사용할 수 있으려면 앞서 지적되었던 두 가지 문제가 해결되어야 한다.
  * DB 마다 특수한 SQL 비표준 문법
  * 호환성 없는 SQLException DB 에러정보 (코드, 상태정보 등)
* 두번째 문제에 대해서 스프링에서는 다음과 같이 해결하고 있다.
  * DB 업체별로 가지고 있는 개별적인 DB 전용 에러코드를 분류해서 스프링이 정의한 예외 클래스와 매핑해놓은 에러코드 매핑 정보 테이블을 만들어두고 이를 이용한다.
  * JdbcTemplate 은 SQLException → DataAccessException 으로 변환하면서 DB 의 에러코드를 DataAccessException 계층 구조의 클래스 중 하나로 매핑해준다.
    * JdbcTemplate 이 던지는 예외는 따라서 모두 DataAccessException 의 서브 클래스 타입이다.
  * 드라이버나 DB 메타정보를 참고해서 적절한 예외 클래스를 선택하기 떄문에 DB 가 달라져도 같은 종류의 에러라면 동일한 예외를 받을 수 있다.

### 3. DAO 인터페이스와 DataAccessException 계층구조

* 자바에는 다양한 데이터 액세스 표준 기술이 존재한다.
  * JDBC, JPA, JDO, TopLink, ORM, iBatis
* DAO 를 다른 코드와 분리해 놓은 이유는 데이터 엑세스 로직을 담은 코드를 성격이 다른 코드에서 분리하기 위함이다.
* DAO 인터페이스를 사용해서 구체적인 클래스 정보와 구현방법을 감추고, DI 를 통해 제공받도록 만드는 것이 바람직하다.
* 하지만 DAO 예외을 다룰 때, 매소드 선언에 나타나는 예외정보가 문제가 될 수 있다.
  * 각 기술마다 예외상황에서 던져지는 예외가 다르기 떄문에 메소드 선언이 달라질 수 있다. → 인터페이스로 정의하는데 어려움을 겪는다.
  * JDBC 의 경우만 체크 예외이므로, SQLException → 런타임 예외로 포장해주면 된다.

```java
//JDBC
public void add(User user) throws SQLException;

//JPA
public void add(User user) throws PersistentException;

//JDO
public void add(User user) throws JdoException;

//Hibernate
public void add(User user) throws HibernateException;
```

#### 하지만 여전히 데이터 엑세스 기술이 달라질 때마다 예외처리 방법이 달라져야 한다.

* 스프링은 이 문제를 해결하기 위해 자바의 다양한 데이터 엑세스 기술을 사용할 때 발생하는 예외들을 추상화해서 DataAccessException 계층 구조 안에 정리해두었다.
  * JPA, hibernate 등 ORM 기술에서만 발생하는 경우, 혹은 JDBC 에서만 발생하는 경우도 모두 추상화되어 포함되어있다.
* JPA, JDO, hibernate 등 ORM 기술에서만 발생하는 낙관적인 락킹 예외 역시 처리되어있다. (ObjectOptimisticLockingFailureException)

### 4. 기술에 독립적인 UserDao 만들기

#### UserDao 인터페이스 적용하기

```java
public interface UserDao {
    void add(User var1);

    User get(String var1);

    List<User> getAll();

    void deleteAll();

    int getCount();
}
```

#### XML 파일에서 bean Class 변경하기

* bean 의 id 는 그대로 두고 class 만 변경한다.
* 추후에 데이터베이스가 변경되거나 한다면, 클래스만 변경시켜주면 된다.

```xml
<bean id="userDao" class="springbook.user.dao.UserDaoJdbc">
   <property name="dataSource" ref="dataSource" />
</bean>

```

#### 테스트코드로 예외 테스트하기

* DB 에 따라서 특정할 수 있는 예외 클래스가 다르다. 모두 DataAccessException 하위에 있기는 하지만, 만약에 JPA 를 쓰고 있는 상황에서 JDBC 에서만 발생하는 DuplicatedKeyException 과 같은 클래스로 예외를 특정할 경우, 기대했던 결과대로 동작하지 않을 것이다.
* SQLException 을 직접 DuplicateKeyException 으로 전환하는 기능을 확인해보는 테스트 진행 (제일 하단)

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"/test-applicationContext.xml"})
public class UserDaoTest {
    @Autowired
    UserDao dao;   // 굳이 UserDaoJdbc 타입으로 변경하지 않는다. 인터페이스 타입으로 전달받는다. 

//		@Test(expected = DataAccessException.class)  // 상위 클래스 
		@Test(expected = DuplicateKeyException.class)  // 좀 더 정확한 예외 특정하기 
    public void duplciateKey() {
        this.dao.deleteAll();
        this.dao.add(this.user1);
        this.dao.add(this.user1);  //예외 발생 지점 
    }

	@Test
  public void sqlExceptionTranslate() {
	        this.dao.deleteAll();
	
	        try {
	            this.dao.add(this.user1);
	            this.dao.add(this.user1);
	        } catch (DuplicateKeyException var5) {
	            SQLException sqlEx = (SQLException)var5.getCause();
	            SQLExceptionTranslator set = new SQLErrorCodeSQLExceptionTranslator(this.dataSource);

	            DataAccessException transEx = set.translate((String)null, (String)null, sqlEx);
	            Assert.assertThat(transEx, CoreMatchers.is(DuplicateKeyException.class));
	        }
	
	    }

}
```

## 기억하기

* JDBC의 SQLException 은 대부분 복구할 수 없는 예외이다. 따라서 런타임 예외로 포장하도록 한다.
* 복구할 수 없는 예외는 최대한 빨리 런타임 예외로 전환하는 것이 바람직하다.
* 스프링은 DataAccessException 을 통해서 DB 에 독립적으로 적용 가능한 추상화된 런타임 예외 계층을 제공한다.
* DAO 를 데이터 엑세스 기술에서 독립시키려면, 인터페이스 도입과 런타임 예외 전환, 기술에 독립적인 추상화된 예외로의 전환이 필요하다.
