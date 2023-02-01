# 오브젝트와 의존관계

## 스프링과 객체지향 설계

* 스프링은 자바를 기반으로 한 기술이다. 자바의 특징 중 객체지향 프로그래밍을 가장 중요하게 생각한다.&#x20;
* 그래서 당연하게 가장 중요한 관심사는 `오브젝트.`어플리케이션에서 오브젝트가 생성되고 다른 오브젝트와 관계를 맺고 사용되고 소멸하기까지의 과정을 아는 것이 중요함
* 객체지향 설계, 디자인 패턴, 리팩토링, 단위 테스트 등과 같이 오브젝트 설계와 구현에 관한 여러가지 응용 지식과 기술이 요구된다. 스프링은 프레임워크 차원에서 이런 객체 지향 기술과 설계, 구현에 대한 전략과 베스트 프랙티스를 적용할 수 있도록 도와준다.&#x20;



{% hint style="info" %}
아래부터는 사용자 정보를 JDBC API 를 통해 DB에 저장하고, 조회할 수 있는 DAO를 만들고 이를 개선해나가는 과정을 통해 스프링과 객체지향 개념을 이해해보겠다. 코드는 [이곳](https://github.com/Miniminis/toby-spring3-1)을 참고!
{% endhint %}



## 1. 엉망진창 DAO 만들기&#x20;

사용자 정보를 저장하고 조회하는 기능을 구현해보자. 필요한 것은 대략 아래와 같을 것이다.&#x20;

* User 자바빈 클래스 : id, name, password, setters, getters
* MySQL > users 테이블 : id, name, password
* UserDAO : 사용자 정보를 DB에 넣고 관리할 수 있는 DAO 클래스
  * connection, add, get
  * main() 을 이용한 테스트 코드

핵심이 되는 UserDAO 클래스 코드만 살펴보자.&#x20;

```java
public class UserDao {
    public UserDao() {
    }

    public void add(User user) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
        PreparedStatement ps = c.prepareStatement("insert into users(id, name, password) values(?,?,?)");
        ps.setString(1, user.getId());
        ps.setString(2, user.getName());
        ps.setString(3, user.getPassword());
        ps.executeUpdate();
        ps.close();
        c.close();
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
        PreparedStatement ps = c.prepareStatement("select * from users where id = ?");
        ps.setString(1, id);
        ResultSet rs = ps.executeQuery();
        rs.next();
        User user = new User();
        user.setId(rs.getString("id"));
        user.setName(rs.getString("name"));
        user.setPassword(rs.getString("password"));
        rs.close();
        ps.close();
        c.close();
        return user;
    }
}
```



### 위의 코드의 문제점 : 관심사의 분리&#x20;

위의 코드에서 UserDAO는 크게 3개의 관심사를 가지고 있다.&#x20;

1. DB 와 연결을 위한 커넥션을 가져오는 것 : 어떤 DB를 쓰는가, 어떤 드라버를 사용, 어떤 로그인 정보, 커넥션의 생성방법 등
2. 사용자 정보를 등록하고 조회하는데 쓰이는 SQL statement 를 만들고 실행하는 것
3. 사용한 리소스(resultset, prepared statement, connection 등) 오브젝트를 닫아주어 공유 리소스를 시스템에 반환하는 것

이렇게 여러개의 관심사가 한 곳에 몰려있게 되면, 조그만 변화라도 코드에 영향을 미치게 되어 유지보수하기가 굉장히 어려워진다. 예를 들면 아래와 같은 상황들.

* 데이터베이스를 오라클에서 MySQL로 변경한다.
* add와 get 이외에 기능 매소드가 200개 정도로 늘어난 상황에서 DB 접속 정보가 변경되었다. &#x20;



## 2. 관심사의 분리

* 관심사가 같은 것끼리는 하나의 객체 안으로 혹은 친한 객체로 모이게 하고 관심이 다른 것은 가능한 한 따로 떨어져서 서로 영향을 주지 않도록 분리하는 것&#x20;
* 이와 같은 분리를 통해 각자 자신의 관심에만 집중할 수 있도록 한다.&#x20;



### 개선 1) 중복 매소드 추출하기

* connectioon 부분이 계속 중복되므로 이를 추출하여 하나의 매소드로 만들어본다.&#x20;
* 아래와 같이 개선할 경우, connection 정보가 변경되어도 하나의 매소드만 수정하면 된다.&#x20;

```java
public void add(User user) throws ClassNotFoundException, SQLException {
    Connection c = this.getConnection();
    ...
}

public User get(String id) throws ClassNotFoundException, SQLException {
    Connection c = this.getConnection();
    ...
}

private Connection getConnection() throws ClassNotFoundException, SQLException {
    Class.forName("com.mysql.jdbc.Driver");
    Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
    return c;
}
```

{% hint style="info" %}
기존 코드를 외부의 동작방식 변화없이 내부 구조를 변경해서 재구성하는 것을 `리펙토링`이라고 한다. 이렇게 리펙토링을 한 뒤에는 반드시 테스트를 통해 기능이 기존과 똑같이 동작하는지 확인하는 과정이 필요하다.&#x20;
{% endhint %}



### 개선 2) 상속을 통해서 확장시키기

* 만약에 UserDAO 서비스를 외부에서 사용하고자 할 때, 코드의 핵심 로직 공개 없이 기능만 사용할 수 있게 오픈하고 싶다면, 상속을 통해서 확장하는 방법을 생각해볼 수 있다.&#x20;
  * 현재상태라면, N사와 D사가 UserDAO 서비스를 사용하려고 한다면, 서로 다른 데이터베이스 접속 정보가 존재하기 때문에 UserDAO 코드를 직접 받아서 각자 수정해야한다. 이 경우, UserDAO 코드가 공개되므로 우리는 이 방법 말고 다른 방법을 생각해야한다.&#x20;
* 아래처럼 UserDAO를 추상클래스로 만들고, getConnection() 매소드를 추상 매소로 만들어 반드시 구현하여 사용하도록 강제한다면, 공통기능인 add() 와 get() 은 부모 클래스의 것을 그대로 이용하면서도 getConnection() 처럼 커스텀이 필요한 부분은 각자 상황에 맞게 구현하여 사용할 수 있게 된다.&#x20;
* UserDAO의 입장에서는 NUserDAO와 DUserDAO 가 커넥션을 어떻게 구현해서 사용하는지 전혀 알 필요도 없고 관심도 없다.&#x20;
* 문제점
  * 하지만 자바의 경우, 다중 상속을 지원하지 않으므로, 상속은 딱 한 번만 사용할 수 있다. 만약에 다른 상속이 이미 존재할 경우, 이 방법을 사용할 수 없다.&#x20;
  * 상속은 슈퍼-서브 클래스 간 관계가 여전히 긴밀하다. 만약 공통으로 사용되는 슈퍼 클래스의 매소드가 변경되어야 할 경우, 그를 공통으로 사용하고 있는 서브 클래스 역시 영향범위에 미치게 된다.&#x20;

```java
public abstract class UserDao {
    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = this.getConnection();
        ... 
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = this.getConnection();
        ... 
    }

    protected abstract Connection getConnection() throws ClassNotFoundException, SQLException;
}

public class NUserDao extends UserDao { 
    protected Connection getConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
        return c;
    }
}

public class DUserDao extends UserDao {
    protected Connection getConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
        return c;
    }
}

```

### 템플릿 매소드 패턴

* 슈퍼클래스에서 기본적인 로직의 흐름을 만들고, 그 기능 중 일부를 추상 매소드나 오버라이딩이 가능한 protected 매소드로 만든 뒤, 서브 클래스에서 필요에 맞게 구현해서 쓰도록 하는 것

### 팩토리 매소드 패턴

* 서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 하는 것



## 3. DAO의 확장&#x20;

### 개선 3) 클래스로 분리하기

* 아래와 같이 구현할 경우, 상속을 통한 분리보다 훨씬 더 명확하게 관심사가 구분될 수 있다. UserDao에서는 정말 기능에만 집중할 수 있고, ConnectionMaker 에서는 데이터베이스 커넥션과 관련된 로직에만 관심을 두면 된다.&#x20;
* 문제점
  * 하지만 아래와 같은 경우, 다시 N사와 D사가 각자 커스텀할 수 있도록 열어둔 connection 기능이 다시 닫히게 되었다. UserDao는 SimpleConnectionMaker 에 종속되어있기 때문에, N사에서 커스텀한 ConnectionMaker 를 구현할 경우, UserDao 내부의 코드를 수정해야한다.&#x20;
    * SimpleConnectionMaker 와 같이 인스턴스 변수가 지정되어있음
    * 커스텀한 ConnectionMaker 내부의 매소드가 getConnection 이라고 보장되지 않음. openConnection() 같이 매소드 명이 다를 경우, 다시 UserDao 코드 내에서 add(), get() 내부의 코드를 변경해야한다.&#x20;

```java
public class UserDao {
    private SimpleConnectionMaker simpleConnectionMaker = new SimpleConnectionMaker();

    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = this.simpleConnectionMaker.getConnection();
        ...
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = this.simpleConnectionMaker.getConnection();
        ...
    }
}

public class SimpleConnectionMaker {

    public Connection getConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
        return c;
    }
}
```



### 개선 4) 인터페이스 도입

* `개선 3`에서의 문제점을 해결하려면, UserDao와 ConnectionMaker 클래스 사이에 느슨한 연결고리를 두어야 한다. 자바에서는 Interface를 이용할 수 있다.&#x20;
* interface 로 구현할 매소드명이 makeConnection() 로 미리 정해져있으니, 구현 클래스마다 매소드 명이 다를 것을 우려하지 않아도 된다.&#x20;
* 문제점
  * 하지만 아래와 같은 경우, `this.connectionMaker = new DConnectionMaker();` 생성자 부분에서 특정 형태의 ConnectionMaker 생성자를 호출하고 있다. 이렇게 할 경우, 여전히 새로운 고객사가 늘어나거나 수정이 필요할 때마다 UserDao 생성자 메소드를 수정해야한다. 자유로운 DB 커넥션 확장기능을 가진 UserDao 를 제공할 수 없게 된다.&#x20;

```java
public interface ConnectionMaker {
    Connection makeConnection() throws ClassNotFoundException, SQLException;
}

public class DConnectionMaker implements ConnectionMaker {
    ...

    public Connection makeConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
        return c;
    }
}


public class UserDao {
    private ConnectionMaker connectionMaker;

    public UserDao() {
        this.connectionMaker = new DConnectionMaker();
    }

    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = this.connectionMaker.makeConnection();
        ...
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = this.connectionMaker.makeConnection();
        ...
    }
}


```



### 개선 5) 관계 설정 책임의 분리&#x20;

* UserDao 가 자유롭지 못한 이유는 한가지이다. 아직 관심사가 덜 분리되어서이다.&#x20;
  * 어떤 ConnectionMaker 구현 클래스를 사용할지 결정하는 것&#x20;
* 이 관심사를  UserDao 를 사용하는 클라이언트 단에 전가하면 어떨까
* 아래와 같이 UserDao 의 생성자에 ConnectionMaker 의 인터페이스 타입을 전달해주면, 아래와 같은 구조로 정리된다.&#x20;
  * UserDaoTest -> (생성, 제공, 사용) -> UserDao -> (사용) -> ConnectionMaker <- DConnectionMaker <- (생성) <- UserDaoTest&#x20;

```java
public interface ConnectionMaker {
    Connection makeConnection() throws ClassNotFoundException, SQLException;
}

public class DConnectionMaker implements ConnectionMaker {
    ...

    public Connection makeConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
        return c;
    }
}


public class UserDao {
    private ConnectionMaker connectionMaker;

    public UserDao(ConnectionMaker simpleConnectionMaker) {
        this.connectionMaker = simpleConnectionMaker;
    }

    public void add(User user) throws ClassNotFoundException, SQLException {
        Connection c = this.connectionMaker.makeConnection();
        ...
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = this.connectionMaker.makeConnection();
        ...
    }
}

//UserDao 를 사용하는 클라이언트 
public class UserDaoTest {
    ...

    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        //클라이언트에서 어떤 커넥션 구현체를 사용할지 결정한 뒤, 
        ConnectionMaker connectionMaker = new DConnectionMaker();    
        //UserDao 에게 전달한다. 
        UserDao dao = new UserDao(connectionMaker);
        ...
    }
}

```



### 로버트 마틴의 객체지향 설계 원칙 SOLID

* SRP, The Single Responsibility Principle : 단일책임원칙
* OCP, The Open-Closed Principle : 개방폐쇄원칙&#x20;
* LSP, The Liskov Substitution Principle : 리스코프 치환 원칙
* ISP, The Interface Segregation Principle : 인터페이스 분리 원칙&#x20;
* DIP, The Dependency Inversion Principle : 의존관계 역전 원칙&#x20;



### 개방 폐쇄 원칙, Open-Cloded Principle

* UserDao 는 DB 연결 방법이라는 기능을 확장하는 것에는 열려있다. 하지만 UserDao 자체를 변경하는 것에는 닫혀있다.&#x20;
  * UserDaoTest -> (생성, 제공, 사용) -> UserDao -> (사용) -> ConnectionMaker <- DConnectionMaker <- (생성) <- UserDaoTest&#x20;
* 인터페이스를 통해 제공되는 기능은 열려있고 인터페이스를 이용하는 클래스는 자신은 변하지 않도록 폐쇄되어있다.&#x20;



### 높은 응집도와 낮은 결합도&#x20;

* 응집도가 높다 : 하나의 모듈, 클래스가 하나의 책임 또는 관심사에만 집중되어 있다는 뜻
  * 외부에 관심과 책임이 얽혀있지 않으며 하나의 공통 관심사는 한 클래스에 모여있는 것
  * UserDao 는 ConnectionMaker 가 어떤 식으로 구현되고 수정되는지에 관심이 없다.&#x20;
* 낮은 결합도 : 책임과 관심사가 다른 오브젝트 또는 모듈과는 낮은 결합도, 느슨하게 연결된 형태를 유지
  * 하나의 오브젝트가 변경이 일어날 때에 관계를 맺는 다른 오브젝트에게 변화를 요구하는 정도
  * ConnectionMaker 와 UserDao 는 각자 자신의 책임에 대해서 응집도가 높다. 하지만 둘은 인터페이스를 통해 느슨하게 연결되어있다. 서로가 어떤 식으로 구현되어있는지는 전혀 알필요도 관심도 없다. 꼭 필요한 관계만 인터페이스를 통해 연결되어있을 뿐이다.&#x20;



### 전략패턴

* 자신의 기능 맥락에서 필요에 따라 변경이 필요한 알고리즘을 인터페이스를 통해 통째로 외부로 분리시키고, 이를 현한 구체적인 알고리즘 클래스를 필요에 따라서 바꿔쓸 수 있도록 한 디자인 패턴
* UserDao 는 자신의 기능 중에서 변경이 가능한 "DB 연결방식"이라는 알고리즘을 ConnectionMaker 인터페이스로 정의한 뒤, 이를 구현한 클래스(=전략)을 바꿔가면서 사용할 수 있도록 분리시켰다.&#x20;
* UserDao 라는 컨텍스트를 사용하는 클라이언트 UserDaoTest 에서는 컨텍스트의 생성자(UserDao()) 를 통해 컨텍스트가 사용할 전략(ConnectionMaker)를 제공해준다. &#x20;



## 4. 제어의 역전 Inversion of Control

* `개선 5`까지 코트를 개선했는데, 한 가지 문제점이 있다. 바로 데이터베이스 커넥션을 설정할 떄, 어떤 구현체를 쓸를 결정하는 책임을 UserDaoTest 라는 클라이언트 사이드로 넘겼다는 점이다. UserDaoTest 는 이미 UserDao 를 테스트한다는 책임을 가지고 있는데, 다른 책임이 전가된 것이다. 따라서 UserDao 를 생성하고 어떤 구현체를 쓸지까지 결정하는 책임을 담당하는 부분을 펙토리 클래스로 분리하여 책임을 분리해본다. &#x20;

```java
public class UserDaoFactory {
   public UserDao userDao() {
      UserDao dao = new UserDao(connectionMaker());
      return dao;
   }

   public ConnectionMaker connectionMaker() {
      ConnectionMaker connectionMaker = new DConnectionMaker();
      return connectionMaker;
   }
}

public class UserDaoTest {
	public static void main(String[] args) throws ClassNotFoundException, SQLException {
		UserDao dao = new UserDaoFactory().userDao();
		...
	}
}

```

### 설계로서의 팩토리

* 위와 같이 펙토리 매소드로 분리를 하니, 전체적으로 구조가 다음과 같이 나누어지게 되었다.&#x20;
  * 어플리케이션의 핵심 기능과 로직을 담당하는 컴포넌트 : UserDaoTest, UserDao, ConnectionMaker, DConnectionMaker
  * 어플리케이션의 생성 및 관계설정 등 구조를 결정하는 오브젝트 : UserDaoFactory



### 제어권의 이전, 제어관계의 역전

* 위처럼 UserDao 나 ConnectionMaker 등의 오브젝트를 직접 생성하고 오브젝트 간의 관계를 설정해주는 역할을 하는 펙토리 메소드를 만듬으로서, UserDao 나 ConnectionMaker 는 스스로가 어떻게 생성되고 어디서 쓰이는지 전혀 알 수가 없게 되었다. 제어권이 UserDaoFactory 로 넘어가게 된 것이다.&#x20;
* 이렇게 원래 일반적으로 프로그램의 흐름이 main() 등의 초기 매소드에서 주도권을 가지고 오브젝트의 생성 및 매소드 호출, 이후의 흐름을 결정하는 능동적인 방식이었다면 제어의 역전에서는 오브젝트가 자신이 사용할 오브젝트를 스스로 선택하지 않고 생성하지도 않는다. 어떻게 만들어지고 사용되는지도 알 수 없다. 제어권을 다른 오브젝트에게 위임하게 되는 것이다.&#x20;
* 제어의 역전 예시
  * `개선 2` 에서 상속을 통해 getConnection 을 서브클래스에서 다시 정의하여 쓰도록 하였는데, 이 경우, 슈퍼 클래스인 UserDao 는 getConnection 이 어떻게 정의된지도 모르는 채 add(), get() 을 통해서 기능을 수행한다. 그 반대로 서브클래스에서도 자신이 구현한 커넥션이 어떻게 쓰이는지 모르는채, add(), get() 매소드를 호출하여 사용한다.&#x20;
  * 프레임워크
    * 라이브러리 : 라이브러리는 어플리케이션에서 흐름을 직접 제어하고 필요한 기능을 라이브러리에서 가져다가 쓸 뿐이다. (툴킷, 엔진, 라이브러리 모두 해당함)&#x20;
    * 프레임워크 : 하지만 프레임워크의 경우, 제어의 역전 개념이 반드시 담겨있어야 한다. 라이브러리와 반대로 어플리케이션이 프레임워크가 짜놓은 틀에서 수동적으로 동작해야한다. 프레임워크가 어플리케이션의 코드를 가져다가 쓸 뿐이다.&#x20;
  * UserDao - DaoFactory 간의 관계
    * DaoFactory 에서 UserDao 의 생성, ConnectionMaker의 생성과 그 둘 사이의 관계를 설정함
    * UserDao 는 어떻게 생성되고 쓰이는지도 모른다.&#x20;

## 5. Spring 의 IoC

* 빈
  * 빈, 빈 오브젝트는 스프링이 IoC 방식으로 관리하는 오브젝트라는 뜻. 관리되는 오브젝트라고 부르기도 함.&#x20;
  * 스프링 프레임워크를 사용하는 어플리케이션에서 모든 오브젝트를 빈이라고 부르는 것이 아니라, 스프링에 의해 생성되고 제어되는 오브젝트만을 빈이라고 한다.&#x20;
* 빈 팩토리&#x20;
  * 스프링 IoC 를 담당하는 핵심 컨테이너. 빈을 등록, 생성, 조회, 반환, 부가적으로 빈을 관리하는 기능을 담당한다. 보통은 빈 팩토리를 바로 쓰지 않고 이를 확장한 어플리케이션 컨텍스트를 사용한다. 이곳에 getBean() 과 같은 매소드가 정의되어있다.&#x20;
* 어플리케이션 컨텍스트
  * 빈 팩토리를 확장한 IoC 컨테이너이다. 빈을 등록하는 등 빈 팩토리가 가진 기능을 모두 가지고 있으며, 추가적으로 스프링이 제공하는 각종 부가서비스를 제공한다. BeanFactory 를 상속한다.&#x20;
* 설정정보/설정 메타정보
  * 빈 펙토리나 어플리케이션 컨텍스트에서 IoC를 적용하기 위해서 참고하는 설정정보를 말한다. configuration 이라고 하며, IoC 컨테이너에 의해 관리되는 어플리케이션 오브젝트를 생성하고 구성할 떄 사용된다.&#x20;
* 컨테이너 혹은 IoC 컨테이너
  * IoC 방식으로 빈을 관리한다는 의미에서 어플리케이션 컨텍스트나 빈 팩토리를 컨테이너 혹은 IoC 컨테이너라고 한다.&#x20;
* 스프링 프레임워크
  * IoC 컨테이너, 어플리케이션 컨텍스트를 포함하여 스프링이 제공하는 모든 기능을 통틀어 말할 때, 주로 사용한다. &#x20;



```java
@Configuration
public class DaoFactory {
    public DaoFactory() {
    }

    @Bean
    public UserDao userDao() {
        UserDao dao = new UserDao(this.connectionMaker());
        return dao;
    }

    @Bean
    public ConnectionMaker connectionMaker() {
        ConnectionMaker connectionMaker = new DConnectionMaker();
        return connectionMaker;
    }
}


public class UserDaoTest {
    public UserDaoTest() {
    }

    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(new Class[]{DaoFactory.class});
        UserDao dao = (UserDao)context.getBean("userDao", UserDao.class);
        ...
    }
}

```

* 스프링을 사용하여 DaoFactory 를 설정정보로 사용하는 어플리케이션 컨텍스트를 만들어보았다. 어노테이션(@Configuration, @Bean) 을 통해서 설정한다.&#x20;
* 빈을 가져올 떄, 이름을 별도로 붙여주는 이유는 (userDao) 같은 빈을 가져오더라도 이를 생성하는 방식이나 구성을 다르게 하고자 할 떄를 지원하기 위함이다.&#x20;

### 어플리케이션 컨텍스트의 동작방식

* @Configuration, @Bean 등을 동해 DaoFactory 에 미리 사용할 설정정보들을 ApplicationContext 에 등록한다.&#x20;
* ApplicationContext 는 @Bean 어노테이션이 붙은 매소드의 이름을 가져와서 빈 목록을 만들두고,&#x20;
* 클라이언트에서 어플리케이션 컨텍스트의 getBean() 매소드를 호출하면, 자신의 빈 목록에서 요청한 이름이 있는지 찾고, 있다면 전달해준다.&#x20;
* 장점
  * 클라이언트에서 사용할 떄, 구체적인 펙토리 클래스를 알 필요가 없다.&#x20;
  * 어플리케이션 컨텍스트는 종합 IoC 서비스를 제공해준다.&#x20;
    * 단순히 오브젝트 생성과 관계설정 뿐인 아니라, 오브젝트가 만들어지는 방식, 시점과 전략을 다르게 할 수 있다.&#x20;
    * 부가적으로 자동생성, 후처리, 정보의 조합, 설정 방식의 다변화, 인터셉팅 등 다양한 것을 설정할 수 있다.&#x20;
  * 어플리케이션 컨텍스트는 빈을 검색하는 다양한 방법을 제공한다. &#x20;



## 6. 싱글톤 레지스트리와 오브젝트 스코프

### ApplicationContext vs. 단순한 Factory class

* 아직까지는 너무 단순해서 어플리케이션 컨텍스트나 스프링 프레임워크 없이 펙토리 클래스로만 해도 충분히 괜찮을 것 같다고 생각된다. 어떤 점에서 차이가 발생하는 것일까?&#x20;
* 오브젝트의 동등성
  * DaoFactory 클래스에서 userDao() 를 두 번 호출한다면, 매번 new 키워드로 새롭게 오브젝트가 생성되므로, 호출할 때마다 새로운 오브젝트가 생성되는 셈이다.&#x20;
  * 반면 ApplicationContext 를 통해 userDao() 를 호출한다면, 이미 bean 목록에 있는 것은 다시 조회하지 않고 반환하도록 되어있기 떄문에 매번 같은 오브젝트가 반환될 것이다.&#x20;
* 차이점은 바로 어플리케이션 컨텍스트가 싱글톤 레지스트리로서의 역할을 하고 있기 때문이다.&#x20;
  * 스프링에서 별도 설정이 없다면 내부에서 생성된 빈 오프젝트는 기본이 싱글톤&#x20;
* 스프링은 자바 엔터프라이즈 기술을 사용하는 서버환경이다. 스프링이 만들어질 때에는 서버 하나당 최대로 초당 수십에서 수백번씩 브라우저나 타 시스템으로부터 요청을 받아 처리할 수 있는 높은 성능이 요구되는 환경이었다고 한다. 이런 경우에, 매 요청마다 5개의 오브젝트가 만들어진다면, 초당 500개의 요청이 들어올 경우, 2500개의 오브젝트가 한번에 만들어지게 될 것이다. 성능에 당연히 부하가 발생할 수밖에 없다.&#x20;
  * 서블릿은 자바 엔터프라이즈 기술의 가장 기본이 되는 서비스 오브젝트임!&#x20;
  * 클래스 당 하나의 오브젝트만 만들어두고 사용자의 요청을 담당하는 여러 스레드에서 하나의 오브젝트를 공유해서 동시에 사용한다.&#x20;



### 싱글톤 패턴의 문제점

* 싱글톤 패턴에 따라 UserDao 를 바꿔본다면 아래와 같을 것이다.&#x20;
* 코드가 지저분해졌고, 리펙토링한 깔끔한 코드를 버려야한다는 점 이외에도 아래와 같은 한계들이 있을 것이다.&#x20;
* private 생성자를 가지고 있기 떄문에 상속이 불가하다. 상속을 통한 다형성은 객체지향의 장점 중 하나인데, 이것을 전혀 쓸 수가 없게 된다.&#x20;
  * static field와 매소드가 사용되는 것 역시 객체지향 특징에 위배된다.&#x20;
* 싱글톤은 테스트하기가 힘들다. 매우 만들어지는 방식이 제한적이기 때문에 테스트를 위해서 목 오브젝트를 만드는 것 등 테스트하는 과정이 매우 힘들다.&#x20;
* 서버환경에서는 싱글톤이 하나만 만들어진다고 보장되는것도 아니다.&#x20;
  * 자바 JVM 이 여러개로 분산된 환경에서는 싱글톤 패턴으로 만들었다고 해서 하나만 만들어지는 것도 아니라서 싱글톤으로서의 가치가 떨어진다.&#x20;
* 싱글톤은 언제나 전역상태이기 때문에 바람직하지 못하다.&#x20;
  * 싱글톤을 사용하는 클라이언트는 딱히 정해져있지 않고 중구난방으로 흩어져있다. 따라서 모든 이들이 접근하고 수정하고 공유할 수 있게 되는데, 이는 객체지향에서 가장 권장하지 않은 방식이다. 차라리 static field 와 method 로 된 클래스를 사용하는 편이 나을 것이다.&#x20;

```java

public class UserDao {
    private static UserDao INSTANCE;
    
    private UserDao(ConnectionMaker connectionMaker) {
        this.connectionMaker = connectionMaker;
    }
    
    public static synchronized UserDao getInstance() {
        if (INSTANCE == null) INSTANCE = new UserDao(???);
        return INSTANCE;
    }
}

```



### 싱글톤 레지스트리

* 그렇다면 스프링 프레임워크는 어떻게 오브젝트들을 싱글톤으로 관리하고 있을까. 싱글톤의 문제점들 때문에 스프링은 싱글톤 형태의 오브젝트를 만들고 관리하는 "싱글톤 레지스트리" 기능을 제공한다.&#x20;
* 오브젝트의 생성과 등록, 관계설정을 포함한 권한을 스프링 컨테이너로 넘기면, 스프링 컨테이너는 알아서 해당 오브젝트를 싱글톤 방식으로 관리해준다. 모두 스프링 컨테이너가 오브젝트의 제어권을 가지고 있기 때문에 가능한 것이다.
* 이렇게 되면 객체지향적 설계방식과 원칙, 디자인패턴 등을 모두 해치지 않고 싱글톤 방식으로 오브젝트를 관리할 수 있게 된다.&#x20;



### 빈 스코프

* 스프링이 관리하는 오브젝트인 빈이 생성되고 등록되며 적용되는 범위를 `빈 스코프`라고 한다.&#x20;
* 기본적으로 스프링 컨테이너에서는 빈을 싱글톤 스코프를 갖도록 관리한다.&#x20;
* 하지만 다른 스코프도 존재한다. prototype, request, session 등의 스코프는 매 요청마다 새로운 빈이 생성된다. &#x20;



## 7. 의존관계 주입, Dependency Injection

* IoC는 소프트웨어에서 자주 발견할 수 있는 일반적인 개념. 우리는 DaoFactory 내에서 오브젝트의 생성과 관계설정 등을 관리함으로써 IoC 컨테이너처럼 사용하였고 이러한 점을 일반화한 것이 스프링의 IoC 컨테이너라고 했다.&#x20;
* 그런데, 이 IoC 라는 용어 자체는 사실 스프링이 제공하는 기능의 특징을 100% 다 담지 못하고 있다. 그래서 등장한 것이 Dependency Injection, 의존관계 주입이다.&#x20;



### 의존관계란?

* 두 클래스나 모듈이 의존관계가 있을 때에는 항상 방향성이 존재한다.&#x20;
* A가 B에 의존한다는 말은
  * B의 변화에 A가 영향을 받는다는 것을 의미한다.&#x20;
* 예를 들어 A가 B에서 정의한 매소드를 가져다가 쓴다고 해보자. 매소드의 이름이나 사용방법은 같은데, 내부 로직이 변했다면, B에서만 수정한 것이지만, A에도 그 영향이 있다. 또한 B에서 해당 매소드가 사라지거나 추가될 때에도 A가 영향을 받을 수 있다. 이때, A는 B에 의존한다는 관계가 성립되는 것이다.&#x20;
* UserDao 에서는 의존관계가 다음과 같았다.&#x20;
  * UserDao -> 사용 -> ConnectionMaker&#x20;
  * ConnectionMaker <- 구현 <- DConnectionMaker
* 이때, UserDao 는 DConnectionMaker 가 아니라 ConnectionMaker 인터페이스에 의존하고 있으므로, DConnectionMaker 의 변화에 영향을 받지 않게 된다. 둘 사이의 관계가 느슨해지면서 결합도가 낮아지고 있다. &#x20;



### 의존관계 주입

{% hint style="info" %}
구체적인 의존 오브젝트와 그것을 사용할 주체(클라이언트) 오브젝트를 런타임 시에 연결해주는 작업을 말한다.  이를 위해서는 아래와 같은 세 가지 조건을 충족해야한다.&#x20;
{% endhint %}

1. 클래스 모델이나 코드에는 런타임 시점의 의존관계가 드러나지 않는다. 이를 위해서는 인터페이스에만 의존하고 있어야 한다.&#x20;
2. 런타임 시점의 의존관계는 컨테이너나 팩토리 같은 제 3의 존재가 결정한다.&#x20;
3. 의존관계는 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공(주입) 해줌으로써 만들어진다.&#x20;



핵심은&#x20;

* 제 3의 존재에 의해서 두 오브젝트가 생성되고 관리되며 관계를 맺는다는 점이다.&#x20;
* DaoFactory, IoC Container, ApplicationContext, BeanFactory 등 모두 외부에서 오브젝트 사이의 런타임 관계를 맺어주는 책임을 지닌 제 3의 존재
* UserDao 예제에서는 1) UserDao 가 ConnectionMaker 라는 인터페이스에 의존하고 있고, 2) DaoFactory 라는 외부 존재가 3) UserDao 의 생성자를 통해 DConnectionMaker 와의 의존관계를 주입시켜준 것이다.&#x20;
* 의존관계 주입
  * DI 컨테이너에 의해 런타임시에 의존 오브젝트를 사용할 수 있도록 그 레퍼런스를 전달받는 과정이 마치 매소드를 통해 DI 컨테이너가 UserDao 에게 주입해주는 것과 같다고 해서 의존관계 주입이라고 부른다.&#x20;

