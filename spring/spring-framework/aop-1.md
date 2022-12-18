# AOP 1

## 지금까지

* 서비스 추상화 기법을 통해서 트랜잭션 기술에 독립적으로 개선
* 하지만, 트랜잭션 경계 설정을 위해 UserService 내부에 비즈니스 로직보다 트랜잭션을 위한 코드가 더 많은 범위를 차지하는 것, 트랜잭션 코드와 비즈니스 로직이 섞이게 되는 것이 불만족스럽다.

## 어떻게 개선할 수 있을까?

* UserService 의 시작부터 끝까지 트랜잭션 범위가 설정되기 때문에 코드가 그곳에 존재하는 것은 불가피해보임.
* 그럼 겉으로 보기에 아예 없는 것처럼 보이게만이라도 할 수는 없을까?
* 현재 구조는 아래와 같다.
  * Client : UserServiceTest → UserService
  * 문제점 : 직접적으로 클래스에 의존하고 있기 때문에 강한 결합상태이다.
* 바꾼다면 아래와 같이 바꿔볼 수 있지 않나?
  * 바꾸려는 최종 구조
    * UserServiceTest - client
    * UserService - interface
      * UserServiceImpl - business logic
      * UserServiceTx - transaction
  * 이렇게 바꾸면 클라이언트 입장에서 보기에는 비즈니스 로직 뿐인 코드에 트랜잭션이 적용된 것처럼 보일 것이다.

## 1차 개선

* UserService
  * interface 화 한다.
* UserServiceImpl
  * mail, upgradeLevels() 제외하고 비즈니스 로직 이외의 트랜잭션 코드는 모두 제거한다.
* UserServiceTx
  * 트랜잭션에만 집중하므로, add(), upgradeLevels() 에서는 DI 받은 다른 UserService 구현체의 매소드를 호출해서 비즈니스 로직에 관여하지 않는다.
  * upgradeLevels() 에 transaction 관련 코드를 추가한다. 이때 트랜잭션 코드 사이에 비즈니스 로직을 호출하는 부분은 DI 받은 다른 UserService 구현체를 호출한다.
* 설정정보 변경하기
  * 이 구조로 개선하게 되면 최종 의존관계는 아래와 같다.
    * client : UserServiceTest → UserServiceTx → UserServiceImpl
  * 따라서 설정파일도 아래와 같이 변경
    * bean : userServiceTx → transactionManager, userServiceImpl
    * bean : userServiceImpl → userdao, mailSender
* 테스트 변경하기
  * userService 를 직접 생성후 주입하던 방식 → DI 하는 방식으로 변경
  * @AutoWired 의 경우, 가장 먼저 일치하는 타입의 클래스를 검사하고, 없다면, bean 설정에서 이름이 같은 클래스를 매핑해준다. 이 경우, userService 는 인터페이스화 되어 클래스가 사라졌으니, userService 라는 빈 이름을 가지고 있는 userServiceTx 가 매핑될 것이다.
* 1차 개선으로 인한 장점
  * 비즈니스 로직을 담당하는 userServiceImpl 에서 트랜잭션과 같은 기술적인 내용을 신경쓰지 않아도 된다.
  * 비즈니스 로직에 대한 테스트가 훨씬 쉬워졌다.

