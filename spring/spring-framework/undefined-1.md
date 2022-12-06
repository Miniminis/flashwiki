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



###