### 의존관계 검색 vs. 주입&#x20;

* 의존관계 검색 : 자신이 필요한 오브젝트를 스스로 검색하여 찾는다.&#x20;
  * 런타임시 의존관계를 맺을 오브젝트를 결정하는 것과 오브젝트의 생성 작업은 외부 컨테이너에게 IoC로 맡긴다.&#x20;
  * 다만 이를 가져올 때에는 매소드나 생성자를 통핸 주입 대신에, 스스로 컨테이너에게 요청하는 방법을 사용한다.
* 그동안 생성자를 통해서 받았던 것과 비교해서 보면 UserDao 내부에서 필요한 것을 컨테이너인 DaoFactory 에게 요청하고 있는 것을 볼 수 있다.&#x20;
  * 미리 정해놓은 이름을 전달하여 그 이름에 해당하는 오브젝트를 찾게 되므로 일종의 검색이다.&#x20;

```java
//생성자를 통한 의존 관계 주입
public class UserDao {
    private ConnectionMaker connectionMaker;

    public UserDao(ConnectionMaker simpleConnectionMaker) {
        this.connectionMaker = simpleConnectionMaker;
    }
}

//의존관계 검색
public UserDao() {
    DaoFactory daoFactory = new DaoFactory();
    ConnectionMaker connectionMaker = daoFactory.connectionMaker();
}
```