```java
public interface UserService {
   void add(User user);
   void upgradeLevels();
}

public class UserServiceImpl implements UserService { ... }

public class UserServiceTx implements UserService {
	UserService userService;
	PlatformTransactionManager transactionManager;

	public void setTransactionManager(PlatformTransactionManager transactionManager) {
		this.transactionManager = transactionManager;
	}

	public void setUserService(UserService userService) {
		this.userService = userService;
	}

	public void add(User user) {
		this.userService.add(user);
	}

	public void upgradeLevels() {
		TransactionStatus status = this.transactionManager.getTransaction(new DefaultTransactionDefinition());
		try {
			//주입받은 비즈니스로직 구현체인 userService 내 매소드를 호출. 
			//이 트랜잭션 클래스는 비즈니스 로직에 전혀 관여하지 않는다. 			
			userService.upgradeLevels();  
			this.transactionManager.commit(status);
		} catch (RuntimeException e) {
			this.transactionManager.rollback(status);
			throw e;
		}
	}
}

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="/test-applicationContext.xml")
public class UserServiceTest {

	@Test
	public void upgradeAllOrNothing() {
		TestUserService testUserService = new TestUserService(users.get(3).getId());
		testUserService.setUserDao(userDao);
		testUserService.setMailSender(mailSender);
		
		//클라이언트인 Test -> 프록시인 UserServiceTx 호출 -> 테스트용 구현체 TestUserService 호출
		UserServiceTx txUserService = new UserServiceTx();
		txUserService.setTransactionManager(transactionManager);
		txUserService.setUserService(testUserService);
		 
		userDao.deleteAll();			  
		for(User user : users) userDao.add(user);
		
		try {
			txUserService.upgradeLevels();   
			fail("TestUserServiceException expected"); 
		}
		catch(TestUserServiceException e) { 
		}
		
		checkLevelUpgraded(users.get(1), false);
	}

}
```

```xml
<bean id="userService" class="springbook.user.service.UserServiceTx">
   <property name="transactionManager" ref="transactionManager" />
   <property name="userService" ref="userServiceImpl" />
</bean>

<bean id="userServiceImpl" class="springbook.user.service.UserServiceImpl">
   <property name="userDao" ref="userDao" />
   <property name="mailSender" ref="mailSender" />
</bean>
```

## 고립된 단위테스트의 필요성

* 테스트 단위는 작을수록 좋다. 하나의 테스트에서는 하나의 기능만을 체크할 수 있도록 해야한다.
* 구조가 엉망이라면 하나의 기능을 테스트 하기 위해, 수십개의 의존성과 영향도를 확인해야할 것이다.
  * 예를 들면, UserService 의 레벨업 기능인 upgradeLevels() 를 테스트 하기 위해서 UserDao, UserDaoJdbc, DataSource, DB, TransactionManager, JavaMailSenderImpl, JavaMail, MailServer 까지 모든 부분이 완벽하게 세팅되어있어야 한다.
* 오브젝트 간 의존도를 낮추고 고립된 테스트가 가능하도록 구조를 끊임없이 개선해야한다.

## 개선한 UserServiceTest 의 upgradeLevels()

* getAll()시에는 미리 준비된 사용자들의 목록인 스텁이 필요하고 update() 시에는 목 오브젝트로서 동작하는 테스트 대역이 필요하다. → MockUserDao 를 내부 스태틱 클래스로 생성해보자. (UserDaoImpl)
* 이렇게 되면 UserServiceTest 는 외부 UserDao 나 MailSender 와 같은 외부 빈에 의존할 필요가 없으므로, @AutoWired 로 인해 주입받는 것이 아닌, 내부에서 UserServiceImpl을 직접 생성 해줌으로써 테스트를 진행할 수 있다. MockUserDao 역시 직접 DI 해준다.
* 장점
  * 이제 DAO 나 외부 로직에 의존하지 않은 UserService 만의 비즈니스 로직만 효율적으로 테스트할 수 있게 되었다.
  * 더군다나 테스트 시간도 DB 를 갔다가 오는 시간이 줄어들기 때문에 이전과 비교하면 500배이상 빨라진 것을 알 수 있다. 테스트 개수가 늘어날수록, 어플리케이션의 크기가 커질수록 그 효과는 엄청나다.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(
    locations = {"/test-applicationContext.xml"}
)
public class UserServiceTest {

