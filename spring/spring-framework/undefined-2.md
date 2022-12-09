# 테스트

## 스프링이 주는 핵심 가치 2가지

1. 객체지향설계
2. 테스트

{% hint style="info" %}
테스트 없이는 스프링도 의미없다.&#x20;
{% endhint %}



## 테스트의 필요성

* 일반적으로 기능하나를 개발한다고 하면, DAO 를 테스트하기 위해서는 데이터를 표시해줄 화면, 컨트롤러, 서비스 단까지 실제 테스트하고자 하는 것보다 더 많은 양의 코드를 미리 작성해두어야 한다.&#x20;
  * 이 경우, 핵심 기능 자체만 테스트하기가 어렵다. 테스트의 과정이 길기 때문에 어디서에 에러가 났는지 디버깅하기가 쉽지 않기 떄문이다.&#x20;
* 작은 단위의 테스트가 필요하다. 단위테스트라고 한다. 테스트 코드 역시 한번에 하나의 관심사만 갖고 있을 수 있도록 해야한다.&#x20;
* 단위테스트를 하면 각 기능당 발생할 수 있는 버그는 웬만큼 잡고 어플리케이션이 출시되기 때문에 실제 QA 에서 보고되는 오류의 수가 월등히 줄어들 수 있다.&#x20;
* 자동수행되는 테스트코드가 바람직하다. 매 테스트 때마다 DB 에 조작을 가하거나, 추가적인 작업이 필요하다면, 테스트를 반복해서 하기도 어렵고 쉽게 넘어가버리게 된다.&#x20;

## UerDaoTest 개선하기

#### 현재 UserDaoTest 는 두 가지 문제점이 있다.&#x20;

1. 데이터의 입력 등은 자동화했지만, 여전히 테스트 결과는 화면에 출력되고, 사람의 눈으로 직접 확인해야한다.&#x20;
2. 테스트를 위해서 main() 매소드를 계속 직접 실행시켜줘야한다.&#x20;

{% hint style="info" %}
필요한 것

1. 테스트 결과 검증의 자동화
2. 테스트를 위한 데이터 베이스 상태 원복
{% endhint %}



## 개발자를 위한 테스팅 프레임워크, JUnit

### JUnit 테스트로 전환하기

* 테스트 지원 도구로써 자바에서 단위테스트 하기에 유리하다.&#x20;
* JUnit 테스트로의 전환
  * JUnit 테스트 프레임워크를 이용함으로써, IoC 가 발생하게 된다. 어플리케이션 실행을 위한 오브젝트를 생성, 실행하는 일은 모두 프레임워크에게도 제어권이 넘어온다. 따라서 main() 매소드는 더이상 필요하지 않다.&#x20;
* 테스트 메소드의 전환
  * JUnit 프레임워크가 요구하는 조건 2가지
    * 메소드는 public 으로 선언한다.&#x20;
    * 테스트를 위한 메소드에 @Test 어노테이션을 붙여준다.&#x20;
* 검증코드의 전환
  * if, else 로 구성된 결과 검증 코드를 assertThat() 스태틱 메소드를 이용하여 변경한다. assertThat() 은 첫번째 파라미터를 두번째 파라미터로 오는 매처라고 불리는 조건과 비교하여 일치하면 넘어가고, 아니면 테스트가 실패하도록 해준다.&#x20;
* JUnit 테스트 실행하기
  * 스프링 컨테이너와 마찬가지로 JUnit 프레임워크도 자바코드로 만들어진 프로그램이니까, 어디서든 한번은 실행을 시켜줘야한다.&#x20;
  * 아무 곳에서 JUnitCore 클래스의 main 매소드를 호출해주는 간단한 코드를 넣어준다.&#x20;



```java

public class UserDaoTest {
    public UserDaoTest() {
    }

    @Test
    public void andAndGet() throws SQLException {
        ApplicationContext context = new GenericXmlApplicationContext(new String[]{"applicationContext.xml"});
        UserDao dao = (UserDao)context.getBean("userDao", UserDao.class);
        
        User user = new User();
        user.setId("gyumee");
        user.setName("박성철");
        user.setPassword("springno1");
        
        dao.add(user);

        User user2 = dao.get(user.getId());

        Assert.assertThat(user2.getName(), CoreMatchers.is(user.getName()));
        Assert.assertThat(user2.getPassword(), CoreMatchers.is(user.getPassword()));
    }

    public static void main(String[] args) {
        JUnitCore.main(new String[]{"springbook.user.dao.UserDaoTest"});
    }
}
```



### 테스트 결과의 일관성 유지하기 - 데이터베이스 선처리

