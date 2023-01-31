---
description: DAO 에서 SQL 을 제거하는 과정의 연속
---

# 스프링 핵심 기술의 응용

## 스프링의 3대 핵심 기술 : IoC/DI, 서비스 추상화, AOP

* 스프링이 가장 가치를 두고 적극적으로 활용하고자 하는 것은 결국 자바 언어 기반인 객체지향 기술!
* 스프링은 객체지향적인 언어의 장점을 적극 활용하여 코드를 작성하도록 도와준다.

## UserDao 에서는 더 개선할 점이 없나?

* SQL을 DAO 에서 분리하는 작업
* 현재 DAO는 반복적인 JDBC 작업 흐름도 템플릿을 이용해서 제거하고, 트랜잭션과 예외처리도 서비스 추상화와 AOP 를 이용하여 처리해서 깔끔한 상태.
* 하지만 여전히 DB 테이블과 필드정보를 그대로 담고 있는 SQL 이 존재한다.
* 현재 구조대로라면 데이터베이스의 테이블 구조나 필드 정보가 변경될 떄마다 UserDao 코드가 변경되어야 한다. SQL 이 변경되기 때문이다.
* 이 경우, 변경될 필드나 테이블 정보를 일괄적으로 수정할 필요가 생기게 되는데, 사람이 직접 하는 작업이라 실수가 발생할 여지가 높고 문제가 생기면 지금 운영중인 어플리케이션의 쿼리가 어떤 것인지 직접 자바 소스코드에서 찾아야 한다.
* 따라서 SQL 문을 DAO 에서 분리하여 다른 파일이나 위치에 두고 관리할 수 있도록 분리하는 작업을 해보려고 한다.

### 방법 1. XML 설정을 이용한 분리

* 사용할 SQL 문장을 XML 설정파일에 세팅해두고 스프링이 빈에 값을 주입해도록 하는 방법
* UserDao 코드 수정없이 XML 설정를 바꾸기만해도 자유롭게 수정이 가능하다.
* 하지만 SQL 이 점점 많아지면, DAO 에 SQL 용 프로퍼티를 추가하는 것이 귀찮아진다. sqlAdd 이외에 sqlUpgradeLevels 프로퍼티를 추가하려면 결국 DAO내부에도 이를 DI 받기 위한 변수과 수정자 메소드 setter 를 추가해주어야 한다. 상당히 귀찮다.

```xml

<bean id="userDao" class="springbook.user.dao.UserDaoJdbc">
   <property name="dataSource" ref="dataSource" />
   <property name="sqlAdd" value ="insert into users(id, name, password, email, level, login, recommend) values(?,?,?,?,?,?,?)" />
</bean>
```

```java
public class UserDaoJdbc implements UserDao {
		private String sqlAdd;

		...
		public void setSqlAdd(String sqlAdd) {
        this.sqlAdd = sqlAdd;  // DI 주입받는 SQL 문장 
    }

		...
		public void add(User user) {
	        this.jdbcTemplate.update(
						this.sqlAdd,  //사용할 SQL 문장 
						user.getId(), user.getName(), user.getPassword(), user.getEmail(), user.getLevel().intValue(), user.getLogin(), user.getRecommend()
					);
    } 
}
```

### 방법 2. SQL 맵 프로퍼티 방식

* SQL 이 늘어날때마다 프로퍼티를 추가하기 귀찮으니, 아예 Map 컬렉션 내부에 담아주는 방법을 생각해본다.
* 새로운 SQL 이 늘어나면, 이제 entry 만 추가해줘도 좋다.
* 다만 이 방법은 SQL 문장 자체에 오류가 있을 경우, 직접 메소드 실행 전에는 오류를 발견하기가 어렵다는 단점이 있다. test 를 철저하게 작성하여 미리미리 검증할 필요가 있는 것이다.

```xml
<!--  application components -->
<bean id="userDao" class="springbook.user.dao.UserDaoJdbc">
   <property name="dataSource" ref="dataSource" />
   <property name="sqlMap">
      <map>
         <entry key="add" value="insert into users(id, name, password, email, level, login, recommend) values(?,?,?,?,?,?,?)" />
         <entry key="get" value="select * from users where id = ?" />
         <entry key="getAll" value="select * from users order by id" />
         <entry key="deleteAll" value="delete from users" />
         <entry key="getCount" value="select count(*) from users" />
         <entry key="update" value="update users set name = ?, password = ?, email = ?, level = ?, login = ?, recommend = ? where id = ?"  />
      </map>
   </property>
</bean>
```

```java
public class UserDaoJdbc implements UserDao {
	...
		public void setSqlMap(Map<String, String> sqlMap) {
        this.sqlMap = sqlMap;
    }

		...
		public void add(User user) {
	        this.jdbcTemplate.update((String)this.sqlMap.get("add"), new Object[]{user.getId(), user.getName(), user.getPassword(), user.getEmail(), user.getLevel().intValue(), user.getLogin(), user.getRecommend()});
    } 
}

```

### XML 설정 및 SQL 맵 프로퍼티 방식의 문제점

* 스프링 설정정보와 데이터 엑세스 로직의 일부인 SQL 이 섞여있게 된다.
* SQL 을 따로 분리해두어야 독립적으로 SQL 문의 리뷰나 SQL 튜닝 작업을 수행하기에도 편하다.
* 생각의 전환
  * 꼭 XML 파일에 담아두어야 할까? 이렇게 되면 스프링 어플리케이션을 재시작하기 전까지는 변경도 매우 어렵다.
  * 엑셀이나 외부 파일에서 읽어올 수도 있지 않을까?
  * 아니면 DB 나 웹서비스 혹은 리모팅을 이용해 최신 SQL 정보를 보관해주는 외부 시스템에서 가져올 수도 있을 것이다.
* 필요한 것
  * 결국, 필요한 것은 독립적인 SQL 제공 서비스가 필요하다. 다양한 SQL 정보 소스를 사용할 수 있도록, 그리고 운영중에도 동적으로 갱신할 수 있도록 유연하고 확장성이 뛰어난 SQL 서비스가 필요하다.

## SQL 제공 서비스 만들기

* SQL 서비스 인터페이스 설계
  * 주어진 키를 가지고 SQL 문을 가져올 수 있도록 함
* 주어진 키에 맞는 SQL 조회 실패하면 exception 반환하도록 runtimeException 새롭게 정의
* UserDaoJdbc 는 SqlService 타입의 빈을 DI 받을 수 있도록 변수 및 수정자 메소드 setters 추가
* sqlService 를 사용하도록 UserDao 메소드 수정
* SqlService 가장 간단한 버전으로 먼저 구현 - 이전에 썼던 map 프로퍼티를 쓰는 방식으로 일단 구현

```java
//1. SQL 서비스 인터페이스 설계 
public interface SqlService {
    String getSql(String var1) throws SqlRetrievalFailureException;
}

//2. 주어진 키에 맞는 SQL 조회 실패하면 exception 반환하도록 runtimeException 새롭게 정의
public class SqlRetrievalFailureException extends RuntimeException {
    public SqlRetrievalFailureException() {
    }

    public SqlRetrievalFailureException(String message) {
        super(message);
    }

    public SqlRetrievalFailureException(Throwable cause) {
        super(cause);
    }

    public SqlRetrievalFailureException(String message, Throwable cause) {
        super(message, cause);
    }
}

//3. UserDaoJdbc 는 SqlService 타입의 빈을 DI 받을 수 있도록 변수 및 수정자 메소드 setters 추가
public class UserDaoJdbc implements UserDao {
    ...
		private SqlService sqlService;
		
		public void setSqlService(SqlService sqlService) {
        this.sqlService = sqlService;
    }
		...
}

//4. sqlService 를 사용하도록 UserDao 메소드 수정
public class UserDaoJdbc implements UserDao {
		...
		public void add(User user) {
        this.jdbcTemplate.update(this.sqlService.getSql("userAdd"), new Object[]{user.getId(), user.getName(), user.getPassword(), user.getEmail(), user.getLevel().intValue(), user.getLogin(), user.getRecommend()});
    }

    public User get(String id) {
        return (User)this.jdbcTemplate.queryForObject(this.sqlService.getSql("userGet"), new Object[]{id}, this.userMapper);
    }

    public void deleteAll() {
        this.jdbcTemplate.update(this.sqlService.getSql("userDeleteAll"));
    }

    public int getCount() {
        return this.jdbcTemplate.queryForInt(this.sqlService.getSql("userGetCount"));
    }

    public List<User> getAll() {
        return this.jdbcTemplate.query(this.sqlService.getSql("userGetAll"), this.userMapper);
    }

    public void update(User user) {
        this.jdbcTemplate.update(this.sqlService.getSql("userUpdate"), new Object[]{user.getName(), user.getPassword(), user.getEmail(), user.getLevel().intValue(), user.getLogin(), user.getRecommend(), user.getId()});
    }
		...
}

//5. SqlService 구현
public class SimpleSqlService implements SqlService {
    private Map<String, String> sqlMap;

    public SimpleSqlService() {
    }

    public void setSqlMap(Map<String, String> sqlMap) {
        this.sqlMap = sqlMap;
    }

    public String getSql(String key) throws SqlRetrievalFailureException {
        String sql = (String)this.sqlMap.get(key);
        if (sql == null) {
            throw new SqlRetrievalFailureException(key + "를 이용해서 SQL을 찾을 수 없습니다");
        } else {
            return sql;
        }
    }
}
```

```xml
<!--  application components -->
<bean id="userDao" class="springbook.user.dao.UserDaoJdbc">
	<property name="dataSource" ref="dataSource" />
	<property name="sqlService" ref="sqlService" />
</bean>

<bean id="sqlService" class="springbook.user.sqlservice.SimpleSqlService">
   <property name="sqlMap">
      <map>
         <entry key="userAdd" value="insert into users(id, name, password, email, level, login, recommend) values(?,?,?,?,?,?,?)" />
         <entry key="userGet" value="select * from users where id = ?" />
         <entry key="userGetAll" value="select * from users order by id" />
         <entry key="userDeleteAll" value="delete from users" />
         <entry key="userGetCount" value="select count(*) from users" />
         <entry key="userUpdate" value="update users set name = ?, password = ?, email = ?, level = ?, login = ?, recommend = ? where id = ?"  />
      </map>
   </property>
</bean>
```

#### 장점

* 이제 UserDao 는 SQL 문을 어디에 저장해두고 가져오는지 신경쓰지 않아도 된다. SqlService 인터페이스 타입의 빈을 DI 받아서 필요한 SQL 을 가져다가 쓰기만 하면 된다.

## SqlService 인터페이스 구현 방법 개선하기

* 현재 상태
  * UserDao 에서 SQL 문을 완전히 분리해냈다.
  * 하지만 데이터 엑세스를 위한 SQL 문들이 스프링 설정정보에 뒤섞여있다.
  * SQL 문장과 그를 검색할 키만 담은 XML 문서를 설계한 뒤, SQL 을 읽어뒀다가 DAO 에게 제공해주는 SQL 서비스 구현 클래스가 필요하다.

### JAXB

* XML 파일에 담긴 정보를 파일에서 읽어오는 방법은 다양한데, 가장 간단한 방법은 JAXB 를 이용하는 것이다.
* JAXB 는 Java Architecture for XML Binding 의 약자로, JDK 6 java.xml.bind 패키지 안에서 JAXB 의 구현 클래스를 찾을 수 있다.
* 장점
  * XML 정보를 그대로 담고 있는 오브젝트 트리 구조로 만들어줌. 따라서 XML 정보를 오브젝트처럼 다룰 수 있어서 편리함
  * 스키마를 이용해서 매핑할 오브젝트의 클래스까지 자동으로 만들어주는 컴파일러도 제공한다.
* JAXB API 는 애노테이션에 담긴 정보를 이용해서 XML 과 매핑된 오브젝트 트리 사이의 자동변환 작업을 수행해준다.
* 동작방식
  * XML 스키마 → 스키마 컴파일러 → XML 정보를 담을 수 있는 매핑용 클래스 → 참고 → JAXB API → XML 문서 정보를 담은 오브젝트 트리

#### 1. SQL 맵을 위한 스키마 작성 및 컴파일

* 아래와 같이 SQL 맵 XML 문서를 만들어준다.
* 스키마 파일도 만들어준다. - XML 툴이나 이클립스의 스키마 에디터 이용
* sqlmap.xsd 이름으로 프로젝트 루트에 저장
* JAXB 컴파일러로 컴파일
* 프로젝트 루트 폴더에서 다음 명령어 실행
  * `xjc -p springbook.user.sqlservice.jaxb sqlmap.xsd -d src`