		@Test
    public void upgradeLevels() throws Exception {
        UserServiceImpl userServiceImpl = new UserServiceImpl();
        UserServiceTest.MockUserDao mockUserDao = new UserServiceTest.MockUserDao(this.users, (UserServiceTest.MockUserDao)null);
        userServiceImpl.setUserDao(mockUserDao);

        UserServiceTest.MockMailSender mockMailSender = new UserServiceTest.MockMailSender();
        userServiceImpl.setMailSender(mockMailSender);
        userServiceImpl.upgradeLevels();

        List<User> updated = mockUserDao.getUpdated();
        Assert.assertThat(updated.size(), CoreMatchers.is(2));

        this.checkUserAndLevel((User)updated.get(0), "joytouch", Level.SILVER);
        this.checkUserAndLevel((User)updated.get(1), "madnite1", Level.GOLD);

        List<String> request = mockMailSender.getRequests();
        Assert.assertThat(request.size(), CoreMatchers.is(2));
        Assert.assertThat((String)request.get(0), CoreMatchers.is(((User)this.users.get(1)).getEmail()));
        Assert.assertThat((String)request.get(1), CoreMatchers.is(((User)this.users.get(3)).getEmail()));
    }

		static class MockMailSender implements MailSender {
        private List<String> requests = new ArrayList();

        MockMailSender() {
        }

        public List<String> getRequests() {
            return this.requests;
        }

        public void send(SimpleMailMessage mailMessage) throws MailException {
            this.requests.add(mailMessage.getTo()[0]);
        }

        public void send(SimpleMailMessage[] mailMessage) throws MailException {
        }
    }

    static class MockUserDao implements UserDao {
        private List<User> users;
        private List<User> updated;

        private MockUserDao(List<User> users) {
            this.updated = new ArrayList();
            this.users = users;
        }

        public List<User> getUpdated() {
            return this.updated;
        }

        public List<User> getAll() {
            return this.users;
        }

        public void update(User user) {
            this.updated.add(user);
        }

        public void add(User user) {
            throw new UnsupportedOperationException();
        }

        public void deleteAll() {
            throw new UnsupportedOperationException();
        }

        public User get(String id) {
            throw new UnsupportedOperationException();
        }

        public int getCount() {
            throw new UnsupportedOperationException();
        }
    }

    static class TestUserService extends UserServiceImpl {
        private String id;

        private TestUserService(String id) {
            this.id = id;
        }

        protected void upgradeLevel(User user) {
            if (user.getId().equals(this.id)) {
                throw new UserServiceTest.TestUserServiceException();
            } else {
                super.upgradeLevel(user);
            }
        }
    }