{% hint style="info" %}
위에서 볼 수 있다시피, 의존관계 검색보다는 의존관계 주입이 훨씬 더 코드가 단순하고 깔끔하다.&#x20;

* 검색의 경우, 어플리케이션 컴포넌트의 코드 내에 팩토리 클래스나 스프링 API 가 나타나게 된다. 이 경우, 어플리케이션 컴포넌트가 컨테이너와 같이 성격이 다른 오브젝트에 의존하게 되는 것이므로 바람직하지 않다.&#x20;
{% endhint %}

### 언제 의존관계 검색을 사용하는가?

* 테스트코드인 UserDaoTest 를 보면, 이미 검색방식인 getBean() 을 사용하고 있다.&#x20;
* 스프링의 IoC와 DI 컨테이너를 사용한다고는 해도, 어플리케이션의 구동 시점에서 적어도 한 번은 의존관계 검색 방식을 통해 오브젝트를 가져와야 하는 것이다.&#x20;
* static method 인 main() 에서는 DI 를 통해 오브젝트를 주입받을 수 없기 때문!&#x20;

### 의존관계 주입의 응용

* DI 의 장점
  * 코드에는 런타임 클래스에 대한 의존관계가 드러나지 않는다.&#x20;
  * 인터페이스를 통해 결합도가 낮는 코드를 만드므로, 다른 책임을 가진 사용 의존관계에 있는 대상이 바뀌거나 변경되더라도 영향으로부터 자유롭다.&#x20;
  * 반면, 변경을 통한 다양한 확장 방법에는 또 자유롭다.&#x20;
  * LocalDBConnection 과 ProductionDBConnection 등 두 환경을 바꿔주어야 하는 경우나, 매 DB 커넥션마다 특정 값을 계산하는 등 부가기능을 추가할 때에도 아주 유용하게 사용할 수 있다.&#x20;