* 그 결과, SqlType.java, Sqlmap.Java 등 두개의 바인딩용 자바 클래스와 팩토리 클래스가 생성된다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<sqlmap xmlns="<http://www.epril.com/sqlmap>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>"
   xsi:schemaLocation="<http://www.epril.com/sqlmap> <http://www.epril.com/sqlmap/sqlmap.xsd> ">
   <sql key="userAdd">insert into users(id, name, password, email, level, login, recommend) values(?,?,?,?,?,?,?)</sql>
   <sql key="userGet">select * from users where id = ?</sql>
   <sql key="userGetAll">select * from users order by id</sql>
   <sql key="userDeleteAll">delete from users</sql>
   <sql key="userGetCount">select count(*) from users</sql>
   <sql key="userUpdate">update users set name = ?, password = ?, email = ?, level = ?, login = ?, recommend = ? where id = ?</sql>
</sqlmap>
```

```java

@XmlAccessorType(XmlAccessType.FIELD)
@XmlType(
    name = "",
    propOrder = {"sql"}
)
@XmlRootElement(
    name = "sqlmap"
)
public class Sqlmap {
    @XmlElement(
        required = true
    )
    protected List<SqlType> sql;

    public Sqlmap() {
    }

    public List<SqlType> getSql() {
        if (this.sql == null) {
            this.sql = new ArrayList();
        }

        return this.sql;
    }
}

@XmlAccessorType(XmlAccessType.FIELD)
@XmlType(
    name = "sqlType",
    propOrder = {"value"}
)
public class SqlType {
    @XmlValue
    protected String value;
    @XmlAttribute(
        required = true
    )
    protected String key;

    public SqlType() {
    }

    public String getValue() {
        return this.value;
    }

    public void setValue(String value) {
        this.value = value;
    }

    public String getKey() {
        return this.key;
    }

    public void setKey(String value) {
        this.key = value;
    }
}
```

#### 2. 언 마샬링 테스트

* 언마샬링 : XML 문서 → 자바 오브젝트로 변환하는 것
* 마샬링 : 바인딩 오브젝트 → XML 문서로 변환하는 것
* JAXB API 사용을 위한 테스트 학습테스트 작성
* 아래는 `sqlmap.xml` 이 JAXB 언마샬링을 통해 매핑 오브젝트로 변환되는지 확인하는 테스트

```xml
<!-- sqlmap.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<sqlmap xmlns="<http://www.epril.com/sqlmap>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>"
   xsi:schemaLocation="<http://www.epril.com/sqlmap> ../../../../../sqlmap.xsd ">
   <sql key="add">insert</sql>
   <sql key="get">select</sql>
   <sql key="delete">delete</sql>
</sqlmap>

```

```java

public class JaxbTest {
    public JaxbTest() {
    }

    @Test
    public void readSqlmap() throws JAXBException, IOException {
        String contextPath = Sqlmap.class.getPackage().getName();
        JAXBContext context = JAXBContext.newInstance(contextPath);
        Unmarshaller unmarshaller = context.createUnmarshaller();
        Sqlmap sqlmap = (Sqlmap)unmarshaller.unmarshal(this.getClass().getResourceAsStream("sqlmap.xml"));

        List<SqlType> sqlList = sqlmap.getSql();
        Assert.assertThat(sqlList.size(), CoreMatchers.is(3));
        Assert.assertThat(((SqlType)sqlList.get(0)).getKey(), CoreMatchers.is("add"));
        Assert.assertThat(((SqlType)sqlList.get(0)).getValue(), CoreMatchers.is("insert"));
        Assert.assertThat(((SqlType)sqlList.get(1)).getKey(), CoreMatchers.is("get"));
        Assert.assertThat(((SqlType)sqlList.get(1)).getValue(), CoreMatchers.is("select"));
        Assert.assertThat(((SqlType)sqlList.get(2)).getKey(), CoreMatchers.is("delete"));
        Assert.assertThat(((SqlType)sqlList.get(2)).getValue(), CoreMatchers.is("delete"));
    }
}

```

#### 3. XML 파일 이용한 SQL 서비스

* SQL 맵 XML 문서 작성
* SqlService 구현하는 XmlSqlService 클래스 만들기
* 스프링 빈 설정 변경하기
* SQL 을 담은 XML 파일 경로와 이름을 외부에서 DI 받을 수 있도록 프로퍼티 설정
  * `<property name="sqlmapFile" value="sqlmap.xml" />`
* `loadSql()` 의 실행시점
  * 스프링 컨테이너가 빈을 생성한 뒤, 부가적인 작업을 수행할 수 있게 해주는 후처리기를 사용한다.
  * bean 태그 이용하는 방법 대신에 context 스키마의 annotation-config 태그를 이용하기로 한다.
    * `<context:annotation-config />` → `@PostConstruct` 사용
  * `@PostConstruct public void loadSql() {}` 처럼 초기화 작업을 수행할 메소드에 부여해주면,
    * 스프링은 XmlSqlService 클래스로 등록된 빈 오브젝트를 생성하고
    * DI 작업을 마친 뒤에,
    * @PostConstruct 가 붙은 메소드를 자동으로 실행해준다.
    * 생성자와는 다르게 프로퍼티까지 모두 준비된 후에 실행된다는 면에서 매우 유용하다.
* 참고) 스프링 컨테이너의 초기 작업 순서 정리
  * XML 빈 설정을 읽는다.
  * 빈 오브젝트를 생성한다.
  * 프로퍼티에 의존 오브젝트 또는 값을 주입한다.
  * 빈이나 태그로 등록된 후처리기를 동작시킨다.
    * 코드에 달린 애노테이션에 대한 부가작업을 진행시킨다.

```xml
<!-- sqlmap.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<sqlmap xmlns="<http://www.epril.com/sqlmap>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>"
   xsi:schemaLocation="<http://www.epril.com/sqlmap> <http://www.epril.com/sqlmap/sqlmap.xsd> ">
   <sql key="userAdd">insert into users(id, name, password, email, level, login, recommend) values(?,?,?,?,?,?,?)</sql>
   <sql key="userGet">select * from users where id = ?</sql>
   <sql key="userGetAll">select * from users order by id</sql>
   <sql key="userDeleteAll">delete from users</sql>
   <sql key="userGetCount">select count(*) from users</sql>
   <sql key="userUpdate">update users set name = ?, password = ?, email = ?, level = ?, login = ?, recommend = ? where id = ?</sql>
</sqlmap>

```

```java
public interface SqlService {
    String getSql(String var1) throws SqlRetrievalFailureException;
}

public class XmlSqlService implements SqlService {
    private Map<String, String> sqlMap = new HashMap();  // xml -> 오브젝트로 변환된 SQL 문들 담아둘 곳
    private String sqlmapFile;

    public XmlSqlService() {
    }

    public void setSqlmapFile(String sqlmapFile) {
        this.sqlmapFile = sqlmapFile;
    }

    @PostConstruct
    public void loadSql() {
        String contextPath = Sqlmap.class.getPackage().getName();

        try {
            JAXBContext context = JAXBContext.newInstance(contextPath);
            Unmarshaller unmarshaller = context.createUnmarshaller();
            InputStream is = UserDao.class.getResourceAsStream(this.sqlmapFile);

            Sqlmap sqlmap = (Sqlmap)unmarshaller.unmarshal(is);
            Iterator var7 = sqlmap.getSql().iterator();

            while(var7.hasNext()) {
                SqlType sql = (SqlType)var7.next();
                this.sqlMap.put(sql.getKey(), sql.getValue());
            }

        } catch (JAXBException var8) {
            throw new RuntimeException(var8);
        }
    }