    static class TestUserServiceException extends RuntimeException {
        TestUserServiceException() {
        }
    }

}
```

## 단위테스트와 통합테스트

* 단위테스트의 범위는 정하기 나름이지만, 보통의 경우 테스트 대상 클래스를 목 오브젝트 등의 테스트 대역을 이용해 의존 오브젝트나 외부의 리소스를 사용하지 않도록 고립시켜서 테스트 하는 것을 말한다.
* 통합테스트의 경우, 두 개 이상의, 성격이나 계층이 다른 오브젝트가 연동하도록 만들어 테스트하거나, 외부의 DB나 파일, 서비스 등의 리소스가 참여하는 테스트를 말한다.
  * 두 개 이상의 단위가 결합해서 동작하면서 테스트가 수행되는 것
* 가이드라인
  * 단위테스트를 먼저 고려한다.
  * 하나의 클래스나 성격과 목적이 같은 긴밀한 클래스를 몇 개 모아서, 외부와의 의존과나계를 모두 차단하고 필요에 따라 스텁이나 목 오브젝트 등의 테스트 대역을 이용하도록 테스트를 만든다.
  * 외부 리소스를 사용해야만 가능한 테스트만 통합테스트로 만든다.
  * DAO 와 같이 DB 에 의존적이고 로직이 없는 코드의 경우는 통합테스트로 만드는 것이 낫다. 이 테스트를 잘 작성해두어야, 나중에 DAO 가 쓰이는 다른 곳에서도 목오브젝트를 통해 DAO 역할을 대체할 수 있다. DAO 에서도 잘 동작하리라는 믿음이 있기 때문이다.
  * 단위테스트를 모두 통과했다고 해서, 서로 연관된 코드들이 반드시 잘 동작하리라는 보장은 없다. 그래서 여러개의 단위가 의존관계를 가지고 동작할 때를 위한 통합테스트는 반드시 필요하다. 하지만 이 통합테스트 마저 단위테스트가 충분히 잘 이루어졌다면, 작성하기가 훨씬 수월하다.
  * 스프링 테스트 컨텍스트 프레임워크를 이용하는 테스트 역시 통합테스트이다. 가능하면 스프링의 지원없이 직접 코드 레벨의 DI 를 사용하면서 단위테스트 하는 것이 좋다. 하지만 스프링 설정 자체도 테스트 대상이고 보다 추상적인 레벨에서 테스트 해야할 경우도 있다.
* 테스트 잘 작성하면 코드 품질은 자연스럽게 높아진다. 코드를 작성할 때, 어떻게 테스트할 수 있을까 고민하는 습관을 들이도록 하자.

## 목 프레임워크

* 단위테스트가 중요한건 알지만, 작성하기가 너무 귀찮다. 준비과정이 너무 길고 고단하다. 해줘야할 것이 많기 때문이다.
* 다행히도 목 오브젝트를 편리하게 작성하도록 도와주는 다양한 목 오브젝트 지원 프레임워크가 존재한다!
* Mockito 프레임워크 - 사용하기 편함

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(
    locations = {"/test-applicationContext.xml"}
)
public class UserServiceTest {

		@Test
    public void mockUpgradeLevels() throws Exception {
        UserServiceImpl userServiceImpl = new UserServiceImpl();
        UserDao mockUserDao = (UserDao)Mockito.mock(UserDao.class);
        Mockito.when(mockUserDao.getAll()).thenReturn(this.users);
        userServiceImpl.setUserDao(mockUserDao);

        MailSender mockMailSender = (MailSender)Mockito.mock(MailSender.class);
        userServiceImpl.setMailSender(mockMailSender);

        userServiceImpl.upgradeLevels();

        ((UserDao)Mockito.verify(mockUserDao, Mockito.times(2))).update((User)Matchers.any(User.class));
        ((UserDao)Mockito.verify(mockUserDao, Mockito.times(2))).update((User)Matchers.any(User.class));
        ((UserDao)Mockito.verify(mockUserDao)).update((User)this.users.get(1));
        Assert.assertThat(((User)this.users.get(1)).getLevel(), CoreMatchers.is(Level.SILVER));

        ((UserDao)Mockito.verify(mockUserDao)).update((User)this.users.get(3));
        Assert.assertThat(((User)this.users.get(3)).getLevel(), CoreMatchers.is(Level.GOLD));

        ArgumentCaptor<SimpleMailMessage> mailMessageArg = ArgumentCaptor.forClass(SimpleMailMessage.class);
        ((MailSender)Mockito.verify(mockMailSender, Mockito.times(2))).send((SimpleMailMessage)mailMessageArg.capture());

        List<SimpleMailMessage> mailMessages = mailMessageArg.getAllValues();
        Assert.assertThat(((SimpleMailMessage)mailMessages.get(0)).getTo()[0], CoreMatchers.is(((User)this.users.get(1)).getEmail()));
        Assert.assertThat(((SimpleMailMessage)mailMessages.get(1)).getTo()[0], CoreMatchers.is(((User)this.users.get(3)).getEmail()));
    }

}
```

## 프록시와 타겟

* 현재 구조는 아래와 같다.
  * client : UserServiceTest → 핵심기능을 사용하는 것으로 착각하며 UserService의 구현체 호출
  * UserServiceTx 는 UserService를 구현한 상태, 트랜잭션 코드 이후 핵심 비즈니스 로직을 위해 또 다른 UserService 구현체인 UserServiceImpl 호출
  * UserServiceImpl 는 핵심 비즈니스 로직 실행
