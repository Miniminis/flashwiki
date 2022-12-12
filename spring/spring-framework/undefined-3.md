# 템플릿

## 템플릿

* 개방폐쇄원칙을 보면, 코드 내에서도 변경을 통해 기능이 다양해지려는 성질도 있고, 고정되어 변하지 않으려는 성질도 있다는 것을 알 수 있다. 변화의 특성이 서로 다른 부분들을 구분하고, 각가 다른 목적과 다른 이유에 의해 다른 시점에 독립적으로 변경될 수 있는 효율적인 구조를 만들어주는 것이 바로 개방폐쇄 원칙인 셈!&#x20;
* 템플릿의 경우, 이렇게 바뀌는 성질이 다른 코드 중에서 변경이 거의 일어나지 않으며 일정한 패턴으로 유지되는 특성을 가진 부분을 자유롭게 변경되는 성질을 가진 부분으로부터 독립시켜 효과적으로 활용할 수 있도록 하려는 방법이다.



## 예외처리 코드 추가하기

* 기존까지 진행된 리펙토링을 위해 많은 구조 개선이 있었지만, 한 가지 문제점, 예외처리가 되지 않았다는 문제점이 있었다. 특히나 DB 커넥션과 같은 제한적인 리소스를 공유해서 사용하는 서버에서 동작하는 JDBC 코드에서 예외처리는 필수적이다. 중간에 예외가 발생해서 리소스가 제대로 반환이 안될 경우, 계속 문제가 누적되어 나중에는 서버가 멈추는 현상까지 갈 수 있기 때문이다.&#x20;
* 아래와 같이 예외처리를 추가해보았다.&#x20;

```java
public class UserDao {
    private DataSource dataSource;

    public UserDao() {
    }

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
    
    ...

    public void deleteAll() throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = this.dataSource.getConnection();
            ps = c.prepareStatement("delete from users");
            ps.executeUpdate();
        } catch (SQLException var15) {
            throw var15;
        } finally {
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException var14) {
                }
            }

            if (c != null) {
                try {
                    c.close();
                } catch (SQLException var13) {
                }
            }

        }
    }
    
    ...
}

```

### 문제점

* 위의 코드 상 문제점은 `c.prepareStatement("delete from users"); 사실상 이 구절만을 제외하고는 UserDao 내의 다른 매소드(add(), get(), getCount() 등) 에서 위의 구절이 반복된다는 것이다.`
* 복사 붙여넣기를 하는 것은 문제를 해결할 수 없다. 도중에 한 줄의 코드라도 실수로 잘못 붙여넣거나 빠뜨리게 되면, connection 자원은 계속 누수가 생길 것이고, 언젠가는 서버가 멈추게 된다.&#x20;
* 이 상황을 효과적으로 해결하기 위해서는 변하는 부분과 변하지 않는 부분을 명확하게 파악하고 일종의 템플릿화를 함으로써 접근할 수 있다. &#x20;



## 분리와 재사용을 위한 디자인 패턴 적용

생각해보면, 변하는 부분과 변하지 않는 부분은 명확하다.&#x20;

* 변하는 부분 : `ps = c.prepareStatement("delete from users");`
* `변하지 않는 부분 : 나머지`



### 1. 매소드 추출 방법 ❌

* 그렇다면 우선 변하는 부분을 메소드로 빼는 방법을 선택해볼 수 있다.&#x20;
* 하지만 이렇게 진행할 경우, 매소드의 내용이 다른 곳에 재사용할 수 있는 것이어야 하는데, 이 경우는 반대로 매소드 이외의 부분을 재사용하고, 매소드 부분은 DAO 로직마다 새롭게 만들어야 한다.