    public String getSql(String key) throws SqlRetrievalFailureException {
        String sql = (String)this.sqlMap.get(key);
        if (sql == null) {
            throw new SqlRetrievalFailureException(key + "를 이용해서 SQL을 찾을 수 없습니다");
        } else {
            return sql;
        }
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="<http://www.springframework.org/schema/beans>"
	...
	xmlns:context="<http://www.springframework.org/schema/context>"
	...
	>
						
	<context:annotation-config />						

	...

	<!--  application components -->
	<bean id="userDao" class="springbook.user.dao.UserDaoJdbc">
		<property name="dataSource" ref="dataSource" />
		<property name="sqlService" ref="sqlService" />
	</bean>
	
	<bean id="sqlService" class="springbook.user.sqlservice.XmlSqlService">
		<property name="sqlmapFile" value="sqlmap.xml" />
	</bean>
	
... 

</beans>

```

## SqlService 인터페이스 책임에 따라 분리하기

* 현재의 XmlSqlService 도 잘 만들어졌지만, 문제점이 많이 남아있다.
  * SQL 을 가져오는 방식이 JAXB 라는 특정 기술에 고정되어있다.
  * XML 대신 다른 포멧의 파일에서 가져올 수 없다.
* 문제점은 분리 가능한 관심사가 하나의 인터페이스에 묶여있는 것이다.
* SqlService 는 사실 두 개의 책임으로 분리될 수 있다.
  * SQL 문을 외부 리소스로부터 읽어오는 것
  * SQL 문을 어플리케이션 내에 저장해뒀다가 요청이 오면 제공해주는 것
* 게다가 SQL 문을 어플리케이션 실행 중에도 수정하는 기능을 추가하는 책임도 갖을 수 있다.
* 그렇다면 아래와 같은 구조로 변경이 필요하다.
  * Dao
  * SqlService
    * SqlReader : Sql 읽기 요청 → SQL 리소스
    * SqlRegistry : Sql 등록, 조회 ← SqlUpdater 에 의해 런타임시에 SQL 변경 요청

### 생각해볼 점

* SqlReader 에서 읽어온 정보를 어떻게 SqlRegistry 로 전달할까?

### 방법 1. SqlService 에서 SqlReader 로부터 SQL 문을 읽어오고, SqlRegistry 로 다시 그 SQL 문을 전달하는 방법

* 문제점 : 이 둘 사이에서 데이터를 전달하기 위해 Map 형태의 데이터타입을 써야한다. 각각 정의하고 있는 데이터 타입에 따라서 번거로운 형변환이 일어날 가능성이 많다.

### 방법 2. SqlService 를 거치지 않고, SqlReader → SqlRegistry 로 바로 데이터를 전달한다. SqlRegistry 에 SqlReader 가 의존하는 구조를 만들어준다.

* SqlService → SqlReader : SQL 을 가지고 오도록 요청
* SqlReader → SqlRegistry : SQL 등록
* SqlService → SqlRegistry : SQL 검색
* 이때, XmlSqlService 가 SqlService, SqlRegistry, SqlReader 인터페이스를 한번에 구현한 클래스로서 역할하도록 일단 구성한다 → 건드리기 복잡한 구조의 경우, 일단 이렇게 해두면 나중에 개선하기 편하다.
* XmlSqlService 가 자기 자신을 참조하도록 xml 설정을 추가해준다.

```java
public interface SqlReader {
    void read(SqlRegistry var1);
}

public interface SqlRegistry {
    void registerSql(String var1, String var2);

    String findSql(String var1) throws SqlNotFoundException;
}

public class XmlSqlService implements SqlService, SqlRegistry, SqlReader {
    private SqlReader sqlReader;
    private SqlRegistry sqlRegistry;
    private Map<String, String> sqlMap = new HashMap();
    private String sqlmapFile;

    public XmlSqlService() {
    }

    public void setSqlReader(SqlReader sqlReader) {
        this.sqlReader = sqlReader;
    }

    public void setSqlRegistry(SqlRegistry sqlRegistry) {
        this.sqlRegistry = sqlRegistry;
    }

    @PostConstruct
    public void loadSql() {
        this.sqlReader.read(this.sqlRegistry);
    }

    public String getSql(String key) throws SqlRetrievalFailureException {
        try {
            return this.sqlRegistry.findSql(key);
        } catch (SqlNotFoundException var3) {
            throw new SqlRetrievalFailureException(var3);
        }
    }

    public String findSql(String key) throws SqlNotFoundException {
        String sql = (String)this.sqlMap.get(key);
        if (sql == null) {
            throw new SqlRetrievalFailureException(key + "를 이용해서 SQL을 찾을 수 없습니다");
        } else {
            return sql;
        }
    }

    public void registerSql(String key, String sql) {
        this.sqlMap.put(key, sql);
    }

    public void setSqlmapFile(String sqlmapFile) {
        this.sqlmapFile = sqlmapFile;
    }

    public void read(SqlRegistry sqlRegistry) {
        String contextPath = Sqlmap.class.getPackage().getName();

        try {
            JAXBContext context = JAXBContext.newInstance(contextPath);
            Unmarshaller unmarshaller = context.createUnmarshaller();
            InputStream is = UserDao.class.getResourceAsStream(this.sqlmapFile);
            Sqlmap sqlmap = (Sqlmap)unmarshaller.unmarshal(is);
            Iterator var8 = sqlmap.getSql().iterator();

            while(var8.hasNext()) {
                SqlType sql = (SqlType)var8.next();
                sqlRegistry.registerSql(sql.getKey(), sql.getValue());
            }

        } catch (JAXBException var9) {
            throw new RuntimeException(var9);
        }
    }
}
```

```xml
<!-- sql service -->
<bean id="sqlService" class="springbook.user.sqlservice.XmlSqlService">
   <property name="sqlReader" ref="sqlReader" />
   <property name="sqlRegistry" ref="sqlRegistry" />
</bean>

```

#### 구조 개선하기 - 인터페이스 분리

```java
public class BaseSqlService implements SqlService {
    private SqlReader sqlReader;
    private SqlRegistry sqlRegistry;

    public BaseSqlService() {
    }

    public void setSqlReader(SqlReader sqlReader) {
        this.sqlReader = sqlReader;
    }

    public void setSqlRegistry(SqlRegistry sqlRegistry) {
        this.sqlRegistry = sqlRegistry;
    }

    @PostConstruct
    public void loadSql() {
        this.sqlReader.read(this.sqlRegistry);
    }

    public String getSql(String key) throws SqlRetrievalFailureException {
        try {
            return this.sqlRegistry.findSql(key);
        } catch (SqlNotFoundException var3) {
            throw new SqlRetrievalFailureException(var3);
        }
    }
}

public class HashMapSqlRegistry implements SqlRegistry {
    private Map<String, String> sqlMap = new HashMap();

    public HashMapSqlRegistry() {
    }

    public String findSql(String key) throws SqlNotFoundException {
        String sql = (String)this.sqlMap.get(key);
        if (sql == null) {
            throw new SqlRetrievalFailureException(key + "를 이용해서 SQL을 찾을 수 없습니다");
        } else {
            return sql;
        }
    }

    public void registerSql(String key, String sql) {
        this.sqlMap.put(key, sql);
    }
}

public class JaxbXmlSqlReader implements SqlReader {
    private String sqlmapFile;

    public JaxbXmlSqlReader() {
    }

    public void setSqlmapFile(String sqlmapFile) {
        this.sqlmapFile = sqlmapFile;
    }

    public void read(SqlRegistry sqlRegistry) {
        String contextPath = Sqlmap.class.getPackage().getName();

        try {
            JAXBContext context = JAXBContext.newInstance(contextPath);
            Unmarshaller unmarshaller = context.createUnmarshaller();
            InputStream is = UserDao.class.getResourceAsStream(this.sqlmapFile);
            Sqlmap sqlmap = (Sqlmap)unmarshaller.unmarshal(is);
            Iterator var8 = sqlmap.getSql().iterator();

            while(var8.hasNext()) {
                SqlType sql = (SqlType)var8.next();
                sqlRegistry.registerSql(sql.getKey(), sql.getValue());
            }

        } catch (JAXBException var9) {
            throw new RuntimeException(var9);
        }
    }
}
```

```xml
<!-- sql service -->
<bean id="sqlService" class="springbook.user.sqlservice.BaseSqlService">
   <property name="sqlReader" ref="sqlReader" />
   <property name="sqlRegistry" ref="sqlRegistry" />
</bean>

<bean id="sqlReader" class="springbook.user.sqlservice.JaxbXmlSqlReader">
   <property name="sqlmapFile" value="sqlmap.xml" />
</bean>

<bean id="sqlRegistry" class="springbook.user.sqlservice.HashMapSqlRegistry">
</bean>

```

#### 한번 더 개선하기 - 디폴트 의존관계

* 확장 가능한 구조로 만들어두기는 했지만, 당분간은 JaxbXmlSqlReader 와 HashMapSqlRegistry 를 쓸 것 같다면, 디폴트 의존관계를 미리 설정해준 서비스를 이용하는 것도 좋은 방법이다.
* JaxbXmlSqlReader 에서 사용하는 파일명 역시 디폴트로 정의해두고 다른 파일명이 주입되지 않았다면, 미리 생성해둔 파일명을 쓰도록 처리함으로써, DefaulSqlService 에서 따로 주입시켜주지 않도록 한다.
* DI 를 사용한다고 해서 항상 모든 프로퍼티 값을 설정에 넣고 모든 의존 오브젝트를 빈으로 일일이 지정할 필요는 없다. 자주 사용되는 의존 오브젝트는 미리 지정한 디폴트 의존 오브젝트를 설정 없이도 사용할 수 있게 만드는 것도 좋은 방법이다.
* 이 방법의 단점
  * 설정을 통해 다른 구현 오브젝트를 사용하게 해도 DefaultSqlService 의 생성자에서 디폴트 의존 오브젝트를 다 만들어버린 뒤, 프로퍼티로 설정한 빈 오브젝트로 대체된다는 점. 사용하지 않는 오브젝트가 만들어진다는 찝찝함이 있다.
* 해결방법
  * `@PostConstruct` 초기화 메소드를 이용해 프로퍼티가 설정됐는지 확인하고, 없는 경우에만 디폴트 오브젝트를 만드는 방법을 사용하면 된다.

```java
public class DefaultSqlService extends BaseSqlService {
    public DefaultSqlService() {
        this.setSqlReader(new JaxbXmlSqlReader());
        this.setSqlRegistry(new HashMapSqlRegistry());
    }
}

public class JaxbXmlSqlReader implements SqlReader {
    private final String DEFAULT_SQLMAP_FILE = "sqlmap.xml";
    private String sqlmapFile = DEFAULT_SQLMAP_FILE;

    public JaxbXmlSqlReader() {
    }

    public void setSqlmapFile(String sqlmapFile) {
        this.sqlmapFile = sqlmapFile;
    }
}
```

```xml
<!-- sql service -->
<bean id="sqlService" class="springbook.user.sqlservice.DefaultSqlService">
</bean>
```

## JaxbXmlSqlService 개선하기

* 현재 JaxbXmlSqlService 에는 두가지 아쉬운 점이 있다.
  * JAXB 이외에도 다양한 XML - 자바 오브젝트 매핑 기술이 있는데, JAXB 기술에 한정되어있다.
  * XML 파일을 다양한 소스에서 가져올 수 있게 하고 싶다. 현재는 UserDao 와 같은 클래스 패스 내에서만 XML 을 읽어올 수 있는 상황이다. 임의의 클래스 패스타 파일시스템 상의 절대위치, 혹은 HTTP 프로토콜을 통해 원격으로 가져오도록 확장할 수는 없을까
* 개선하고 싶은 부분이 JAXB 기술에만 한정되지 않고 다양한 XML-자바 오브젝트 매핑 기술을 유연하게 바꿀 수 있는 구조라면 `서비스 추상화` 방법을 떠올릴 수 있다.

### OXM (Object-Xml Mapping) 프레임워크

* JAXB(Java Architecture for XML Binding) 이외에도 XML 과 오브젝트를 매핑해주는 기술은 여러개가 있다.
  * Castor XML : 설정파일이 필요없는 인트로스펙션 모드를 지원. 매우 간결하고 가벼운 바인딩 프레임워크
  * JiBX : 뛰어난 퍼포먼스의 XML 기술
  * XmlBeans : 아파치 XML 프로젝트의 하나. XML의 정보셋을 효과적으로 제공
  * Xstream : 관례를 이용해서 설정이 필요없는 바인딩을 지원함
* 이들을 모두 OXM 프레임워크라고 할 수 있는데, 사용 목적이 동일한만큼, 유사한 기능과 API 제공해준다.
* 이들은 유사하지만 기술들만 다른 경우로, 서비스 추상화 기법을 통해 개선할 수 있을 것으로 기대할 수 있다.
* 스프링이 이러한 추상화 기법을 제공하는데, OXM 추상화 서비스 인터페이스를 통해서 서로 다른 기술들을 모두 하나의 코드로 이용할 수 있다.

#### JAXB 구현 테스트

```xml
<!-- OxmTest-context.xml -->
 <bean id="jaxbUnmarshaller" class="org.springframework.oxm.jaxb.Jaxb2Marshaller">
   <property name="contextPath" value="springbook.user.sqlservice.jaxb" />
 </bean>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration
public class OxmTest {
    @Autowired
    Unmarshaller unmarshaller;

    public OxmTest() {
    }

    @Test
    public void unmarshallSqlMap() throws XmlMappingException, IOException {
        Source xmlSource = new StreamSource(this.getClass().getResourceAsStream("sqlmap.xml"));
        Sqlmap sqlmap = (Sqlmap)this.unmarshaller.unmarshal(xmlSource);

        List<SqlType> sqlList = sqlmap.getSql();

        Assert.assertThat(sqlList.size(), CoreMatchers.is(3));
        Assert.assertThat(((SqlType)sqlList.get(0)).getKey(), CoreMatchers.is("add"));
        Assert.assertThat(((SqlType)sqlList.get(0)).getValue(), CoreMatchers.is("insert"));
        Assert.assertThat(((SqlType)sqlList.get(1)).getKey(), CoreMatchers.is("get"));
        Assert.assertThat(((SqlType)sqlList.get(1)).getValue(), CoreMatchers.is("select"));
        Assert.assertThat(((SqlType)sqlList.get(2)).getKey(), CoreMatchers.is("delete"));
        Assert.assertThat(((SqlType)sqlList.get(2)).getValue(), CoreMatchers.is("delete"));
    }
}
```

#### Castor 구현 테스트

* 기존의 OxmTest 의 코드는 전혀 바꾸지 않고, xml 설정만 변경하여 완벽하게 같은 테스트를 통과한다!
* OXM 추상화 서비스 인터페이스를 이용한다면, 테스트용 Unmarchaller 로 대체하기도 쉽다. 아직 매핑 파일이 준비되지 않았거나 OXM 기술을 못정한 경우에도 일단 테스트 용으로 Unmarchaller 인터페이스를 구현해서 미리 준비된 오브젝트를 돌려주도록 할 수도 있다. Mockito 로 만들어서 대체해줄수도 있고!

```xml
<!-- mapping.xml -->
<?xml version="1.0"?>
<!DOCTYPE mapping PUBLIC "-//EXOLAB/Castor Mapping DTD Version 1.0//EN" "<http://castor.org/mapping.dtd>">
<mapping>
    <class name="springbook.user.sqlservice.jaxb.Sqlmap">
        <map-to xml="sqlmap" />
        <field name="sql"
               type="springbook.user.sqlservice.jaxb.SqlType"
               required="true" collection="arraylist">
            <bind-xml name="sql" node="element" />
        </field>
    </class>
    <class name="springbook.user.sqlservice.jaxb.SqlType">
        <map-to xml="sql" />
        <field name="key" type="string" required="true">
            <bind-xml name="key" node="attribute" />
        </field>
        <field name="value" type="string" required="true">
            <bind-xml node="text" />
        </field>
    </class>
</mapping>

<!-- OxmTest-context.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="<http://www.springframework.org/schema/beans>"
	xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>"
	xsi:schemaLocation="<http://www.springframework.org/schema/beans> <http://www.springframework.org/schema/beans/spring-beans-2.0.xsd>">

	<bean id="unmarshaller" class="org.springframework.oxm.castor.CastorMarshaller">
		<property name="mappingLocation" value="springbook/learningtest/spring/oxm/mapping.xml" />
	</bean>

</beans>
```

#### OXM 서비스 추상화 적용하기

* OxmlSqlService 가 OxmlSqlReader 를 내부 멤버 클래스로 소유하고 있는 강한 결합 구조를 만들도록 한다.
  * 편리하고 유연한 구조를 위해서는 클래스를 분리하고, 빈을 따로 등록해 DI 할 수 있는 구조로 가져가는 것이 좋지만, 빈의 개수가 너무 많아지고 비슷한 구조의 DI 가 반복되면 불편해진다.
  * 그래서 하나의 빈 설정만으로도 SqlService 와 SqlReader 가 필요한 프로퍼티 설정이 모두 가능하도록 결합도를 강하게 만들었다.
* 아래처럼 빈으로는 OxmlSqlService 하나만을 등록하고, 실제로는 Reader에 필요한 프로퍼티까지 주입받고 있음을 알 수 있다.

```xml
<!-- sql service -->
<bean id="sqlService" class="springbook.user.sqlservice.OxmSqlService">
   <property name="unmarshaller" ref="unmarshaller" />
</bean>

<bean id="unmarshaller" class="org.springframework.oxm.jaxb.Jaxb2Marshaller">
   <property name="contextPath" value="springbook.user.sqlservice.jaxb" />
</bean>
```

```java
public class OxmSqlService implements SqlService {
    private final BaseSqlService baseSqlService = new BaseSqlService();  //내부 멤버
    private final OxmSqlService.OxmSqlReader oxmSqlReader = new OxmSqlService.OxmSqlReader((OxmSqlService.OxmSqlReader)null);
    private SqlRegistry sqlRegistry = new HashMapSqlRegistry();  // default 설정

    public OxmSqlService() {
    }

    public void setSqlRegistry(SqlRegistry sqlRegistry) {
        this.sqlRegistry = sqlRegistry;
    }

    public void setUnmarshaller(Unmarshaller unmarshaller) {
        this.oxmSqlReader.setUnmarshaller(unmarshaller);
    }

		//property 가 설정된다면, 다른 파일로 교체될 것이다. 이름이 같다면 디폴트로 설정된다. 
    public void setSqlmapFile(String sqlmapFile) {
        this.oxmSqlReader.setSqlmapFile(sqlmapFile);
    }

    @PostConstruct
    public void loadSql() {
        this.baseSqlService.setSqlReader(this.oxmSqlReader);
        this.baseSqlService.setSqlRegistry(this.sqlRegistry);
        this.baseSqlService.loadSql();
    }

    public String getSql(String key) throws SqlRetrievalFailureException {
        return this.baseSqlService.getSql(key);
    }

    private class OxmSqlReader implements SqlReader {
        private Unmarshaller unmarshaller;
        private static final StringDEFAULT_SQLMAP_FILE= "sqlmap.xml";  //디폴트 설정파일
        private String sqlmapFile;

        private OxmSqlReader() {
            this.sqlmapFile = "sqlmap.xml";
        }

        public void setUnmarshaller(Unmarshaller unmarshaller) {
            this.unmarshaller = unmarshaller;
        }

        public void setSqlmapFile(String sqlmapFile) {
            this.sqlmapFile = sqlmapFile;
        }

        public void read(SqlRegistry sqlRegistry) {
            try {
                Source source = new StreamSource(UserDao.class.getResourceAsStream(this.sqlmapFile));
                Sqlmap sqlmap = (Sqlmap)this.unmarshaller.unmarshal(source);
                Iterator var5 = sqlmap.getSql().iterator();

                while(var5.hasNext()) {
                    SqlType sql = (SqlType)var5.next();
                    sqlRegistry.registerSql(sql.getKey(), sql.getValue());
                }

            } catch (IOException var6) {
                throw new IllegalArgumentException(this.sqlmapFile + "을 가져올 수 없습니다", var6);
            }
        }
    }
}

```

{% hint style="info" %}
📸 잠시 숨참고 여기까지 정리해보자면
{% endhint %}

* 지금까지 행한 모든 것들은 DAO 에서 SQL 문을 빼내어 데이터베이스의 테이블 구조나 필드 변경 등에 영향을 받아 코드를 수정하지 않기 위함이다.
* XML 파일에 SQL 문들을 모아서 관리하고 이를 자바 오브젝트로 매핑시켜서 사용하는 여러가지 방법(OXM)을 사용해왔는데, 그 과정에서 다양한 설계와 구조에 대한 고민이 있었고 최적화의 상태로 변환시켜갔다.
* 스프링이 제공해주는 OXM 서비스 추상화 방법을 이용하여 이제 XML-자바오브젝트 매핑 기술에 상관없이 코드 변경이 일어나지 않는 구조가 완성되었다.

#### OXM 서비스 추상화 개선하기

* 지금 구조로 보면 한 가지 문제점이 존재한다.
  * SqlService 의 구현체인 OxmSqlService 와 BaseSqlService 에 loadSql() 과 getSql() 등의 메소드 작업에 대한 코드가 거의 똑같다는 것이다.
* 해결방법
  * OxmlSqlService → BaseSqlService 로 작업을 위임한다.
  * 아래와 같이 코드를 수정하면 중복코드도 사라지게 된다.

```java
public class OxmSqlService implements SqlService {
    private final BaseSqlService baseSqlService = new BaseSqlService();
    private final OxmSqlService.OxmSqlReader oxmSqlReader = new OxmSqlService.OxmSqlReader((OxmSqlService.OxmSqlReader)null);

		@PostConstruct
    public void loadSql() {
        this.baseSqlService.setSqlReader(this.oxmSqlReader);
        this.baseSqlService.setSqlRegistry(this.sqlRegistry);
        this.baseSqlService.loadSql();
    }

		public String getSql(String key) throws SqlRetrievalFailureException {
        return this.baseSqlService.getSql(key);
    }
}
```

{% hint style="info" %}
💡 이해 안되는 점
{% endhint %}

* BaseSqlService 는 코드 개선 이전에 구현된 레거시이고, 이제부터는 OxmSqlServie 만 사용하면 되지 않나. XmlSqlService 는 그대로 두고 사용하면서, 왜 BaseSqlService 는 코드가 중복된다는 이유로 이를 해결하려고 고민하는지 이해가 되지 않았다.

## SQL이 담긴 XML 파일 소스 다양화하기

### 리소스 추상화

* 같은 목적이지만 사용법이 다른 기술들이므로 리소스를 가져오는 것 자체도 추상화할 수 있지 않을까? → 리소스 추상화의 필요성
* 스프링은 자바에 존재하는 일관성 없는 리소스 접근 API 를 추사오하해서 Resource 추상화 인터페이스를 정의한다.
* 어플리케이션 컨텍스트가 사용할 설정정보 파일을 지정하는 것부터 스프링의 거의 모든 API 는 외부의 리소스 정보가 필요할 떄, 항상 이 Resource 추상화를 이용한다.
* 이 Resource 는 빈 타입이 아니라 값으로 간주되기 때문에 추상화를 적용하는데 고민이 필요하다.
* 스프링에서는 이를 해결하기 위해 접두어를 이용한 Resource 오브젝트를 선언하는 방법이 있다. 그리고 ResourceLoader 를 통해 문자열로 정의된 리소스를 실제 Resource 타입 오브젝트로 변환해준다.
* ResourceLoader 는 접두어(file:, classpath:, http: 등)을 이용해서 리소스 로더의 종류와 상관없이 접두어가 의미하는 위치와 방법을 이용하여 리소스를 읽어온다. 만약에 접두어가 없다면 리소스 로더의 구현방식에 따라 리소스를 가져오는 방식이 달라진다.
* 가장 대표적인 ResourceLoader 는 바로 스프링의 어플리케이션 컨텍스트이다. ApplicationContext 는 ResourceLoader 인터페이스를 상속하고 있다. 따라서 어플리케이션 컨텍스트는 리소스 로더이기도 하다.
* 스프링 컨테이너가 리소스 로더를 사용하는 목적들 예시
  * 어플리케이션 컨텍스트가 사용할 스프링 설정정보가 담긴 XML 파일
  * 컨텍스트 외부에서 읽어오는 모든 정보들
  * 빈의 프로퍼티 값을 변환할 때에도 value 문자열로 정의된 프로퍼티를 본인 스스로 리소스 로더로써 Resource 오브젝트로 변환하여 프로퍼티에 주입시킨다.

### Resource 이용해서 XML 파일 가져오기

1. string type 으로 정의되었던 sqlMapFile 모두 Resource 타입으로 변경하기
2. 이름도 sqlMap 으로 변경
3. getInputStream() 을 이용하여 리소스 소스 상관없이 스트림으로 가져오기
4. StreamSource 클래스 이용하여 OXM 언마샬러가 필요로 하는 Source 타입으로 만들어주기

```java
public classOxmSqlServiceimplementsSqlService {

		public void setSqlmap(Resource sqlmap) {
        this.oxmSqlReader.setSqlmap(sqlmap);
    }

		...

		private class OxmSqlReader implements SqlReader {
        private Unmarshaller unmarshaller;
        private Resource sqlmap;

        private OxmSqlReader() {
						//default 설정은 똑같이 해주지만, Resource 타입으로 변경
            this.sqlmap = new ClassPathResource("sqlmap.xml", UserDao.class);
        }

        public void setUnmarshaller(Unmarshaller unmarshaller) {
            this.unmarshaller = unmarshaller;
        }

        public void setSqlmap(Resource sqlmap) {
            this.sqlmap = sqlmap;
        }

        public void read(SqlRegistry sqlRegistry) {
            try {
                Source source = new StreamSource(this.sqlmap.getInputStream());
                Sqlmap sqlmap = (Sqlmap)this.unmarshaller.unmarshal(source);
                Iterator var5 = sqlmap.getSql().iterator();

                while(var5.hasNext()) {
                    SqlType sql = (SqlType)var5.next();
                    sqlRegistry.registerSql(sql.getKey(), sql.getValue());
                }

            } catch (IOException var6) {
                throw new IllegalArgumentException(this.sqlmap.getFilename() + "을 가져올 수 없습니다", var6);
            }
        }
    }
}
```

```xml
<!-- sql service -->
<bean id="sqlService" class="springbook.user.sqlservice.OxmSqlService">
   <property name="unmarshaller" ref="unmarshaller" />
   <property name="sqlmap" value="classpath:/springbook/user/dao/sqlmap.xml" />
</bean>

```

## 운영중인 앱의 SQL 을 동적으로 변경해야한다면

* 원칙적으로는 발생하면 안되는 일이지만, 운영 중인 서비스에 심각한 SQL 오류가 있거나 해서 운영중인 서비스의 SQL 을 급히 변경해야한다면, 어떻게 해야할까?
* 지금까지 방법들은 모두 스프링 어플리케이션이 시작되면서 초기화 될 때, 리소스로부터 SQL 정보를 읽어와서 아예 앱 메모리 내부에 두고 그대로 사용하는 방법이었다. 즉, 어플리케이션이 일단 시작되었으면, SQL 매핑 정보를 변경한다고 해서 그게 적용되지는 않았다. 변경된 SQL 문을 적용시키려면 앱을 재시작해서 SqlService 구현 빈을 다시 초기화하는 방법밖에는 없었다.

### DI 와 기능의 확장

* 스프링에서 XML 설정정보를 통해 DI 를 해주는 것은 어렵지 않다. 하지만 이것은 진정으로 DI 를 사용하고 있다고는 할 수 없다.
* DI 의 가치를 제대로 누리려면, 유연하고 확장 가능한 좋은 오브젝트 설계가 함께 이루어져야한다.
* 객체지향을 잘 하는 방법 중 하나는 바로 DI 를 의식하면서 설계하는 방식이다.
  * DI 를 적용하려면 커다란 오브젝트 하나가 있어서는 안된다. 최소한 두 개 이상의 의존관계를 가지고 서로 협력해서 일하는 오브젝트가 필요하다.
  * 그렇게 되면 적절한 책임에 따라 오브젝트를 분리하게 된다.

### DI 와 인터페이스 프로그래밍

#### 인터페이스를 쓰는 이유 1. 다형성을 얻기 위해서

* 하나의 인터페이스를 통해 여러개의 구현을 바꿔가면서 사용할 수 있게 하기 위해서이다.
* 사실 꼭 DI 의 목적이 오직 다형성을 편리하게 적용하기 위해서라면 굳이 인터페이스를 쓰지 않아도 된다. 귀찮더라도 상속 방식으로 구현을 확장할 수 있다.

#### 인터페이스를 쓰는 이유 2. 인터페이스 분리 원칙을 통해 클라이언트와 의존 오브젝트 사이의 관계를 명확하게 해줄 수 있기 때문에

* A와 B가 인터페이스로 연결되어 있다는 것은 A가 B를 바라볼 때, 해당 인터페이스라는 창을 통해서 본다는 것을 의미한다. 따라서 A에게 B는 그냥 B1 인터페이스를 구현한 임의의 오브젝트일 뿐이다. B가 C, D, E, F 등으로 바뀌어도 A에게는 DI 가 가능해진다.
* 하나의 오브젝트는 여러개의 인터페이스를 구현할 수 있다. 하나의 오브젝트를 바라보는 창이 여러가지 일 수 있다는 뜻이다.

#### 인터페이스 분리 원칙

* 목적과 관심이 각기 다른 클라이언트가 있다면 인터페이스를 통해 적절하게 분리해준다.
* 웬만하면 DI 는 인터페이스를 통해 한다고 생각해도 좋다.

#### 인터페이스 상속

* 인터페이스를 꼭 여러 개 만들 필요는 없다. 대신 기존 인터페이스를 상속을 통해 확장하는 방법도 있다.
* 예를 들어 지금 SqlRegistry 에 SQL 수정 기능을 추가한다고 생각해보자. 그렇게 되면 SqlRegistry 에 의존하고 있던 기존의 BaseSqlService 는 굳이 필요없는 기능에 노출되게 된다. 이 경우는 아래와 같이 해결할 수 있다.
* 오브젝트간 관계
  * SqlRegistry
    * UpdatableSqlRegistry : SqlRegistry 상속한 interface
    * MyUpdatableSqlRegistry : UpdatableSqlRegistry 상속한 구현 클래스
  * BaseSqlService : SqlRegistry 인터페이스 타입을 주입받는 클래스
  * SqlAdminService : UpdatableSqlRegistry 타입을 주입받는 클래스
* 위와 같이 인터페이스를 상속하는 방식으로 진행하게 되면, BaseSqlService와 SqlAdminService는 모두 MyUpdatableSqlRegistry 클래스만 DI 받아도 각각 받는 인터페이스 타입에 따라 접근할 수 있는 메소드가 달라지게 되며, 원하는 기능은 접근할 수 있는 상태가 된다.

```java
public interface UpdatableSqlRegistry extends SqlRegistry {
    void updateSql(String var1, String var2) throws SqlUpdateFailureException;

    void updateSql(Map<String, String> var1) throws SqlUpdateFailureException;
}
```

```xml
<bean id="sqlService" class="springbook.user.sqlservice.BaseSqlService">
   <property name="unmarshaller" ref="unmarshaller" />
   <property name="sqlRegistry" ref="sqlRegistry" />
</bean>

<bean id="sqlRegistry" class="springbook.user.sqlservice.MyUpdatableSqlRegistry"/>

<bean id="sqlAdminService" class="springbook.user.sqlservice.SqlAdminService">
   <property name="updatableSqlRegistry" ref="sqlRegistry" />
</bean>

```

## DI를 이용해 다양한 구현 방법 적용하기

* 운영 중인 시스템에서 정보를 실시간으로 변경하는 작업을 만들 때 가장 먼저 고려해야할 점은 동시성 문제이다.

### 1. ConcurrentHashMap 이용하여 수정가능한 SQL 레지스트리 만들기

* 현재 사용중인 HashMap 의 경우, 멀티스레드 환경에서 동시에 수정을 시도하거나 동시에 요청하는 경우, 예상치 못한 문제가 발생할 수 있다.
* 이를 해결하기 위해 Collections.synchronizedMap() 등을 이용해서 외부에서 동기화를 해줘야하지만, DAO 처럼 요청이 많은 고성능 서비스에서는 이렇게하면 성능상 이슈가 발생하게 된다.
* 해결방법은 ConcurrentHashMap 을 이용하는 것이다. 이는 데이터 조작시 전체 데이터에 대해 락을 걸지 않고 조회는 락을 아예 사용하지 않는다. 어느정도 안전하면서 성능도 괜찮은 동기화된 HashMap 인 셈이다.

#### ConcurrentHashMap 사용법 테스트

```java
public class ConcurrentHashMapSqlRegistryTest {
    UpdatableSqlRegistry sqlRegistry;

    public ConcurrentHashMapSqlRegistryTest() {
    }

    @Before
    public void setUp() {
        this.sqlRegistry = new ConcurrentHashMapSqlRegistry();
        this.sqlRegistry.registerSql("KEY1", "SQL1");
        this.sqlRegistry.registerSql("KEY2", "SQL2");
        this.sqlRegistry.registerSql("KEY3", "SQL3");
    }

    @Test
    public void find() {
        this.checkFindResult("SQL1", "SQL2", "SQL3");
    }

    private void checkFindResult(String expected1, String expected2, String expected3) {
        Assert.assertThat(this.sqlRegistry.findSql("KEY1"), CoreMatchers.is(expected1));
        Assert.assertThat(this.sqlRegistry.findSql("KEY2"), CoreMatchers.is(expected2));
        Assert.assertThat(this.sqlRegistry.findSql("KEY3"), CoreMatchers.is(expected3));
    }

    @Test(
        expected = SqlNotFoundException.class
    )
    public void unknownKey() {
        this.sqlRegistry.findSql("SQL9999!@#$");
    }

    @Test
    public void updateSingle() {
        this.sqlRegistry.updateSql("KEY2", "Modified2");
        this.checkFindResult("SQL1", "Modified2", "SQL3");
    }

    @Test
    public void updateMulti() {
        Map<String, String> sqlmap = new HashMap();
        sqlmap.put("KEY1", "Modified1");
        sqlmap.put("KEY3", "Modified3");
        this.sqlRegistry.updateSql(sqlmap);
        this.checkFindResult("Modified1", "SQL2", "Modified3");
    }

    @Test(
        expected = SqlUpdateFailureException.class
    )
    public void updateWithNotExistingKey() {
        this.sqlRegistry.updateSql("SQL9999!@#$", "Modified2");
    }
}
```

#### 수정 기능을 추가한 SQL 레지스트리 구현

```java
public interface UpdatableSqlRegistry extends SqlRegistry {
    void updateSql(String var1, String var2) throws SqlUpdateFailureException;

    void updateSql(Map<String, String> var1) throws SqlUpdateFailureException;
}


public class ConcurrentHashMapSqlRegistry implements UpdatableSqlRegistry {
    private Map<String, String> sqlMap = new ConcurrentHashMap();  
    //동시성 문제 해결을 위한 ConcurrentHashMap 타입 사용 

    public ConcurrentHashMapSqlRegistry() {
    }

    public String findSql(String key) throws SqlNotFoundException {
        String sql = (String)this.sqlMap.get(key);
        if (sql == null) {
            throw new SqlNotFoundException(key + "를 이용해서 SQL을 찾을 수 없습니다");
        } else {
            return sql;
        }
    }

    public void registerSql(String key, String sql) {
        this.sqlMap.put(key, sql);
    }

    public void updateSql(String key, String sql) throws SqlUpdateFailureException {
        if (this.sqlMap.get(key) == null) {
            throw new SqlUpdateFailureException(key + "에 해당하는 SQL을 찾을 수 없습니다");
        } else {
            this.sqlMap.put(key, sql);
        }
    }

    public void updateSql(Map<String, String> sqlmap) throws SqlUpdateFailureException {
        Iterator var3 = sqlmap.entrySet().iterator();

        while(var3.hasNext()) {
            Entry<String, String> entry = (Entry)var3.next();
            this.updateSql((String)entry.getKey(), (String)entry.getValue());
        }

    }
}

```

```xml
<!-- sql service -->
<bean id="sqlService" class="springbook.user.sqlservice.OxmSqlService">
   <property name="unmarshaller" ref="unmarshaller" /> 
   <property name="sqlRegistry" ref="sqlRegistry" />
</bean>

<bean id="sqlRegistry" class="springbook.user.sqlservice.updatable.ConcurrentHashMapSqlRegistry">
</bean>
   
```



### 2. ConcurrentHashMap 대신에 내장형 DB 사용하여 SQL 저장하고 수정하기&#x20;

* concurrentHashMap의 한계
  * 성능상 그리 나쁘지는 않지만 데이터 양이 많아지고 잦은 조회와 변경이 일어나는 환경에는 한계가 있다.&#x20;
* 해결방법
  * 인덱스를 이용한 최적화 검색 + 동시에 많은 요청을 처리 + 안정적인 변경 작업이 가능한 기술
  * 바로 데이터베이스를 이용하는 것이다.&#x20;
* 그러나 자칫하면 SQL 저장하자고 DB 구축을 엄청나게 하면 배보다 배꼽이 더 커질 수 있음. 내장형 DB 로 깔삼하게 구축하는 것이 이득이다.&#x20;



#### 내장형 데이터베이스의 특징

* &#x20;어플리케이션과 함께 시작되고 종료됨
* 메모리에 저장되기 때문에 IO 로 발생하는 부하가 적어서 성능이 뛰어나다&#x20;
* Map 같은 컬랙션보다 매우 효과적인 방법으로 등록, 수정, 삭제, 검색이 가능하다.&#x20;
* 최적화된 락킹, 격리수준, 트랜잭션을 적용하는 것도 가능하다.&#x20;
* 검색, 통계 등 데이터 조작이 많이 필요한 경우, 관계형 DB와 SQL 문이 가장 좋은 방법이다.&#x20;
* 종류&#x20;
  * Derby, HSQL, H2&#x20;
  * 모두 JDBC 드라이버를 제공하고 표준 DB 와 호환되는 기능을 제공한다.&#x20;
* 스프링에서는 내장형 DB 를 초기화하는 작업을 지원하는 편리한 내장형 DB 빌더를 제공한다.&#x20;
* JDBC 접속 URL 을 이용하여 연결을 시도하면 JDBC 드라이버 내에서 이를 생성해준다.&#x20;
* 어플리케이션 내에서 직접 종료할 수 있어야 한다.&#x20;



#### 내장형 데이터베이스 학습테스트

```sql
CREATE TABLE SQLMAP (
   KEY_ VARCHAR(100) PRIMARY KEY,
   SQL_ VARCHAR(100) NOT NULL
);
```

```sql
INSERT INTO SQLMAP(KEY_, SQL_) values('KEY1', 'SQL1');
INSERT INTO SQLMAP(KEY_, SQL_) values('KEY2', 'SQL2');
```

```java
public class EmbeddedDbTest {
    EmbeddedDatabase db;
    SimpleJdbcTemplate template;

    public EmbeddedDbTest() {
    }

    @Before
    public void setUp() {
        this.db = (new EmbeddedDatabaseBuilder())
            .setType(EmbeddedDatabaseType.HSQL)  //H2, Derby 등 데이터베이스에 맞게 교체 
            .addScript("classpath:/springbook/learningtest/spring/embeddeddb/schema.sql")
            .addScript("classpath:/springbook/learningtest/spring/embeddeddb/data.sql")
            .build();
        this.template = new SimpleJdbcTemplate(this.db);
    }

    @After
    public void tearDown() {
        this.db.shutdown();
    }

    @Test
    public void initData() {
        Assert.assertThat(this.template.queryForInt("select count(*) from sqlmap", new Object[0]), CoreMatchers.is(2));

        List<Map<String, Object>> list = this.template.queryForList("select * from sqlmap order by key_", new Object[0]);
        Assert.assertThat((String)((Map)list.get(0)).get("key_"), CoreMatchers.is("KEY1"));
        Assert.assertThat((String)((Map)list.get(0)).get("sql_"), CoreMatchers.is("SQL1"));
        Assert.assertThat((String)((Map)list.get(1)).get("key_"), CoreMatchers.is("KEY2"));
        Assert.assertThat((String)((Map)list.get(1)).get("sql_"), CoreMatchers.is("SQL2"));
    }

    @Test
    public void insert() {
        this.template.update("insert into sqlmap(key_, sql_) values(?,?)", new Object[]{"KEY3", "SQL3"});
        Assert.assertThat(this.template.queryForInt("select count(*) from sqlmap", new Object[0]), CoreMatchers.is(3));
    }
}
```



#### 내장형 데이터베이스를 이용하는 Registry 신규 생성 및 테스트

* 스프링에서 내장형 데이터베이스를 만들기 위해서는 EmbeddedDatabaseBuilder 를 사용해야한다.&#x20;
  * 그런데 이는 직접 빈으로 등록한다고 사용할 수 있는 것이 아니다.&#x20;
  * 직접 적절한 메소드를 호출해주는 초기화 코드가 필요하다.
* 이런 초기화 코드는 팩토리 빈으로 만드는 것이 좋다. EmbeddedDatabaseBuilder 오브젝트는 한 번 초기화를 거쳐서 내장형 DB 를 기동하고 여기에 접근할 수 있는 EmbeddDatabase 를 만들어주면 그 이후로는 사용할 일이 없기 때문이다. &#x20;
* 스프링에는 팩토리 빈을 대신 만들어주는 전용 태그가 있다.&#x20;
  * 아래와 같이 설정하면 embeddedDatabase 아이디를 가진 빈이 등록되며, type 은 EmbeddedDatabase 이다.&#x20;
  * 이제 이 빈 오브젝트를 통해서 내장형 데이터베이스를 마음껏 사용할 수 있다. &#x20;

```xml
<!-- sql service -->
<bean id="sqlService" class="springbook.user.sqlservice.OxmSqlService">
   <property name="unmarshaller" ref="unmarshaller" /> 
   <property name="sqlRegistry" ref="sqlRegistry" />
</bean>

<bean id="sqlRegistry" class="springbook.user.sqlservice.updatable.EmbeddedDbSqlRegistry">
   <property name="dataSource" ref="embeddedDatabase" />
</bean>

<jdbc:embedded-database id="embeddedDatabase" type="HSQL">
   <jdbc:script location="classpath:springbook/user/sqlservice/updatable/sqlRegistrySchema.sql"/>
</jdbc:embedded-database>
```

```sql
CREATE TABLE SQLMAP (   
    KEY_ VARCHAR(100) PRIMARY KEY, 
    SQL_ VARCHAR(100) NOT NULL
);
```

```java
public class EmbeddedDbSqlRegistry implements UpdatableSqlRegistry {
    SimpleJdbcTemplate jdbc;

    public EmbeddedDbSqlRegistry() {
    }

    public void setDataSource(DataSource dataSource) {
        this.jdbc = new SimpleJdbcTemplate(dataSource);
    }

    public void registerSql(String key, String sql) {
        this.jdbc.update("insert into sqlmap(key_, sql_) values(?,?)", new Object[]{key, sql});
    }

    public String findSql(String key) throws SqlNotFoundException {
        try {
            return (String)this.jdbc.queryForObject("select sql_ from sqlmap where key_ = ?", String.class, new Object[]{key});
        } catch (EmptyResultDataAccessException var3) {
            throw new SqlNotFoundException(key + "에 해당하는 SQL을 찾을 수 없습니다", var3);
        }
    }

    public void updateSql(String key, String sql) throws SqlUpdateFailureException {
        int affected = this.jdbc.update("update sqlmap set sql_ = ? where key_ = ?", new Object[]{sql, key});
        if (affected == 0) {
            throw new SqlUpdateFailureException(key + "에 해당하는 SQL을 찾을 수 없습니다");
        }
    }

    public void updateSql(Map<String, String> sqlmap) throws SqlUpdateFailureException {
        Iterator var3 = sqlmap.entrySet().iterator();

        while(var3.hasNext()) {
            Entry<String, String> entry = (Entry)var3.next();
            this.updateSql((String)entry.getKey(), (String)entry.getValue());
        }

    }
}
```

* 내장형 데이터베이스를 이용하기 위해서 dataSource 를 DI 받았다.&#x20;
  * 위의 xml 설정에서 정의하기를, jdbc 스키마의 태그를 이용해서 내장형 데이터베이스의 팩토리 빈을 생성했을 때는  EmbeddedDatabase 타입이었다. 그런데 주입받을 때에는 DataSource 인터페이스 타입으로 주입을 받고 있다. 그 이유는 무엇일까?&#x20;
  * 인터페이스 분리 원칙을 지키기 위해서이다.&#x20;
  * 현재 SQL 레지스트리에게 필요한 기능은 이미 DataSource 인터페이스에 정의된 메소드로 충분히 해결 가능하다. 데이터베이스 종료 기능을 가진 EmbbeddedDatabase 타입은 과하다. 따라서 최소한의 필요한 기능을 가진 DataSource 타입으로 주입을 받게 한 것이다.&#x20;



{% hint style="info" %}
현재까지 인터페이스 - 클래스 구조 정리  &#x20;
{% endhint %}

* SqlService - getSql()
  * SqlReader
    * JaxbXmlSqlReader
  * SqlRegistry
    * HashMapSqlRegistry
    * UpdatableRegistry
      * ConcurrentHashMapSqlRegistry&#x20;
      * EmbeddedDbSqlRegistry&#x20;
  * SimpleSqlService
  * BaseSqlService
  * OxmSqlService
    * OxmSqlReader
  * XmlSqlService



#### 테스트 코드 합치기&#x20;

* UpdatableSqlRegistry 인터페이스를 구현하고 있는 ConcurrentHashMapSqlRegistry와 EmbeddedDbSqlRegistry 는 서로 구현하고 있는 코드가 비슷하므로 테스트용 코드도 중복될 가능성이 높다. 따라서 두 개의 테스트를 합쳐보려고 한다.&#x20;

```java
public class ConcurrentHashMapSqlRegistryTest extends AbstractUpdatableSqlRegistryTest {
    public ConcurrentHashMapSqlRegistryTest() {
    }

    protected UpdatableSqlRegistry createUpdatableSqlRegistry() {
        return new ConcurrentHashMapSqlRegistry();
    }
}
```

```java
public class EmbeddedDbSqlRegistryTest extends AbstractUpdatableSqlRegistryTest {
    EmbeddedDatabase db;

    public EmbeddedDbSqlRegistryTest() {
    }

    protected UpdatableSqlRegistry createUpdatableSqlRegistry() {
        this.db = (new EmbeddedDatabaseBuilder()).setType(EmbeddedDatabaseType.HSQL).addScript("classpath:springbook/user/sqlservice/updatable/sqlRegistrySchema.sql").build();
        EmbeddedDbSqlRegistry embeddedDbSqlRegistry = new EmbeddedDbSqlRegistry();
        embeddedDbSqlRegistry.setDataSource(this.db);
        return embeddedDbSqlRegistry;
    }

    @After
    public void tearDown() {
        this.db.shutdown();
    }
}
```

```java
public abstract class AbstractUpdatableSqlRegistryTest {
    UpdatableSqlRegistry sqlRegistry;

    public AbstractUpdatableSqlRegistryTest() {
    }

    @Before
    public void setUp() {
        this.sqlRegistry = this.createUpdatableSqlRegistry();
        this.sqlRegistry.registerSql("KEY1", "SQL1");
        this.sqlRegistry.registerSql("KEY2", "SQL2");
        this.sqlRegistry.registerSql("KEY3", "SQL3");
    }

    //test 픽스처를 생성하는 부분만 추상 메소드로 만들어두고 서브 클래스에서 이를 구현하도록 만든다. 
    protected abstract UpdatableSqlRegistry createUpdatableSqlRegistry();

    @Test
    public void find() {
        this.checkFind("SQL1", "SQL2", "SQL3");
    }

    @Test(
        expected = SqlNotFoundException.class
    )
    public void unknownKey() {
        this.sqlRegistry.findSql("SQL9999!@#$");
    }

    //서브클래스에서 테스트를 추가할 수 잇도록, protected 로 접근 제어자 변경한다. 
    protected void checkFind(String expected1, String expected2, String expected3) {
        Assert.assertThat(this.sqlRegistry.findSql("KEY1"), CoreMatchers.is(expected1));
        Assert.assertThat(this.sqlRegistry.findSql("KEY2"), CoreMatchers.is(expected2));
        Assert.assertThat(this.sqlRegistry.findSql("KEY3"), CoreMatchers.is(expected3));
    }

    @Test
    public void updateSingle() {
        this.sqlRegistry.updateSql("KEY2", "Modified2");
        this.checkFind("SQL1", "Modified2", "SQL3");
    }

    @Test
    public void updateMulti() {
        Map<String, String> sqlmap = new HashMap();
        sqlmap.put("KEY1", "Modified1");
        sqlmap.put("KEY3", "Modified3");
        this.sqlRegistry.updateSql(sqlmap);
        this.checkFind("Modified1", "SQL2", "Modified3");
    }

    @Test(
        expected = SqlUpdateFailureException.class
    )
    public void updateWithNotExistingKey() {
        this.sqlRegistry.updateSql("SQL9999!@#$", "Modified2");
    }
}
```



#### 내장 데이터베이스 이용한 EmbbeddedDbSqlRegistry 적용하기

* jdbc 네임스페이스 + 스키마 위치선언으로 내장 데이터베이스 팩토리 빈 등록&#x20;
  * 데이터베이스 초기화 스크립트를 지정해주자.&#x20;
* XML 설정에서 embeddedDatabase 빈을 dataSource 프로퍼티로 참조하는 EmbeddedDbSqlRegistry 타입의 sqlRegistry 빈을 정의해주면 된다.&#x20;
  * &#x20;embeddedDatabase 타입의 빈은 스프링 컨테이너가 종료될 때 자동으로 shutdown() 메소드 호출되도록 설정되어있다. 내장형 데이터베이스를 종료시키기 위해서 별도 설정이 필요없다는 뜻!&#x20;
* 아래의 설정을 완료한 뒤, UserDaoTest 가 문제없이 실행되는지 확인한다.&#x20;

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   ...
   xmlns:jdbc="http://www.springframework.org/schema/jdbc"
   xsi:schemaLocation="
      ...
      http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd 
      http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-3.0.xsd">
   
   ...
   
   <!-- EmbeddedDbSqlRegistry 클래스를 이용한 빈 등록 -->
   <bean id="sqlService" class="springbook.user.sqlservice.OxmSqlService">
   	<property name="unmarshaller" ref="unmarshaller" /> 
   	<property name="sqlRegistry" ref="sqlRegistry" />
   </bean>
   
   <bean id="sqlRegistry" class="springbook.user.sqlservice.updatable.EmbeddedDbSqlRegistry">
   	<property name="dataSource" ref="embeddedDatabase" />
   </bean>
   
   <!-- 내장형 데이터베이스 등록 -->
   <jdbc:embedded-database id="embeddedDatabase" type="HSQL">
   	<jdbc:script location="classpath:springbook/user/sqlservice/updatable/sqlRegistrySchema.sql"/>
   </jdbc:embedded-database>
   
   ...
</beans>   	
```

### 3. 트랜잭션 적용하기

* 여러개의 SQL 문을 동시에 업데이트 하는 코드가 있다. 만약에 이 업데이트 중간에 오류가 발생하면 어떻게 될까?&#x20;
* 정상적인 상태라면, 그동안 정상적으로 업데이트 되었던 SQL 문들도 모두 롤백하여 처음의 상태로 돌아가는 것이 맞다. 하지만, 지금의 EmbeddedDbSqlRegistry 의 경우는 SimpleJdbcTemplate 을 사용해서 SQL 을 실행하고 있으므로 트랜잭션이 적용되어있지 않다.
* AOP 등 여러가지 방법으로 트랜잭션을 적용할 수 있겠지만, 지금 이 경우는 SQL 레지스트리라는 제한적인 오브젝트 내에서 서비스에 특화된 간단한 트랜잭션이 필요하므로, 그냥 트랜잭션 추상화 API 를 직접 사용하는 것으로 만족할 수 있다.&#x20;
* 진행&#x20;
  * 우선 여러개의 SQL 문을 업데이트 하는 테스트를 만드는데, 두 번째 SQL 문 업데이트에서 반드시 exception 을 만들도록 존재하지 않는 키값을 넣는다.
  * 테스트 내용은 처음 SQL 문들의 상태와 업데이트가 실패한 뒤의 SQL 문들의 상태가 정확하게 일치하는 것이다. 트랜잭션이 제대로 적용되었다면, 중간에 예외가 발생했을 때, 모두 롤백되어야 맞기 때문이다.&#x20;
  * 결과는? 당연히 실패다. 트랜잭션이 적용되지 않은 상태이므로 업데이트 성공한 곳까지는 바뀐 SQL 문이 그대로 남아있게 된다.&#x20;
  * 지금부터는 이 테스트를 성공시키는 방향으로 코드를 작성해나갈 것이다.&#x20;

```java
public class EmbeddedDbSqlRegistryTest extends AbstractUpdatableSqlRegistryTest {
    EmbeddedDatabase db;

    @Test
    public void transactionalUpdate() {
        this.checkFind("SQL1", "SQL2", "SQL3");  //최초 상태
        Map<String, String> sqlmap = new HashMap();
        sqlmap.put("KEY1", "Modified1");
        sqlmap.put("KEY9999!@#$", "Modified9999");

        try {
            this.sqlRegistry.updateSql(sqlmap);
            Assert.fail();
        } catch (SqlUpdateFailureException var3) {
        }

        this.checkFind("SQL1", "SQL2", "SQL3");  //최초 상태와 예외 이후의 상태가 일치하는가 
    }
}

```



* EmbeddedDbSqlRegistry 에 적용하기
  * 템플릿/콜백 패턴 이용하기 위해서 TransactionTemplate 사용&#x20;
  * 트랜잭션 메니저는 AOP 등으로 트랜잭션 프록시가 같이 공유하기 위해서 싱글톤 빈으로 등록해서 만들어지는 경우가 보통인데, 지금 상황에서는 그럴 필요가 없으므로, 별도의 변수로 저장하지 않고 1회성으로만 사용한다.&#x20;
  * transactionTemplate 은 멀티 쓰레드 환경에서 공유해도 안전하도록 만들어져 있으니, 인스턴스 변수로 지정하여 공유한다.&#x20;
* 매우 바쁜 서버환경이라면, 트랜잭션 작업의 격리수준에도 신경써야 한다.&#x20;
  * 내장 데이터베이스 중 하나인 HSQL 는 트랜잭션 격리수준에서 READ\_UNCOMMITED 인 0 단계만 지원한다. 이 단계는 변경이 진행되는 중에 다른 트랜잭션이 변경된 내용을 읽을 가능성이 있다. 그런데 변경되던 중에 롤백되어버리게 되면, 변경된 정보를 읽어간 트랜잭션은 잘못된 정보를 참조한 꼴이 된다.&#x20;
  * 특히 SQL 문 변경과 같은 매우 중요하고 민감한 변경의 경우는 0단계로는 부족하다. READ\_COMMITTED 격리수준을 지원하는 버전이나 다른 DB 를 사용하는 것이 방법이다.&#x20;

```java
public class EmbeddedDbSqlRegistry implements UpdatableSqlRegistry {
    SimpleJdbcTemplate jdbc;
    TransactionTemplate transactionTemplate;

    public EmbeddedDbSqlRegistry() {
    }

    public void setDataSource(DataSource dataSource) {
        this.jdbc = new SimpleJdbcTemplate(dataSource);
        this.transactionTemplate = new TransactionTemplate(new DataSourceTransactionManager(dataSource));
        this.transactionTemplate.setIsolationLevel(2);  //commit 된 데이터만 읽을 수 있도록 격리수준 강화  
    }
    
    ...
    
    public void updateSql(final Map<String, String> sqlmap) throws SqlUpdateFailureException {
        this.transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            protected void doInTransactionWithoutResult(TransactionStatus status) {
                Iterator var3 = sqlmap.entrySet().iterator();

                while(var3.hasNext()) {
                    Entry<String, String> entry = (Entry)var3.next();
                    EmbeddedDbSqlRegistry.this.updateSql((String)entry.getKey(), (String)entry.getValue());
                }

            }
        });
    }
}

```



{% hint style="info" %}
💡 지금까지 우리가 한 작업들의 의미&#x20;
{% endhint %}

* SQL 문을 XML 파일로 일관되게 관리하여 데이터베이스 수정에 DAO가 독립적이도록 만듬
* XML파일로 관리하던 SQL 문들을 자바 오브젝트로 가져오는 방법으로 다양한 OXM 서비스를 이용할 수 있는데, 이러한 기술들을 한 단계 위로 추상화한 스프링 OXM 추상화 서비스를 이용하여 기술에 구애받지 않도록 자유도를 높인 것
* XML 파일 리소스를 가져오는 방법을 프로젝트 내부 뿐만 아니라, 프로젝트 내 다른 디렉토리, 외부폴더, 웹 등 다양한 소스로부터 가져올 수 있도록 자유도를 높이는 작업



## 스프링 3.1의 DI

* 스프링은 지금까지 1.0부터 3.1 까지 발전하는 동안 놀라울 정도의 구 버전 호환성을 보여주었다. 모두 스프링이 견지하고 있는 핵심 철학을 고수한 덕부닝다.&#x20;
* 객체지향적인 코드의 장점인 유연성과 확장성을 스프링 스스로가 충분히 지켜왔기 때문에 스프링 프레임워크 자체도 DI 원칙을 충분히 따라서 만들어졌기 때문에 기존 설계와 코드에 영향을 주지 않고도 꾸준히 새로운 기능을 추가하고 확장해나갈 수 있었다.&#x20;

### 자바 언어 변화와 스프링

* 자바 언어의 변화에 따라 스프링도 영향을 받았다. 대표적으로는 아래의 두 가지
  * 에노테이션의 메타정보 활용
  * 정책과 관례를 이용한 프로그래밍&#x20;

### 에노테이션의 메타정보 활용

* 자바 5부터 등장한 에노테이션 기법은 점차 XML 메타정보 설정 방법을 대체해가기 시작한다.&#x20;
* 에노테이션은 사실 기존의 자바 프로그래밍 방식으로 활용하기가 어렵다.&#x20;
  * 자바 코드가 실행되는 과정에서 직접 참여하지 못하고&#x20;
  * 그 자체로 상속이나 오버라이딩이 가능하지도 않다.&#x20;
  * 동작하는 코드도 넣을 수 없고&#x20;
  * 코드에서 간단히 참조하거나 활용할 수 없다.&#x20;
  * 복잡한 리플렉션 API 를 이용해 에노테이션의 메타정보를 조회하고&#x20;
  * 에노테이션 내에 설정된 값을 가져와서 참고하는 방법이 전부이다.&#x20;
  * 클래스 타입에 영향을 주지도 못하고 일반코드에서도 활용되지 못하기 때문에 일반적인 객체지향 프로그래밍 스타일의 코드나 패턴 등에 적용할 수도 없다.&#x20;
* 그럼에도 불구하고 에노테이션 기법은 왜 널리 퍼지게 되었을까?&#x20;
  * 다음과 같은 방식으로 구성되는 어플리케이션 방식에 잘 어울리기 때문이다.&#x20;
    * 어플리케이션 = 핵심로직을 담은 자바코드 + 이를 지원하는 IoC 방식의 프레임워크 + 프레임워크가 참조하는 메타정보
* 장점
  * 에노테이션 하나를 자바 코드에 넣는 것만으로도 이를 참고하는 코드에서 다양한 부가 정보를 얻을 수 있다.
    * XML로 표현하려면 모든 내용을 명시적으로 길게 작성해야한다.&#x20;
  * XML 방식은 텍스트를 기입하는 것이기 때문에 오타의 가능성도 많다.&#x20;
  * 리펙토링도 에노테이션 방식은 참조하는 관련 코드를 일괄적으로 바꿔주기가 훨씬 쉽다.
* 단점
  * 자바코드에 존재하기 때문에 변경시 다시 컴파일 하는 과정이 필요하다. (XML 은 필요없음)&#x20;
  * 외부에 설정정보 변경을 위해 소스코드를 제공해야한다.&#x20;
* DI 패턴의 변화과정
  * 초기에는 객체지향 언어의 기본에 충실하게 작성된 자바 코드 형태
  * 프레임워크의 발전과 함께 자바 코드 + 프레임워크 + XML 메타정보
  * 스프링 3.1부터는 핵심로직을 담은 자바코드 + DI 프레임워크 + DI 를 위한 메타데이터로서의 자바코드&#x20;



### 정책과 관례를 이용한 프로그래밍

* 명시적으로 동작내용을 기술하는 것 대신에 코드 없이도 미리 약속한 규칙이나 관례를 따라서 프로그램이 동작하도록 만드는 프로그래밍 스타일이 유행하기 시작했다. -> 에노테이션도 그의 일종.&#x20;
* &#x20;예를 들어 `@Transactional`을 살펴보자.&#x20;
  * 만약에 한 오브젝트가 클래스, 인터페이스, 메소드 를 포함해 네 군데에 달려있다면, 트랜잭션 적용은 어떻게 되는 걸까?&#x20;
  * 명확하게 하려면 중첩 설정이 되어있는 경우, `@Transactional(order=1)` 과 같이 적용 우선순위를 직접 지정할 수 있다.&#x20;
  * 하지만 스프링에서는 미리 4단계의 우선순위를 가진 대체 정책을 정해두었다. 이 정책을 기억하고 코드를 작성해야한다. 그렇지 않으면, 코드 레벨에서는 이를 알 수 있는 방법이 없다.&#x20;
  * 이런 문제의 경우는 디버깅도 너무 어렵다 ㅠㅠㅠ&#x20;



{% hint style="info" %}
이후부터는 그동안의 코드를 스프링 3.1 버전의 DI 스타일로 바꾸는 과정을 보여주고 설명한다.&#x20;
{% endhint %}

### 1) 자바 코드를 이용한 빈 설정

* DI 관련 정보를 스프링 3.1로 바꾸는 작업&#x20;
* 테스트 용으로 만들어진 기존의 XML 에서 어플리케이션이 운영환경에서 동작할 때 필요로 하는 DI 정보를 분리해내는 일

#### 1-1. 테스트 코드에서 DI 정보가 XML 에 담겨있다고 생각되는 부분 모두 자바코드로 설정 바꾸기

```java
//Configuration 어노테이션으로 DI 설정정보 세팅할 클래스 만들기 
@Configuration
@ImportResource("/test-applicationContext.xml")
public class TestApplicationContext { ... }

//@ContextConfiguration(locations="/test-applicationContext.xml")
@ContextConfiguration(classes=TestApplicationContext.class)
public class UserDaoTest { ... }


```

#### 1-2. annotation-config 제거

* @PostConstruct 어노테이션이 붙은 매소드를 빈 후처리기에 등록해주어 초기화 이후에 바로 실행될 수 있도록 하기 위한 설정이었다.&#x20;
* 그러니 @Configuration 으로 설정 방식이 변경되면서, 자동으로 등록할 수 있게 되었다.&#x20;

#### 1-3.  bean 전환하기, 전용 태그 전환하기&#x20;

* dataSource, transactionManager 등을 포함하여 모든 bean 태그를 자바 코드로 변환해준다.
* `<tx:annotation-driven />` -> `@EnableTransactionManagement`

```java
@Configuration
@EnableTransactionManagement
public class TestApplicationContext {
	/**
	 * DB����� Ʈ�����
	 */
	
	@Bean
	public DataSource dataSource() {
		SimpleDriverDataSource ds = new SimpleDriverDataSource();
		ds.setDriverClass(Driver.class);
		ds.setUrl("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8");
		ds.setUsername("spring");
		ds.setPassword("book");
		return ds;
	}
	
	@Bean
	public PlatformTransactionManager transactionManager() {
		DataSourceTransactionManager tm = new DataSourceTransactionManager();
		tm.setDataSource(dataSource());
		return tm;
	}
	
	/**
	 * ���ø����̼� ���� & �׽�Ʈ�� ��
	 */
	
	@Autowired SqlService sqlService;
	
	@Bean 
	public UserDao userDao() {
		UserDaoJdbc dao = new UserDaoJdbc();
		dao.setDataSource(dataSource());
		dao.setSqlService(this.sqlService);
		return dao;
	}
	
	@Bean
	public UserService userService() {
		UserServiceImpl service = new UserServiceImpl();
		service.setUserDao(userDao());
		service.setMailSender(mailSender());
		return service;
	}
	
	@Bean
	public UserService testUserService() {
		TestUserService testService = new TestUserService();
		testService.setUserDao(userDao());
		testService.setMailSender(mailSender());
		return testService;
	}
	
	@Bean
	public MailSender mailSender() {
		return new DummyMailSender();
	}
	
	/**
	 * SQL����
	 */
	
	@Bean
	public SqlService sqlService() {
		OxmSqlService sqlService = new OxmSqlService();
		sqlService.setUnmarshaller(unmarshaller());
		sqlService.setSqlRegistry(sqlRegistry());
		return sqlService;
	}
	
	@Bean
	public SqlRegistry sqlRegistry() {
		EmbeddedDbSqlRegistry sqlRegistry = new EmbeddedDbSqlRegistry();
		sqlRegistry.setDataSource(embeddedDatabase());
		return sqlRegistry;
	}
	
	@Bean
	public Unmarshaller unmarshaller() {
		Jaxb2Marshaller marshaller = new Jaxb2Marshaller();
		marshaller.setContextPath("springbook.user.sqlservice.jaxb");
		return marshaller;
	}
	
	@Bean 
	public DataSource embeddedDatabase() {
		return new EmbeddedDatabaseBuilder()
			.setName("embeddedDatabase")
			.setType(HSQL)
			.addScript("classpath:springbook/user/sqlservice/updatable/sqlRegistrySchema.sql")
			.build();
	}
}
```

### 2) 빈 스캐닝과 자동와이어링 (@Autowired)

* 스프링은 @Autowired 가 붙은 수정자 메소드가 있으면 파라미터의 타입을 보고 주입 가능한 타입의 빈을 모두 찾는다.&#x20;
  * 만약 주입 가능한 타입의 빈이 1개라면, 수정자 메소드를 호출해서 넣어준다.
  * 두개 이상이 나오면, 그 중에서 프로퍼티와 동일한 이름의 빈이 있는지 찾는다.&#x20;
  * DataSource 의 경우는 빈이 2개이다. 하나는 userDao 빈이 사용하는 dataSource, 다른 하나는 SQL 서비스 용으로 만든 embeddedDataSource 이다.&#x20;
    * 그 중에서 dataSource 빈이 프로퍼티 이름과 일치하므로 이를 넣어준다.&#x20;
  * 타입과 이름을 모두 비교해도 찾아낼 수 없다면 주입할 빈이 없다는 에러가 난다.&#x20;
* 어차피 목 오브젝트를 이용해서 테스트를 하기 어려운 경우에는 스프링 컨테이너 안에서 DI 이루어진 후에 테스트를 수행하는 방법이 적절하니까, 필드에 바로 @Autowired 를 적용해도 좋다. 하지만 비즈니스 로직을 가지고 있어서 목 오브젝트를 이용하여 적절하게 테스트 간으한 것들은 어노테이션을 이용하더라도 수정자 메소드를 남겨주는 것이 좋다.&#x20;

### 3) @Component 를 이용한 자동 빈 등록&#x20;

* @Component 는 빈으로 등록될 후보 클래스에 붙여주는 일종의 마커이다.&#x20;
* @ComponentScan 을 이용하여 특정 패키지 아래에서만 @Component 에노테이션이 달린 클래스를 찾겠다고 범위 설정을 해준다. 전체 범위를 스캔하려면 부담이 많이 가기 때문이다.&#x20;
  * `@ComponentScan(basePackages="springbook.user")`
    * basePackage 는 여러 개를 넣어도 된다.&#x20;
* 새로운 어노테이션을 만들어 사용할수도 있다. @SnsConnector 라는 에노테이션을 정의할 때, @Component 어노테이션을 붙이면, 클래스마다 @Component 를 따로 붙여주지 않아도 자동으로 빈 등록 대상으로 만들 수 있다.&#x20;
* @Repository : @Component 를 메타 어노테이션으로 가지고 있다. DAO 기능을 제공하는 클래스에는 이를 사용하도록 권장되고 있다.&#x20;
* 다시 한 번 말하지만 @Autowired 는 타입을 기준으로 먼저 찾고, 이름을 기준으로 다시 최종 후보를 찾는다.&#x20;
* @Service : @Repository 처럼 비즈니스 로직을 담고 있는 서비스 계층의 빈을 구분하기 위해 사용된다.&#x20;
  * 보통 서비스 계층은 트랜잭션의 경계가 되는 곳이라서 @Transactional 과 함께 사용되는 경우가 많다.&#x20;
  * 만약에 클래스 이름과 다르게 빈을 등록하고 싶다면, @Service("userService") 와 같이 이름을 명확하게 지정해주면 된다.&#x20;



### 4) 컨텍스트 분리와 @Import&#x20;

* 성격이 다른 DI 정보를 분리할 필요가 있다.&#x20;
* 테스트용 컨텍스트 분리
  * 테스트 용은 TestAppContext, 운영용은 AppContext 로 클래스를 분리한다.&#x20;
  * 하나 이상의 설정 클래스가 테스트에서 사용되게 하려면 모두 나열해주면 된다.&#x20;
    * @ContextConfiguration(classes={AppContext.class, TestAppContext.class})&#x20;
* @Import&#x20;
  * AppContext 중에서도 SQL 과 관련된 빈들은 다른 Context 로 분리하여 관리한다. SqlServiceContext&#x20;
  * 그리고 SqlServiceContext 가 AppContext 에 포함되어 적용되도록 한다.&#x20;
    * ```java
      @Import(SqlServiceContext.class)
      public class AppContext { ... }
      ```

### 5) @Profile and @ActiveProfile&#x20;

* 현재 상황을 보면 아래와 같다.&#x20;
  * mailSender 빈의 경우 TestAppContext 에만 적용되어있다.&#x20;
  * 테스트를 위해서는 더미 클래스를 이용해야한다.&#x20;
  * 하지만 AppContext 에서도 메일 빈이 필요하다. 그렇다고 여기서 또 다른 mailSender 를 정의하면, UserServiceTest 처럼 두 가지의 설정을 모두 사용하는 곳에서는 충돌이 일어난다.&#x20;
* 생각해볼 수 있는 방법은 아래처럼 구분하는 것이다.&#x20;
  * 테스트 환경에서는 @ContextConfiguration 에 AppContext + TestAppContext 를 설정하고&#x20;
  * 운영 환경에서는 AppContext + ProductionAppContext 를 설정하는 것이다.&#x20;
    * ProductionAppContext 에는 운영 환경에서 필요하지만 테스트에는 필요없는 빈이나 설정을 담는다.&#x20;
* 하지만 이건 너무 귀찮다. 방법이 없을까?&#x20;
  * @Profile : 각 컨텍스트에 실행 환경 정보를 세팅한다. test, production 등&#x20;
  * @ActiveProfile : 지금 실행하는 이 클래스에 적용할 환경정보를 설정한다. test 로 설정하면 테스트 환경으로 실행된다.&#x20;
* 아래와 같이 static 중첩 클래스로 넣은 @Configuration 은 스프링이 자동으로 포함시켜준다. 따라서 아래의 AppContext 에서는 SqlServiceContext 만 설정해주면 된다.&#x20;

```java
@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages="springbook.user")
@Import(SqlServiceContext.class)
public class AppContext {      //default context 
   @Bean
   public DataSource dataSource() {
      SimpleDriverDataSource ds = new SimpleDriverDataSource();
      ds.setDriverClass(Driver.class);
      ds.setUrl("jdbc:mysql://localhost/springbook?characterEncoding=UTF-8");
      ds.setUsername("spring");
      ds.setPassword("book");
      return ds;
   }
   
   @Bean
   public PlatformTransactionManager transactionManager() {
      DataSourceTransactionManager tm = new DataSourceTransactionManager();
      tm.setDataSource(dataSource());
      return tm;
   }
   
   @Configuration
   @Profile("production")
   public static class ProductionAppContext {
      @Bean
      public MailSender mailSender() {
         JavaMailSenderImpl mailSender = new JavaMailSenderImpl();
         mailSender.setHost("localhost");
         return mailSender;
      }
   }
   
   @Configuration
   @Profile("test")
   public static class TestAppContext {
      @Bean
      public UserService testUserService() {
         return new TestUserService();
      }
      
      @Bean
      public MailSender mailSender() {
         return new DummyMailSender();
      }
   }

}

```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ActiveProfiles("test")
@ContextConfiguration(classes=AppContext.class)
public class UserServiceTest { ... }
```



#### 위와 같은 설정이 제대로 적용되고 있는지 확인하려면 빈 등록 정보를 확인하면 된다.&#x20;

* 스프링 컨테이너는 모두 BeanFactory 라는 인터페이스를 구현하고 있다.&#x20;
* 이 인터페이스를 구현한 클래스 중에 DefaultListableBeanFactory 가 있다. 거의 대부분의 스프링 컨테이너는 이 클래스를 이용해서 빈을 등록하고 관리한다.&#x20;
* 게다가 이 오브젝트는 @Autowired 로 주입받아서 이용하 수 있다.
* 내부에는 getBeanDefinitionNames() 라는 메소드가 있어서 컨테이너에 등록된 모든 빈 이름을 가져올 수 있고, 빈 이름을 이용해서 실제 빈과 빈 클래스 정보도 조회해볼 수 있다.&#x20;
* 아래의 코드에서 @ActiveProfile 을 적용하여, 테스트 환경과 운영 환경의 빈 생성이 다른지 확인하면 된다. &#x20;

```java
@Autowired DefaultListableBeanFactory bf;

@Test
public void beans() {
    for (String n : bf.getBeanDefinitionNames()) {
        System.out.println(n + " \t" + bf.getBean(n).getClass().getName());
    }
}
```



### 6) 프로퍼티 소스&#x20;

* dataSource 에 쓰이는 정보는 환경별로 다르게 프로퍼티가 적용되어야 한다. 이런 정보들은 소스코드 내에서 관리하지 않고, 외부 파일로 빼두어 관리하는 편이 좋다.&#x20;
  * ```properties
    //database.properties
    db.driverClass=com.mysql.jdbc.Driver
    db.url=jdbc:mysql://localhost/springbook?characterEncoding=UTF-8
    db.username=spring
    db.password=book
    ```
* 이제 환경에 맞게 다른 프로퍼티 파일을 가져오도록 소스코드를 수정해주면 된다.&#x20;

```java

@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages="springbook.user")
@EnableSqlService
@PropertySource("/database.properties")
public class AppContext implements SqlMapConfig {
	@Value("${db.driverClass}") Class<? extends Driver> driverClass;
	@Value("${db.url}") String url;
	@Value("${db.username}") String username;
	@Value("${db.password}") String password;