* 즉, `클라리언트 → (핵심기능 인터페이스 + 부가기능) 프록시 → (핵심기능 인터페이스 + 핵심기능) 타깃`의 형태로 요청이 전달되며, 클라이언트는 마치 자신이 타깃의 기능을 사용한다고 착각하고, 프록시는 클라이언트의 요청을 받아 최종적으로 타깃에게 그 요청을 위임한다. 타깃은 위임받은 요청을 처리한다.
* 프록시는 두 가지로 그 목적에 따라 구분된다.
  * 첫째, 클라이언트가 타깃에 접근하는 방법을 제어하기 위해서 → 프록시 패턴
  * 둘쨰, 타깃에게 부가적인 기능을 부여하기 위해서 → 데코레이터 패턴
* 데코레이터 패턴
  * 목적 : 타깃에게 부가적인 기능을 런타임시에 다이나믹하게 부여해주기 위해서 프록시를 사용하는 패턴
  * 인터페이스를 통해 연결되기 때문에 여러 개의 데코레이터가 같이 쓰일 수 있다.
  * 예를 들면 아래와 같은 식
    * 클라리언트 → 라인넘버 데코레이터 → 컬러 데코레이터 → 페이징 데코레이터 → 타깃 : 소스코드 출력 기능
  * 인터페이스를 통해 위임하는 방식이기 때문에 타깃이 어떤 데코레이터로부터 연결되는지 알 수 없다. 런타임시에 클라이언트를 통해서 다이나믹하게 생성되기 때문이다.
* 프록시 패턴
  * 목적 : 타깃의 기능을 확장하거나 추가하지 않으면서 접근하는 방법을 제어해주는 프록시를 이용하는 방법이다.
  * 코드에서 자신이 만들거나 접근할 타깃 클래스 정보를 알고 있는 경우가 많다.
* 두 개의 패턴을 같이 사용하기도 한다.

## JDK의 다이내믹 프록시

* 개발자들은 프록시의 장점에도 불구하고 프록시를 작성하기에 귀찮아한다.
* 프록시 작성의 문제점
  * 부가 기능이 필요없는 매소드도 일일히 구현해서 타깃으로 위임하는 코드를 만들어줘야 한다.
    * 예) UserService 의 add()
  * 또한 인터페이스의 메소드에 추가나 변경이 일어날 때마다 함께 수정해주어야 한다.
  * 프록시 내부에서 중복코드가 많아질 가능성이 있다.
    * 예) transaction 을 필요로하는 매서드가 upgradeLevels() 이외에, add(), delete() 등등 많아질 경우, 매번 transaction 코드로 감싸고 그 내부에서 요청을 위임하는 코드를 짜야한다. 같은 구조의 코드가 반복될 가능성이 높아지는 것이다.
* 이를 해결할 수 있는 방법 : JDK 다이내믹 프록시
* JDK 다이내믹 프록시는 리플렉션 기능을 이용해서 프록시를 만들어준다.

## HelloUppercase 예제를 통한 다이내믹 프록시 동작과정 확인

* 직접 다이내믹 프록시를 구현했을 때
  * 인터페이스의 모든 매소드를 구현해 위임하도록 코드를 만들어야 하고
  * 부가기능인 리턴값을 대문자로 바꾸는 기능이 모든 메소드에 중복되어 나타난다.
* 다이내믹 프록시를 적용했을 때
  * 동작방식
    * 클라이언트 → 프록시 팩토리에게 프록시를 요청하고, 다이내믹 프록시의 매소드를 호출한다.
    * 다이내믹 프록시는 InvocationHandler 를 통해 매소드 처리를 요청한다.
    * InvocationHandler 에서는 요청을 타깃에게 위임하고 그 결과를 다이내믹 프록시에게 전달한다.
    * 다이내믹 프록시는 클라이언트에게 리턴값을 반환한다.
  * 다이내믹 프록시의 sayHello(), sayHi(), sayThankYou() → InvocationHandler 의 invoke(Method) →HelloTarget의 sayHello(), sayHi(), sayThankYou()