* DAO 가 얼마나 많이 DB 연결을 해서 사용하는지 파악하는 부가기능을 추가한다고 가정해보자. 모든 DAO 에 직접 makeConnection() 매소드를 호출하는 부분에, 새로 추가한 카운터를 증가시키는 코드를 넣지 않아도 된다.
* DI 를 이용하여 의존관계만 좀 변형시키면 된다.&#x20;
  * 변경 전 : UserDao -> (ConnectionMaker) -> DConnectionMaker
  * 변경 후 : UserDao -> (ConnectionMaker) -> CountingConnectionMaker -> DConnectionMaker



```java
@Configuration
public class CountingDaoFactory {
   @Bean
   public UserDao userDao() {
      return new UserDao(connectionMaker());
   }

   @Bean
   public ConnectionMaker connectionMaker() {
      return new CountingConnectionMaker(realConnectionMaker());
   }

   @Bean
   public ConnectionMaker realConnectionMaker() {
      return new DConnectionMaker();
   }
}

public class UserDaoConnectionCountingTest {
	public static void main(String[] args) throws ClassNotFoundException,
			SQLException {
		AnnotationConfigApplicationContext context = 
			new AnnotationConfigApplicationContext(CountingDaoFactory.class);		
		UserDao dao = context.getBean("userDao", UserDao.class);
		
		...

		CountingConnectionMaker ccm =  context.getBean("connectionMaker", CountingConnectionMaker.class);
		System.out.println("Connection counter : " + ccm.getCounter());		
	}
}

```