	...
	
	@Bean
	public DataSource dataSource() {
		SimpleDriverDataSource ds = new SimpleDriverDataSource();
		
		ds.setDriverClass(this.driverClass);
		ds.setUrl(this.url);
		ds.setUsername(this.username);
		ds.setPassword(this.password);
		
		return ds;
	}
	
	...
}
```



### 7) 빈 설정의 재사용과 @Enable\* &#x20;

```java
@Import(value=SqlServiceContext.class)
public @interface EnableSqlService {
}

@Configuration
public class SqlServiceContext { ... }

...
@EnableSqlService
public class AppContext implements SqlMapConfig { ... }
```



### 8) 최종본

```java
@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages="springbook.user")
@EnableSqlService
@PropertySource("/database.properties")
public class AppContext implements SqlMapConfig {
   @Value("${db.driverClass}") Class<? extends Driver> driverClass;
   @Value("${db.url}") String url;
   @Value("${db.username}") String username;
   @Value("${db.password}") String password;
   
   @Override
   public Resource getSqlMapResouce() {
      return new ClassPathResource("sqlmap.xml", UserDao.class);
   }
   
   @Bean
   public static PropertySourcesPlaceholderConfigurer placeholderConfigurer() {
      return new PropertySourcesPlaceholderConfigurer();
   }
   