```java
...
public class DynamicProxyTest {
    public DynamicProxyTest() {
    }

    @Test
    public void simpleProxy() {
        DynamicProxyTest.Hello hello = new DynamicProxyTest.HelloTarget();
        Assert.assertThat(hello.sayHello("Toby"), CoreMatchers.is("Hello Toby"));
        Assert.assertThat(hello.sayHi("Toby"), CoreMatchers.is("Hi Toby"));
        Assert.assertThat(hello.sayThankYou("Toby"), CoreMatchers.is("Thank You Toby"));

        DynamicProxyTest.Hello proxiedHello = (DynamicProxyTest.Hello)Proxy.newProxyInstance(this.getClass().getClassLoader(), new Class[]{DynamicProxyTest.Hello.class}, new DynamicProxyTest.UppercaseHandler(new DynamicProxyTest.HelloTarget(), (DynamicProxyTest.UppercaseHandler)null));
        Assert.assertThat(proxiedHello.sayHello("Toby"), CoreMatchers.is("HELLO TOBY"));
        Assert.assertThat(proxiedHello.sayHi("Toby"), CoreMatchers.is("HI TOBY"));
        Assert.assertThat(proxiedHello.sayThankYou("Toby"), CoreMatchers.is("THANK YOU TOBY"));
    }

    interface Hello {
        String sayHello(String var1);

        String sayHi(String var1);

        String sayThankYou(String var1);
    }

    static class HelloTarget implements DynamicProxyTest.Hello {
        HelloTarget() {
        }

        public String sayHello(String name) {
            return "Hello " + name;
        }

        public String sayHi(String name) {
            return "Hi " + name;
        }

        public String sayThankYou(String name) {
            return "Thank You " + name;
        }
    }

		//직접 구현했을 때
    static class HelloUppercase implements DynamicProxyTest.Hello {
        DynamicProxyTest.Hello hello;

        public HelloUppercase(DynamicProxyTest.Hello hello) {
            this.hello = hello;
        }

        public String sayHello(String name) {
            return this.hello.sayHello(name).toUpperCase();
        }

        public String sayHi(String name) {
            return this.hello.sayHi(name).toUpperCase();
        }

        public String sayThankYou(String name) {
            return this.hello.sayThankYou(name).toUpperCase();
        }
    }

		//invokationHandler 로 구현했을 때 
    static class UppercaseHandler implements InvocationHandler {
        Object target;

        private UppercaseHandler(Object target) {
            this.target = target;
        }

        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            Object ret = method.invoke(this.target, args);
            return ret instanceof String && method.getName().startsWith("say") ? ((String)ret).toUpperCase() : ret;
        }
    }
}
```

#### 다이내믹 프록시 장점

* Hello 인터페이스의 메소드가 엄청나게 늘어났을 때, 매번 코드를 추가해주지 않아도 된다.
  * 예) 3개에서 30개가 된다면…? 매번 코드를 추가해야한다. 다이내믹 프록시가 만들어질 때, 추가된 메소드가 자동으로 포함되고, 부가기능은 invoke() 메소드에서 이루어지기 때문이다.
* 리턴타입이 다양한 경우는 어떻게 할까?
  * 리턴타입을 확인해서 스트링인 경우만 대문자로 바꿔주어도 된다.
* 타깃의 종류에 상관없이 적용이 가능하다. `UpperCaseHandler` 라고 제네럴하게 명명한 뒤, 이 프록시가 필요한 곳이 잇다면, 어떤 타깃이라도 사용할 수 있다.
* 리턴타입 뿐만 아니라 메소드의 이름에도 조건을 거는 것이 가능하다.
  * 메소드 이름이 say 로 시작하는 경우만 적용하고 싶다면, 아래의 코드에서와 같이 가능하다.
    * `return ret instanceof String && method.getName().startsWith("say") ? ((String)ret).toUpperCase() : ret;`

## 그렇다면, 본격적으로 UserServiceTx 에도 다이내믹 프록시를 적용해보자!