### 의존관계를 주입받는 방법들

* 방법들
  * 생성자 : 기존 코드들은 생성자를 통해 주입받았음&#x20;
  * setter 등의 수정자 매소드
  * 일반 메소드를 이용한 주입&#x20;
* 스프링에서는 보통 수정자 setter 매소드를 통해서 DI 를 많이 진행해왔다. 자바코드 대신에 XML 을 사용하는 경우, 자바빈 규약을 따르는 수정자 메소드가 가장 사용하기 편리하다.&#x20;
* 이름짓기 신중해야한다. 딱히 정할 이름이 없다면, 주입받을 오브젝트의 타입 이름을 따르는 것이 가장 무난하다.&#x20;
  * ex) setConnectionMaker();

```java
public class UserDao {
    private ConnectionMaker connectionMaker;

    public UserDao() {
    }

    public void setConnectionMaker(ConnectionMaker simpleConnectionMaker) {
        this.connectionMaker = simpleConnectionMaker;
    }
}

@Configuration
public class DaoFactory {
    public DaoFactory() {
    }

    @Bean
    public UserDao userDao() {
        UserDao dao = new UserDao();
        dao.setConnectionMaker(this.connectionMaker());
        return dao;
    }

    @Bean
    public ConnectionMaker connectionMaker() {
        ConnectionMaker connectionMaker = new DConnectionMaker();
        return connectionMaker;
    }
}
```