   @Bean
   public DataSource dataSource() {
      SimpleDriverDataSource ds = new SimpleDriverDataSource();
      
      ds.setDriverClass(this.driverClass);
      ds.setUrl(this.url);
      ds.setUsername(this.username);
      ds.setPassword(this.password);
      
      return ds;
   }
   
   @Bean
   public PlatformTransactionManager transactionManager() {
      DataSourceTransactionManager tm = new DataSourceTransactionManager();
      tm.setDataSource(dataSource());
      return tm;
   }
   
   @Configuration
   @Profile("production")
   public static class ProductionAppContext {
      @Bean
      public MailSender mailSender() {
         JavaMailSenderImpl mailSender = new JavaMailSenderImpl();
         mailSender.setHost("localhost");
         return mailSender;
      }
   }
   
   @Configuration
   @Profile("test")
   public static class TestAppContext {
      @Bean
      public UserService testUserService() {
         return new TestUserService();
      }
      
      @Bean
      public MailSender mailSender() {
         return new DummyMailSender();
      }
   }
}
```



## 정리

* 스프링 사용자라면 객체지향적인 설계와 DI 를 효과적으로 활용하는 방법이 익숙해야한다. 스프링이 제공해주지 않는 기능도 직접 구현할 수 있어야 하고, 그때에도 적극적으로 DI 와 서비스 추상화, AOP 등을 활용할 수 있어야 한다.&#x20;
* SQL 처럼 변경될 수 있는 텍스트 정보는 외부 리소스에 담아두고 가져오게 만들면 편리하다.&#x20;
* 성격이 다른 코드가 한데 섞여있는 클래스라면, 먼저 인터페이스를 정의해서 코드를 각 인터페이스별로 분리하는게 좋다. 다른 인터페이스에 속한 기능은 인터페이스를 통해 접근하게 만들고, 간단히 자기 참조 빈으로 의존관계를 만들어 검증한다. 검증을 마쳤으면 아예 클래스를 분리해도 좋다.&#x20;
* 자주 사용되는 의존 오브젝트는 디폴트로 미리 정의해두면 편리하다.&#x20;
* XML 과 오브젝트 매핑은 스프링의 OXM 추상화 기능을 활용한다.&#x20;
* 특정 의존 오브젝트를 고정시켜 기능을 특화하려면 멤버 클래스로 만드는 것이 편리하다. 기존에 만들어진 기능과 중복되는 부분은 위임을 통해 중복을 제거하는게 좋다.&#x20;
* 외부 파일이나 리소스를 사용하는 코드에서는 스프링 리소스 추상화와 리소스 로더를 사용한다.&#x20;
* DI 를 의식하면서 코드를 작성하면 객체지향 설계에 도움이 된다.&#x20;
* DI 에는 인터페이스를 사용한다. 인터페이스를 사용하면 인터페이스 분리 원칙을 잘 지키는데도 도움이 된다.&#x20;
* 클라이언트에 따라서 인터페이스를 분리할 때, 새로운 인터페이스를 만드는 방법과 인터페이스를 상속하는 방법 두가지를 사용할 수 있다.&#x20;
* 어플리케이션에 내장하는 DB 를 사용할 때에는 스프링 내장형 DB 추상화 기능과 전용태그를 사용하면 편리하다.&#x20;