* TransactionHandler 와 다이내믹 프록시를 스프링의 DI 를 통해 사용할 수 있도록 만들어야 한다.
* 문제점
  * 다이내믹 프록시는 일반적인 스프링의 빈으로는 등록할 방법이 없다.
  * 스프링 빈은 기본적으로 클래스 이름과 프로퍼티로 정의되는데, 리플렉션 API 를 이용하여 빈 정의에 나오는 클래스 이름을 가지고 빈 오브젝트를 생성한다.
  * 문제는 다이내믹 프록시 오브젝트의 클래스는 어떤 것인지 알 수가 없다. Proxy 클래스의 newProxyInstance() 라는 스태틱 팩토리 매소드를 통해서만 만들 수 있기 때문이다.

### 팩토리 빈을 이용한 해결방법

* 클래스 정보를 가지고 디폴트 생성자를 통해 오브젝트를 만드는 방법 이외에 다양한 방법이 있다. 그 중 하나가 팩토리 빈을 이용한 빈 생성 방법이다.
* 팩토리 빈을 만드는 방법도 여러가지가 있는데 가장 간단한 방법은 FactoryBean 인터페이스를 구현하는 것이다.
* 예제 : private 생성자를 가지고 있는 Message 클래스
  * private 생성자를 가지고 있는 클래스는 스프링에서 빈으로 등록할 수 없다. (사실 리플렉션의 권한이 막강하여 가능은 한데, 직접 생성하지말고 스태틱 메소드를 통해 우회하도록 정의해뒀으므로, 그를 따르는 것이 권장된다.)

#### Message 예제

```java
...
public class Message {
    String text;

    private Message(String text) {
        this.text = text;
    }

    public String getText() {
        return this.text;
    }

    public static Message newMessage(String text) {
        return new Message(text);
    }
}

...
public class MessageFactoryBean implements FactoryBean<Message> {
    String text;

    public MessageFactoryBean() {
    }

    public void setText(String text) {
        this.text = text;
    }

    public Message getObject() throws Exception {
        return Message.newMessage(this.text);  //bean에서 생성해주는 proxy 
    }

    public Class<? extends Message> getObjectType() {
        return Message.class;
    }

    public boolean isSingleton() {
        return true;
    }
}

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration
public class FactoryBeanTest {
    @Autowired
    ApplicationContext context;

    public FactoryBeanTest() {
    }

    @Test
    public void getMessageFromFactoryBean() {
        Object message = this.context.getBean("message");
        Assert.assertThat(message, CoreMatchers.is(Message.class));
        Assert.assertThat(((Message)message).getText(), CoreMatchers.is("Factory Bean"));
    }

    @Test
    public void getFactoryBean() throws Exception {
        Object factory = this.context.getBean("&message");
        Assert.assertThat(factory, CoreMatchers.is(MessageFactoryBean.class));
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans ...>
   <bean id="message" class="springbook.learningtest.spring.factorybean.MessageFactoryBean">
      <property name="text" value="Factory Bean" />
   </bean>

</beans>
```

#### UserService 에 적용

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(
    locations = {"/test-applicationContext.xml"}
)
public class UserServiceTest {

		@Test
    @DirtiesContext
    public void upgradeAllOrNothing() throws Exception {
        UserServiceTest.TestUserService testUserService = new UserServiceTest.TestUserService(((User)this.users.get(3)).getId(), (UserServiceTest.TestUserService)null);
        testUserService.setUserDao(this.userDao);
        testUserService.setMailSender(this.mailSender);
        
				//factoryBean 내부에서 만들어지는 proxy 객체와 그에 주입되는 UserServiceImpl을 테스트용으로 만든 TestUserService 로 교체해주기 위한 작업
				TxProxyFactoryBean txProxyFactoryBean = (TxProxyFactoryBean)this.context.getBean("&userService", TxProxyFactoryBean.class);
        txProxyFactoryBean.setTarget(testUserService);
        UserService txUserService = (UserService)txProxyFactoryBean.getObject();

        this.userDao.deleteAll();
        Iterator var5 = this.users.iterator();

        while(var5.hasNext()) {
            User user = (User)var5.next();
            this.userDao.add(user);
        }

        try {
            txUserService.upgradeLevels();
            Assert.fail("TestUserServiceException expected");
        } catch (UserServiceTest.TestUserServiceException var6) {
        }

        this.checkLevelUpgraded((User)this.users.get(1), false);
    }
}