* 현재의 UserDaoTest 라면, 이전 테스트의 결과로 남아있는 DB 에 중복된 데이터가 있을 수 있다. 이를 방지하기 위해서는 UserDaoTest 의 addAndGet() 테스트가 끝나면 자동으로 추가된 사용자 정보를 삭제해서 테스트 이전의 상태로 데이터베이스를 만들어주는 것이 필요하다.&#x20;
* 이를 위해서는 기능을 추가해야한다.&#x20;
  * deleteAll() : 모든 사용자를 삭제하는 기능&#x20;
  * getCount() : 현재 데이터베이스에 존재하는 모든 사용자의 수를 카운트하는 기능&#x20;

```java

public class UserDao {
    private DataSource dataSource;

    public UserDao() {
    }

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public void add(User user) throws SQLException {
        ...
    }

    public User get(String id) throws SQLException {
        ...
    }

    public void deleteAll() throws SQLException {
        Connection c = this.dataSource.getConnection();
        PreparedStatement ps = c.prepareStatement("delete from users");
        ps.executeUpdate();
        ps.close();
        c.close();
    }

    public int getCount() throws SQLException {
        Connection c = this.dataSource.getConnection();
        PreparedStatement ps = c.prepareStatement("select count(*) from users");
        ResultSet rs = ps.executeQuery();
        rs.next();
        int count = rs.getInt(1);
        rs.close();
        ps.close();
        c.close();
        return count;
    }
}


public class UserDaoTest {
    public UserDaoTest() {
    }

    @Test
    public void andAndGet() throws SQLException {
        ApplicationContext context = new GenericXmlApplicationContext(new String[]{"applicationContext.xml"});
        UserDao dao = (UserDao)context.getBean("userDao", UserDao.class);
        
        dao.deleteAll();

        Assert.assertThat(dao.getCount(), CoreMatchers.is(0));

        User user = new User();
        user.setId("gyumee");
        user.setName("박성철");
        user.setPassword("springno1");
        dao.add(user);

        Assert.assertThat(dao.getCount(), CoreMatchers.is(1));

        User user2 = dao.get(user.getId());

        Assert.assertThat(user2.getName(), CoreMatchers.is(user.getName()));
        Assert.assertThat(user2.getPassword(), CoreMatchers.is(user.getPassword()));
    }
}

```

* deleteAll() 을 하는 시점은 테스트를 마치기 직전이나 테스트를 시작하기 전일 수 있다. 이때, 테스트를 시작하기 전에 호출해주는 것이 좋은데, 이유는 테스트 직전에 외부 요소에 의해 데이터베이스에 추가된 임의의 데이터들도 모두 날려주고 데이터베이스를 테스트를 하기 위해 딱 좋은 상태로 만들어주기 때문이다.&#x20;



### count() 매소드 테스트 추가하기&#x20;

```java
public class UserDaoTest {
    public UserDaoTest() {
    }

    @Test
    public void andAndGet() throws SQLException {
        ...
    }

    @Test
    public void count() throws SQLException {
        ApplicationContext context = new GenericXmlApplicationContext(new String[]{"applicationContext.xml"});
        UserDao dao = (UserDao)context.getBean("userDao", UserDao.class);
        User user1 = new User("gyumee", "박성철", "springno1");
        User user2 = new User("leegw700", "이길원", "springno2");
        User user3 = new User("bumjin", "박범진", "springno3");
        
        dao.deleteAll();
        Assert.assertThat(dao.getCount(), CoreMatchers.is(0));
        
        dao.add(user1);
        Assert.assertThat(dao.getCount(), CoreMatchers.is(1));
        
        dao.add(user2);
        Assert.assertThat(dao.getCount(), CoreMatchers.is(2));
        
        dao.add(user3);
        Assert.assertThat(dao.getCount(), CoreMatchers.is(3));
    }
j
```



### 예외상황에 대한 테스트

* 만약에 id 가 존재하지 않는 회원을 조회하려고 시도했을 때는 어떻게 할까?&#x20;
* 기존의 코드 대로라면, 테스트에서 exception 을 발생시킬 것이다. 이 경우, 테스트의 예외상황을 역시 사용자가 직접 확인해야한다는 점에서 좋지 않다. 예외가 발생하는 상황에 대해서 미리 코드를 만들어 테스트해보자.&#x20;
* UserDao > get() 내부에서 id 값에 맞는 사용자가 없을 경우에 대해서 exception 을 발생시키도록 한다.&#x20;
* 테스트에서는 exception 에 대한 expectation 테스트를 넣어주면 된다.&#x20;