## 8. XML 을 통한 설정

{% hint style="info" %}
java 코드로 DaoFactory 에서 설정하는 것의 문제점
{% endhint %}

* 틀에 박힌 구조가 반복된다.&#x20;
* DI 구성이 바뀔 때마다 자바코드를 수정하고 다시 컴파일 해야한다. (CounterConnectionMaker 추가할 때처럼)&#x20;



XML 방식을 포함하여 다양한 방식으로 DI 의존관계 설정정보를 만들 수 있다.&#x20;

1. DaoFactory 설정정보를 XML 문서로 전환
2. ConnectionMaker -> DataSource 인터페이스 적용&#x20;
3. Connection 정보 설정
   1. 자바 코드를 이용한 설정 방식
   2. XML 을 이용한 설정 방식

```java
//DataSource Interface 적용
public class UserDao {
    private DataSource dataSource;

    public UserDao() {
    }

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }    

```

```java
//javaCode 를 활용한 설정방법
@Configuration
public class DaoFactory {
    public DaoFactory() {
    }

    @Bean
    public DataSource dataSource() {
        SimpleDriverDataSource dataSource = new SimpleDriverDataSource();
        dataSource.setDriverClass(Driver.class);
        dataSource.setUrl("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8");
        dataSource.setUsername("spring");
        dataSource.setPassword("book");
        return dataSource;
    }

    @Bean
    public UserDao userDao() {
        UserDao userDao = new UserDao();
        userDao.setDataSource(this.dataSource());
        return userDao;
    }
}
```



```xml
<!-- XML을 이용한 설정방법 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://www.springframework.org/schema/beans 
                  http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
   
   <bean id="myConnectionMaker" class="springbook.user.dao.DConnectionMaker">
      <property name="driverClass" value="com.mysql.jdbc.Driver" />
      <property name="url" value="jdbc:mysql://localhost/springbook?characterEncoding=UTF-8" />
      <property name="username" value="spring" />
      <property name="password" value="book" />
   </bean>

   <bean id="userDao" class="springbook.user.dao.UserDao">
      <property name="connectionMaker" ref="myConnectionMaker" />
   </bean>
</beans>ml
```