...
public class TxProxyFactoryBean implements FactoryBean<Object> {
    Object target;
    PlatformTransactionManager transactionManager;
    String pattern;
    Class<?> serviceInterface;

    public TxProxyFactoryBean() {
    }

    public void setTarget(Object target) {
        this.target = target;
    }

    public void setTransactionManager(PlatformTransactionManager transactionManager) {
        this.transactionManager = transactionManager;
    }

    public void setPattern(String pattern) {
        this.pattern = pattern;
    }

    public void setServiceInterface(Class<?> serviceInterface) {
        this.serviceInterface = serviceInterface;
    }

    public Object getObject() throws Exception {
        TransactionHandler txHandler = new TransactionHandler();
        txHandler.setTarget(this.target);
        txHandler.setTransactionManager(this.transactionManager);
        txHandler.setPattern(this.pattern);

        return Proxy.newProxyInstance(this.getClass().getClassLoader(), new Class[]{this.serviceInterface}, txHandler);
    }

    public Class<?> getObjectType() {
        return this.serviceInterface;
    }

    public boolean isSingleton() {
        return false;
    }
}

...
public class TransactionHandler implements InvocationHandler {
    Object target;
    PlatformTransactionManager transactionManager;
    String pattern;

    public TransactionHandler() {
    }

    public void setTarget(Object target) {
        this.target = target;
    }

    public void setTransactionManager(PlatformTransactionManager transactionManager) {
        this.transactionManager = transactionManager;
    }

    public void setPattern(String pattern) {
        this.pattern = pattern;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return method.getName().startsWith(this.pattern) ? this.invokeInTransaction(method, args) : method.invoke(this.target, args);
    }

    private Object invokeInTransaction(Method method, Object[] args) throws Throwable {
        TransactionStatus status = this.transactionManager.getTransaction(new DefaultTransactionDefinition());

        try {
            Object ret = method.invoke(this.target, args);
            this.transactionManager.commit(status);
            return ret;
        } catch (InvocationTargetException var5) {
            this.transactionManager.rollback(status);
            throw var5.getTargetException();
        }
    }
}
```

```xml
<bean id="userService" class="springbook.user.service.TxProxyFactoryBean">
   <property name="target" ref="userServiceImpl" />
   <property name="transactionManager" ref="transactionManager" />
   <property name="pattern" value="upgradeLevels" />
   <property name="serviceInterface" value="springbook.user.service.UserService" />
</bean>

<bean id="userServiceImpl" class="springbook.user.service.UserServiceImpl">
   <property name="userDao" ref="userDao" />
   <property name="mailSender" ref="mailSender" />
</bean>
```

#### 프록시 팩토리 빈 방식의 장점과 한계

* 장점
  * 프록시 팩토리 빈의 재사용성 : 이제 트랜잭션이 필요한 모든 클래스에 코드 변경없이 빈 설정정보만 추가함으로써 부가기능을 추가할 수 있게 된다. (위의 xml 코드 참고)
  * 프록시를 적용할 대상이 구현하고 있는 인터페이스를 구현하는 프록시 클래스를 일일히 만들지 않아도 된다. invocationHandler 만 이용하면 된다.
  * 부가적인 기능이 여러 메소드에 반복적으로 나타나지 않아도 된다. invocationHandler 에만 적용시켜주면 된다.
* 한계
  * 메소드 단위로 일어나는 일이기 때문에 한 번에 여러개의 클래스에 공통적인 부가기능을 제공하는 일이 불가하다.
  * 하나의 타깃에 여러개의 부가기능을 적용하려고 할 때도 같은 문제가 있다. 비록 코드의 재사용성은 높였지만, 빈 설정정보가 엄청나게 늘어나게 된다. 심지어는 이 설정정보도 타깃과 인터페이스만 다른 채로 같은 방식의 코드가 중복된다.
* 어떻게 해결할까?
  * 스프링의 프록시 팩토리 빈



{% hint style="info" %}
다음편에 계속!&#x20;
{% endhint %}