```java
public class UserDao {
    private DataSource dataSource;

    public UserDao() {
    }

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public void add(User user) throws SQLException {
        ... 
    }

    public User get(String id) throws SQLException {
        Connection c = this.dataSource.getConnection();
        PreparedStatement ps = c.prepareStatement("select * from users where id = ?");
        ps.setString(1, id);
        ResultSet rs = ps.executeQuery();
        User user = null;
        if (rs.next()) {
            user = new User();
            user.setId(rs.getString("id"));
            user.setName(rs.getString("name"));
            user.setPassword(rs.getString("password"));
        }

        rs.close();
        ps.close();
        c.close();
        if (user == null) {
            throw new EmptyResultDataAccessException(1);
        } else {
            return user;
        }
    }

    public void deleteAll() throws SQLException {
        ...
    }

    public int getCount() throws SQLException {
        ...
    }
}



public class UserDaoTest {
    @Test(
        expected = EmptyResultDataAccessException.class
    )
    public void getUserFailure() throws SQLException {
        this.dao.deleteAll();
        Assert.assertThat(this.dao.getCount(), CoreMatchers.is(0));
        this.dao.get("unknown_id");
    }
}
```



### 테스트 시 주의할 점‼️

* 개발자들은 자신들이 생각한 로직으로 테스트를 짜기 때문에 성공케이스만 테스트하는 경향이 있다.&#x20;
* 스프링의 창시자 로드 존슨은 항상 "네거티브 테스트를 먼저 만들라"는 조언을 했을 정도이다. &#x20;

### 테스트 주도의 개발, TDD

* 테스트 코드를 먼저 만들고, 테스트를 성공하게 해주는 코드를 작성하는 방식의 개발방법&#x20;
* 실패한 테스트를 성공시키기 위한 목적이 아닌 코드는 만들지 않는다. 이 원칙을 따른다면, 만들어진 모든 코드는 빠짐없이 테스트로 검증된 것이다.&#x20;
* 테스트를 먼저 만들고 그 테스트 상황을 충족시키기 위한 기능을 구현하면, 테스트와 기능 구현 사이의 사이클이 짧아지고, 코드에 대한 피드백을 받는 주기도 짧아지게 된다. 서버를 올리거나 사전 작업을 할 필요없이 테스트를 만들고 기능을 만들고 이 과정을 반복하면 된다.&#x20;
* TDD 장점
  * 코드를 만들고 테스트 실행하는 그 사이 시간 간격이 매우 짧다.&#x20;
  * 테스트는 코드를 가능한 빨리 실행할 수 있다.&#x20;
* 개발자들이 테스트코드를 안짜는 이유
  * 자바 엔터프라이즈 어플리케이션에 테스트를 만드는 것이 어렵다고 생각한다.&#x20;
    * 실제로 스프링 이전에는 어려웠다.&#x20;



### 테스트 코드 개선하기

* JUnit 이 하나의 테스트 클래스를 가져와 테스트를 수행하는 방식
  * 테스트 클래스에서 @Test 가 붙고 + public + void 형이며 파라미터가 없는 테스트 매소드를 모두 찾는다.&#x20;
  * 테스트 클래스의 오브젝트를 하나 만든다.&#x20;
  * @Before 매소드 실행한다. &#x20;
  * @Test 매소드를 호출하고 테스트 결과를 저장한다.&#x20;
  * @After 매소드를 실행한다.
  * 위를 반복하고 모든 테스트 결과를 종합해서 돌려준다. &#x20;
* @Before, @Test, @After 매소드를 이용하여 테스트에서 중복되는 코드를 추출하여 테스트에 필요한 선처리, 후처리를 할 수 있다.&#x20;
* JUnit 은 각 테스트 매소드를 실행할 때마다 테스트 클래스의 오브젝트를 새로 만든다.&#x20;
  * 각 테스트가 서로 영향을 주지 않고 독립적으로 실행되는 것을 보장하기 위함!&#x20;
* 모든 테스트에서 공용으로 사용되지 않는 것이라면 @Before 보다는 매소드로 추출하거나 그 기능들만 별도의 클래스에서 테스트하는 것이 좋다.&#x20;
* fixture
  * 테스트 수행시 필요한 정보나 오브젝트를 픽스처라고 한다.&#x20;
  * 다른 테스트 메소드들에서 반복적으로 사용되는 것은 @Before 에서 세팅되도록 하면 편리하다.&#x20;



## 스프링 테스트 적용

* JUnit 프레임워크 덕분에 테스트가 간편해졌지만, @Before 내에서 어플리케이션 컨텍스트를 생성하는 방식은 테스트 개수가 늘어나는 만큼 반복되어 실행되므로 좋지 않다. 어차피 싱글톤으로 만들어진 UserDao 와 데이터베이스는 모두 상태를 갖고 있지 않으므로, 한번만 만들어지고 계속 반복되어 사용되어도 괜찮다.&#x20;





## 학습 테스트로 배우는 스프링