public class UserDao { private DataSource dataSource;

```java
public class UserDao {
    private DataSource dataSource;

    public UserDao() {
    }

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
    
    ...

    public void deleteAll() throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = this.dataSource.getConnection();
            ps = makeStatement(c);        //매소드로 추출한 부분! 
            ps.executeUpdate();
        } catch (SQLException var15) {
            throw var15;
        } finally {
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException var14) {
                }
            }

            if (c != null) {
                try {
                    c.close();
                } catch (SQLException var13) {
                }
            }

        }
    }
    
    ...
    
    private PreparedStatement makeStatement(Connection c) throws SQLException {
        PreparedStatement = ps;
        ps = c.prepareStatement("delete from users");
        return ps;
    }
}
```



### 2. 템플릿 메소드 패턴 적용 ❌

* 템플릿 매소드 패턴은 상속을 통해 기능을 확장해서 사용하는 것
* 변하지 않는 부분은 슈퍼클래스로 두고, 변하는 부분을 추상 매소드로 정의해두어 서브클래스에서 오버라이드하여 새롭게 정의해 쓰도록 한다.&#x20;

```java
public class UserDao {
    ...
    
    abstract private PreparedStatement makeStatement(Connection c) throws SQLException;
}


public class UserDaoDeleteAll extends UserDao {
    
    protected PreparedStatement makeStatement(Connection c) throws SQLException {
        PreparedStatement ps = c.prepareStatement("delete from users");
        return ps;
    }
}
```

* 하지만 이렇게 될 경우, 새로운 DAO 로직이 늘어날 때마다 UserDao 클래스를 오버라이딩하는 새로운 클래스를 만들어야 한다는 단점이 있다.&#x20;
  * UserDao
    * UserDaoAdd
    * UserDaoDeleteAll
    * UserDaoGet
    * UserDaoGetCount
    * ...
* 또한 확장구조가 이미 클래스를 설계하는 시점에 고정되어버린다. 이렇게 되면 관계의 유연성이 떨어지게 된다.&#x20;



### 3. 전략 패턴 적용&#x20;

* 개방폐쇄원칙을 잘 지키면서 템플릿 매소드 패턴보다 유연하고 확장성이 뛰어난 것이 바로 전략패턴이다.&#x20;
* 전략 패턴에서는 오브젝트를 아예 둘로 분리하고, 클래스 레벨에서는 인터페이스를 통해서만 의존하도록 만드는 방법이다.&#x20;
* 확장에 해당하는 변하는 부분을 별도의 클래스로 만들어 추상화된 인터페이스를 통해 위임하는 방식.&#x20;



#### 예) deleteAll()

예를 들어 deleteAll() 을 통해서 보자면, 이 기능은 아래와 같은 컨텍스트를 가지고 있다.&#x20;

1. DB 커넥션 가져오기&#x20;
2. PreparedStatement 를 만들어줄 외부 기능 호출하기&#x20;
3. 전달받은 PreparedStatement 실행하기
4. 예외가 발생하면 이를 다시 메소드 밖으로 던지기&#x20;
5. 모든 경우에 만들어진 PreparedStatement 와 Connection 을 적절하게 닫아주기&#x20;

여기에서 2번을 제외하고는 사실상 모든 Dao 로직에서 반복된다. 따라서 다음과 같이 인터페이스를 이용하여 바꿔볼 수 있다.&#x20;



```java
public interface StatementStrategy {
    PreparedStatement makePreparedStatement(Connection var1) throws SQLException;
}

public class DeleteAllStatement implements StatementStrategy {
    public PreparedStatement makePreparedStatement(Connection c) throws SQLException {
        PreparedStatement ps = c.prepareStatement("delete from users");
        return ps;
    }
}

public class UserDao() {
    ...
    public void deleteAll() throws SQLException {
        try{
            c = dataSource.getConnection();
            
            StatementStrategy strategy = new DeleteAllStatement();
            ps = strategy.makePreparedStatement();
        
            ps.executeUpdate();
        } catch (SQLException e){
        ...
        }
    }
}

```

&#x20;하지만 이 경우, 또 다시 문제점이 발생한다. 컨텍스트인 deleteAll() 에서 이미 구체적인 전략인 DeleteAllStatement() 를 사용하도록 고정되어있다. 컨텍스트가 인터페이스 이외에 인터페이스의 구현체까지 알고 있는 것은 좀 이상하다.&#x20;



### 4. DI 적용을 위한 클라이언트/컨텍스트 분리&#x20;

보통 전략패턴에 따르면 아래와 같은 구조가 되어야 한다.&#x20;

* 클라이언트는 전략을 선택 및 생성하고, 이러한 전략을 컨텍스트에 제공한다.&#x20;
* 클라이언트로부터 전략을 받은 컨텍스트는 전략 인터페이스 내 매소드를 호출한다.&#x20;
* 전략 인터페이스의 구현체 매소드가 실행된다.&#x20;



따라서, UserDao 는 아래와 같은 구조로 리펙토링 되어야 할 것이다.&#x20;

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

```java
public class UserDao {
    ...

    //컨텍스트
    public void jdbcContextWithStatementStrategy(StatementStrategy stmt) throws SQLException {
        Connection c = null;
        PreparedStatement ps = null;

        try {
            c = this.dataSource.getConnection();
            ps = stmt.makePreparedStatement(c);
            ps.executeUpdate();
        } catch (SQLException var15) {
            throw var15;
        } finally {
            if (ps != null) {
                try {
                    ps.close();
                } catch (SQLException var14) {
                }
            }

            if (c != null) {
                try {
                    c.close();
                } catch (SQLException var13) {
                }
            }

        }

    }
    
    ...

    //클라이언트
    public void deleteAll() throws SQLException {
        Statement st = new DeleteAllStatement();
        jdbcContextWithStatementStrategy(st);
    }

    ...
}
```



## JDBC 전략 패턴의 최적화&#x20;

* 위까지 리펙토링을 했지만, 그럼에도 불구하고 2가지의 아쉬움이 있다.&#x20;
  * DAO 로직이 새로 생겨날 때마다, StatementStrategy 구현 클래스를 만들어야 한다. 이렇게 계속 새로 생성되는 것은 로직마다 상속하는 템플릿 메소드 패턴과 다를 것이 없다.&#x20;
  * 또 다른 점은 add() 와 같은 매소드의 경우는 User 와 같은 부가정보를 넘겨받아 statement 를 실행하기 때문에, 부가적인 생성자를 만들어주어야 한다.&#x20;



이 문제를 해결하는 방법은 두 가지 방법이 있을 것 같다. 1. 로직클래스와 2. 익명클래스 이다.&#x20;



### 로컬 클래스

로컬 클래스는 매소드 내부에 정의된 클래스를 말한다. DeleteAllStatement 나 AddStatement 는 어차피 한번씩 UserDao 에서만 사용된다. 그렇다면 굳이 외부 클래스로 두지 말고 매소드 내부에 클래스 정의를 하자는 입장이다.&#x20;





### 익명 내부 클래스

###

## 컨텍스트와 DI&#x20;

### 클래스 분리

### 빈 의존관계 변경&#x20;

&#x20;

##

&#x20;

