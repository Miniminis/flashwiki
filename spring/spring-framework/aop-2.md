# AOP 2

> 자바, 스프링 개발자들의 종착역이자, 주기적으로 다시 돌아오는 그곳, <[토비의 스프링 3.1](http://www.yes24.com/Product/Goods/7516911)>을 드디어 읽는다.&#x20;
>
> 한창 개발 걸음마를 막 떼고 스프링으로 아장아장 기어다니며 CRUD를 할 때즈음, 토비의 스프링을 찬양하고 있는 많은 개발자들을 보았고, 역시 지식이 깊어지려면, 책을 통해서 지식을 정리하고 깊이를 다지는 시간이 필요하겠구나, 생각했다. 하지만, 개발을 이제 막 배운 그때의 나에게 토비 책은 너무나도 무시무시했고, 토비책을 읽다가 개발을 포기한 사람들의 증언도 여럿 읽고 나니, 이 책은 개발자로서 레베루업이 필요할 때즈음, 다시 꺼내봐야겠다고 생각했다.&#x20;
>
> 그리고 지금이다. 퇴사 후, 이직을 준비하는 동안, 지식의 깊이를 좀 더 다져놓고 싶다는 생각이 들었고, 집앞 도서관에서 (아무도 빌려가지 않는) 토비님의 책을 빌려서 읽기 시작했다. 처음에는 가볍게 읽기 시작했지만, 읽을 수록 인사이트가 쌓여, 기록하면서 제대로 읽고 싶어졌다. 그래서 나의 세컨 브레인인 이곳 블로그에 짧은 글들로 그 내용을 적어보며, 토비님으로부터 얻은 인사이트를 내것으로 만들어보고자 한다. &#x20;

## 스프링 프록시 팩토리 빈

### ProxyFactoryBean

```java
public class DynamicProxyTest {
   @Test
   public void simpleProxy() {
      ...
      
      //jdk 다이내믹 프록시 생성 
      Hello proxiedHello = (Hello)Proxy.newProxyInstance(
            getClass().getClassLoader(), 
            new Class[] { Hello.class},
            new UppercaseHandler(new HelloTarget()));
      

      assertThat(proxiedHello.sayHello("Toby"), is("HELLO TOBY"));
      assertThat(proxiedHello.sayHi("Toby"), is("HI TOBY"));
      assertThat(proxiedHello.sayThankYou("Toby"), is("THANK YOU TOBY"));
   }
   

	@Test
	public void proxyFactoryBean() {
		ProxyFactoryBean pfBean = new ProxyFactoryBean();
		pfBean.setTarget(new HelloTarget()); //타깃 설정
		pfBean.addAdvice(new UppercaseAdvice());  //부가기능을 담은 어드바이스 추가. 여러 개 가능

		Hello proxiedHello = (Hello) pfBean.getObject();  //factoryBean 이므로 getObject() 로 생성된 프록시 가져온다. 
		
		assertThat(proxiedHello.sayHello("Toby"), is("HELLO TOBY"));
		assertThat(proxiedHello.sayHi("Toby"), is("HI TOBY"));
		assertThat(proxiedHello.sayThankYou("Toby"), is("THANK YOU TOBY"));
	}
	
	static class UppercaseAdvice implements MethodInterceptor {
		public Object invoke(MethodInvocation invocation) throws Throwable {
			//리플렉션의 Method 와 다르게, 매소드 실행시, 다깃 오브젝트를 전달할 필요가 없다. MethodInvocation 내부에 매소드 정보와 함께 타깃 오브젝트를 알고 있기 떄문이다. 
			String ret = (String)invocation.proceed();  
			return ret.toUpperCase();
		}
	}
}
```

#### Advice

* 타깃이 필요없는 순수한 부가기능&#x20;
* invocationHandler 를 구현했을 때와 다르게 methodInterceptor 를 구현할 때는 타깃 오브젝트가 등장하지 않는다.&#x20;
  * methodInvocation 내부에는 메소드 정보 + 타깃 오브젝트 가 담겨있기 때문이다. 이는 타깃 오브젝트의 메소드를 실행할 수 있는 권한이 있기 때문에, 순수하게 부가기능에만 집중할 수 있는 것이다. &#x20;
  * ```java
    //참고 
    static class UppercaseHandler implements InvocationHandler {
       Object target;

       private UppercaseHandler(Object target) {
          this.target = target;
       }

       //InvocationHandler 에서는 타깃 오브젝트가 등장한다. 
       public Object invoke(Object proxy, Method method, Object[] args)
             throws Throwable {
          Object ret = method.invoke(target, args);
          if (ret instanceof String && method.getName().startsWith("say")) {
             return ((String)ret).toUpperCase();
          }
          else {
             return ret;
          }
       }
    }
    ```
* MethodInvocation&#x20;
  * 일종의 콜백 오브젝트. proceed() 메소드를 실행하면 타깃 오브젝트의 매소드를 내부적으로 실행해주는 기능이 있다.&#x20;
  * proceed() 로 타깃 오브젝트의 매소드를 특정하지 않고 실행할 수 있기 때문에, methodInvocation 은 공유가능한 템플릿처럼 사용될 수 있다.&#x20;
  * 이 점이 JDK 다이나맥 프록시를 직접 사용하는 것 vs. 스프링 제공의 ProxyFactoryBean 을 사용하는 코드의 차이점이자 장점.&#x20;
  * 마치 jdbtTemplate 이 SQL 의 파라미터에 종속되지 않기 때문에 많은 DAO 가 하나의 JdbcTemplate 오브젝트를 공유해서 사용할 수 있는 것과 마찬가지!&#x20;
* addAdvice()&#x20;
  * ProxyFactoryBean 에는 여러개의 부가기능을 담은 MethodInterceptor 들을 쉽게 추가할 수 있다. 별도로 빈을 따로 생성하고 또 등록하지 않아도, proxyFactoryBean 하나로 해결 가능하다.&#x20;
* Advice(메인 인터페이스) <- MethodInterceptor (서브 인터페이스)
* MethodInterceptor 처럼 타깃 오브젝트에 적용하는 부가기능을 담은 오브젝트를 스프링에서는 `어드바이스`라고 부른다.&#x20;
* Hello 인터페이스가 사라지는 점도 주목해야한다.&#x20;
  * ProxyFactoryBean 도 setInterface() 를 통해서 직접 인터페이스를 지정해줄 수도 있다.&#x20;
  * 하지만 내부에서는 인터페이스 자동검출기능을 사용해서 타깃 오브젝트가 구현하고 있는 인터페이스 정보를 알아내고 이를 모두 구현하는 프록시를 만들어준다. 때문에 별도로 선언해주지 않아도 자동으로 생성되는 것이다.&#x20;

#### JDK 다이내믹 프록시 vs. 스프링의 ProxyFactoryBean&#x20;

<table><thead><tr><th>JDK 다이내믹 프록시</th><th>스프링의 ProxyFactoryBean</th><th data-hidden></th></tr></thead><tbody><tr><td>InvocationHandler</td><td>MethodInterceptor</td><td></td></tr><tr><td>타깃 명시</td><td>타깃 내부의 매소드를 proceed() 라는 메소드 하나로 실행할 수 있음 </td><td></td></tr><tr><td>구현해야할 인터페이스 명시</td><td>구현해야할 인터페이스 명시안함</td><td></td></tr><tr><td>새로운 부가기능 추가시마다 프록시와 프록시 팩토리빈 추가해주어야 함 </td><td>하나의 빈에 addAdvice()로 계속 부가기능을 추가할 수 있음 </td><td></td></tr></tbody></table>



#### 포인트컷

* 기존의 InvocationHandler 의 경우, 부가기능을 부여하는 코드 + 적용할 메소드 선정 알고리즘 이 함께 섞여있는 구조였다.&#x20;
  * 이렇게 될 경우, 부가기능을 여러 군데에 적용하기 위해서는 메소드 선정 알고리즘을 계속 수정해야하고,&#x20;
  * 메소드 선정 알고리즘이 수정되면, 부가기능을 부여하는 코드에도 영향이 가는, 전형적인 OCP 를 위반한 구조였다.&#x20;
  * 이 경우, 부가기능을 부여하는 기능과 적용할 범위를 선정하는 기능을 아예 별도의 빈으로 분리하여 구현하는 편이 좋다. 그래야 적절하게 기능들을 재사용하며, 갈아끼워서 적용할 수도 있기 때문이다.&#x20;
* 따라서 스프링의 ProxyFactoryBean 방식의 경우는 부가기능을 제공하는 오브젝트를 어드바이스, 메소드 선정 알고리즘을 담은 오브젝트를 포인트 컷이라고 나누었고, 아래와 같이 동작하도록 만들었다.&#x20;
  * 두 오브젝트 모두 DI 를 통해 프록시에 주입된다. 그래서 여러 프록시에 공유 될 수 있으며 당연히 싱글톤이다.&#x20;
  * 프록시가 클라이언트로부터 요청을 받으면, 우선 포인트 컷에게 부가기능을 부여할 메소드인지 아닌지 확인해달라고 요청한다.&#x20;
  * 포인트 컷이 확인 결과를 프록시에게 전달한다.&#x20;
  * 프록시는 MethodInterceptor 타입의 어드바이스를 호출한다.&#x20;
  * 어드바이스는 JDK 다이내믹 프록시의 InvocationHandler 와는 다르게 직접 타깃을 호출하지 않는다. 일종의 템플릿/콜백 구조로 구현되어있기 때문에 MethodInvocation 타입 콜백 오브젝트의 proceed() 메소드를 호출해주기만 하면 된다. &#x20;
  * Invocation 콜백은 실제 위임 대상이되는 타깃 오브젝트의 레퍼런스를 가지고 있고, 타깃 메소드를 직접 호출할 수 있다.&#x20;

```java
...
import org.springframework.aop.support.NameMatchMethodPointcut;

public class DynamicProxyTest {
   @Test
   public void pointcutAdvisor() {
      ProxyFactoryBean pfBean = new ProxyFactoryBean();
      pfBean.setTarget(new HelloTarget());
      
      //메소드 이름을 비교해서 대상을 선정하는 알고리즘을 제공하는 포인트 컷을 생성
      NameMatchMethodPointcut pointcut = new NameMatchMethodPointcut();
      pointcut.setMappedName("sayH*"); 
      
      pfBean.addAdvisor(new DefaultPointcutAdvisor(pointcut, new UppercaseAdvice()));
      
      Hello proxiedHello = (Hello) pfBean.getObject();
      
      assertThat(proxiedHello.sayHello("Toby"), is("HELLO TOBY"));
      assertThat(proxiedHello.sayHi("Toby"), is("HI TOBY"));
      assertThat(proxiedHello.sayThankYou("Toby"), is("Thank You Toby")); 
      //마지막 메소드만 이름이 sayH 로 시작하기 않기 때문에 적용이 안된 것을 알 수 있다. 
   }

}

```



#### addAdvisor()&#x20;

```java
pfBean.addAdvisor(new DefaultPointcutAdvisor(pointcut, new UppercaseAdvice()));
```

* 어드바이저(Advisor) = 포인트컷(메소드 선정 알고리즘) + 어드바이스(부가기능)
* 포인트컷+어드바이스를 등록할 때에는 어드바이저를 이용하여 묶어서 등록한다. 포인트컷과 어드바이스 모두 싱글톤으로 구현되어 여러 곳에서 쓰일 수 있다. 어떤 메소드에 어떤 부가기능이 부여되어야 하는지 지정해주어야 한다.&#x20;



### ProxyFactoryBean 적용&#x20;

```xml
<bean id="transactionAdvice" class="springbook.user.service.TransactionAdvice">
   <property name="transactionManager" ref="transactionManager" />
</bean>

<bean id="transactionPointcut" class="org.springframework.aop.support.NameMatchMethodPointcut">
   <property name="mappedName" value="upgrade*" />
</bean>

<bean id="transactionAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
   <property name="advice" ref="transactionAdvice" />
   <property name="pointcut" ref="transactionPointcut" />
</bean>

...

<bean id="userService" class="org.springframework.aop.framework.ProxyFactoryBean">
	<property name="target" ref="userServiceImpl" />
	<property name="interceptorNames">
		<list>
			<value>transactionAdvisor</value>
		</list>
	</property>
</bean>
```

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="/test-applicationContext.xml")
public class UserServiceTest {

   @Test 
   @DirtiesContext
   public void upgradeAllOrNothing() {
      TestUserService testUserService = new TestUserService(users.get(3).getId());
      testUserService.setUserDao(userDao);
      testUserService.setMailSender(mailSender);
      
      //ProvyFactoryBean 으로 교체해준다. 
      ProxyFactoryBean txProxyFactoryBean = 
         context.getBean("&userService", ProxyFactoryBean.class);
      txProxyFactoryBean.setTarget(testUserService);
      UserService txUserService = (UserService) txProxyFactoryBean.getObject();
             
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





## 스프링 AOP

### 자동 프록시 생성

* 기존의 프록시 팩토리 빈 방식의 접근 방법에서 한계는 두가지였다.&#x20;
  * 부가 기능이 타깃 오브젝트마다 새로 만들어지는 문제
    * 스프링 ProxyFactoryBean 의 어드바이스를 통해 해결
  * 부가 기능의 적용이 필요한 타깃 오브젝트마다 거의 비슷한 내용의 ProxyFactoryBean 설정 정보를 추가해주는 문제
    * 이 부분은 아직 해결되지 않았다.&#x20;
    * ```xml
      <bean id="userService" class="org.springframework.aop.framework.ProxyFactoryBean">
         <property name="target" ref="userServiceImpl" />
         <property name="interceptorNames">
            <list>
               <value>transactionAdvisor</value>
            </list>
         </property>
      </bean>
      ```
    * 타깃 프로퍼티만 제외한다면, 빈 클래스의 종류, 어드바이스, 포인트컷의 설정이 동일한데, 이런 중복은 어떻게 해결이 안되는 것인가...!!!!&#x20;

#### 빈 후처리기를 이용한 자동 프록시 생성기

* BeanPostProcessor 인터페이스를 구현해서 빈 후처리기를 만든다.&#x20;
* 스프링 빈 오브젝트로 만들어지고 난 후에 다시 빈 오브젝트를 가공할 수 있게 해준다.&#x20;
* DefaultAdvisorAutoProxyCreator : 어드바이저를 이용한 자동 프록시 생성기
* 동작방식 - 결국 빈 후처리기에서 ProxyFactoryBean 빈 프록시 오브젝트를 바꿔치기하는 방법이다.&#x20;
  * DefaultAdvisorAutoProxyCreator 빈 후처리기가 등록되어있다면, 빈 오브젝트를 만들때마다 후처리기에게 빈을 보낸다.&#x20;
  * 빈으로 등록된 모든 어드바이저 내의 포인트컷을 이용해서 전달받은 빈이 프록시 적용 대상인지 확인한다.&#x20;
  * 적용 대상이면, 내장된 프록시 생성기에게 현재 빈에 대한 프록시를 만들게 하고, 만들어진 프록시에 어드바이저를 연결해준다.&#x20;
  * 빈 후처리기는 프록시가 생성되면 원래 컨테이너가 전달해준 빈 오브젝트 대신에 프록시 오브젝트를 컨테이너에게 돌려준다.&#x20;
  * 컨테이너는 최종적으로 빈 후처리기가 돌려주는 오브젝트를 빈으로 등록하고 사용한다.&#x20;



#### 포인트컷 동작방식 테스트

* 목표
  * 포인트컷 적용 클래스를 고를 수 있도록 하는 기능 추가
  * 프록시 적용 후보 클래스를 여러개 만들어두고 이 포인트컷을 적용한 ProxyFactoryBean 으로 프록시를 만들도록 해서 어드바이스가 적용되는지 확인&#x20;
* 결과&#x20;
  * HelloWorld 를 제외하고 클래스 패턴 타입 HelloT 에 맞는 클래스인 HelloTarget, HelloToby 는 어드바이스가 적용된 것을 확인할 수 있다. &#x20;

```java
public class DynamicProxyTest {

	@Test
	public void classNamePointcutAdvisor() {
	   NameMatchMethodPointcut classMethodPointcut = new NameMatchMethodPointcut() {  
	      public ClassFilter getClassFilter() {
	         return new ClassFilter() {
	            public boolean matches(Class<?> clazz) {
	               //class 단위의 규칙 적용 
	               return clazz.getSimpleName().startsWith("HelloT");
	            }
	         };
	      }
	   };
	   classMethodPointcut.setMappedName("sayH*");   //원래 있었던 메소드 단위의 규칙 그대로 적용 
	
	   checkAdviced(new HelloTarget(), classMethodPointcut, true);  
	
	   class HelloWorld extends HelloTarget {};
	   checkAdviced(new HelloWorld(), classMethodPointcut, false);  
	   
	   class HelloToby extends HelloTarget {};
	   checkAdviced(new HelloToby(), classMethodPointcut, true);
	}
	
	private void checkAdviced(Object target, Pointcut pointcut, boolean adviced) { 
		ProxyFactoryBean pfBean = new ProxyFactoryBean();
		pfBean.setTarget(target);
		pfBean.addAdvisor(new DefaultPointcutAdvisor(pointcut, new UppercaseAdvice()));
		Hello proxiedHello = (Hello) pfBean.getObject();
		
		if (adviced) {
			assertThat(proxiedHello.sayHello("Toby"), is("HELLO TOBY"));
			assertThat(proxiedHello.sayHi("Toby"), is("HI TOBY"));
			assertThat(proxiedHello.sayThankYou("Toby"), is("Thank You Toby"));
		}
		else {
			assertThat(proxiedHello.sayHello("Toby"), is("Hello Toby"));
			assertThat(proxiedHello.sayHi("Toby"), is("Hi Toby"));
			assertThat(proxiedHello.sayThankYou("Toby"), is("Thank You Toby"));
		}
	}

}

```



### DefaultAdvisorAutoProxyCreator 적용

```java
//class filter 상속하여 클래시 이름에 매치되는 것 구분하는 필터 추가 
public class NameMatchClassMethodPointcut extends NameMatchMethodPointcut {
   public void setMappedClassName(String mappedClassName) {
      this.setClassFilter(new SimpleClassFilter(mappedClassName));
   }
   
   static class SimpleClassFilter implements ClassFilter {
      String mappedName;
      
      private SimpleClassFilter(String mappedName) {
         this.mappedName = mappedName;
      }

      public boolean matches(Class<?> clazz) {
         return PatternMatchUtils.simpleMatch(mappedName, clazz.getSimpleName());
      }
   }
}
```

```xml
<bean id="transactionAdvisor" class="org.springframework.aop.support.DefaultPointcutAdvisor">
   <property name="advice" ref="transactionAdvice" />
   <property name="pointcut" ref="transactionPointcut" />
</bean>

<bean id="transactionAdvice" class="springbook.user.service.TransactionAdvice">
	<property name="transactionManager" ref="transactionManager" />
</bean>

<bean id="transactionPointcut" class="springbook.user.service.NameMatchClassMethodPointcut">
	<property name="mappedClassName" value="*ServiceImpl" />
	<property name="mappedName" value="upgrade*" />
</bean>
```

* mappedClassName 으로 패턴 추가

```xml
<bean id="userService" class="springbook.user.service.UserServiceImpl">
   <property name="userDao" ref="userDao" />
   <property name="mailSender" ref="mailSender" />
</bean>
```

* UserServiceImpl 은 다시 userService 로 아이디 이름을 갖게 되었고, 그럼에도 불구하고 자동 프록시 생성으로 인해 어드바이스가 적용되어있어야 한다.&#x20;

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="/test-applicationContext.xml")
public class UserServiceTest {

   @Autowired UserService userService;	
   @Autowired UserService testUserService;
   
   @Test 
   public void upgradeAllOrNothing() {
   	userDao.deleteAll();			  
   	for(User user : users) userDao.add(user);
   	
   	try {
   		testUserService.upgradeLevels();   
   		fail("TestUserServiceException expected"); 
   	}
   	catch(TestUserServiceException e) { 
   	}
   	
   	checkLevelUpgraded(users.get(1), false);
   }

   static class TestUserServiceImpl extends UserServiceImpl {
      private String id = "madnite1"; // users(3).getId()
      
      protected void upgradeLevel(User user) {
         if (user.getId().equals(this.id)) throw new TestUserServiceException();  
         super.upgradeLevel(user);  
      }
   }
   	
}
```

```xml
<bean 
    id="testUserService" 
    class="springbook.user.service.UserServiceTest$TestUserServiceImpl" 
    parent="userService" />
```

* 포인트컷 클래스 매칭 방식에 적용되도록 TestUserService 클래스 이름 변경.
* 기존 생성자 방식에서 바꿔치기하는 대신에 DI 되도록 빈 등록



#### 자동 프록시 확인하기&#x20;

* 간단한 테스트를 위해서 포인트 컷 적용 클래스 패턴을 `*NotServiceImpl`로 바꾸어본다.
* 아니면 프록시로 변경된 것인지 직접 클래스 타입을 확인해본다. &#x20;

```xml
<bean id="transactionPointcut" class="springbook.user.service.NameMatchClassMethodPointcut">
   <property name="mappedClassName" value="*NotServiceImpl" />
   <property name="mappedName" value="upgrade*" />
</bean>
```

```java
@Test
public void advisorAutoProxyCreator() {
    assertThat(testUserService, is(java.lang.reflect.Proxy.class));
}
```

&#x20;

### 포인트컷 표현식을 이용한 포인트 컷

* 포인트 컷 적용 대상을 지금까지는 클래스 이름 패턴과 매소드 이름 패턴 정도로만 구분해왔는데, 좀 더 세밀하게 구분하고자 한다.&#x20;
* 이는 포인트컷 표현식을 통해서 가능하다.&#x20;
  * 포인트컷 표현식 : 정규식이나 JSP의 EL 과 비슷한 일종의 표현식 언어를 사용해서 포인트컷을 작성할 수 있도록 하는 방법
* AspectJExpressionPointcut&#x20;
  *



### AOP&#x20;

### AOP 적용기술&#x20;



## 트랜잭션 속성

### 트랜잭션 정의

### 트랜잭션 인터셉터와 트랜잭션 속성

### 포인트컷과 트랜젹샌 속성의 적용 전략&#x20;

### 트랜잭션 속성 적용&#x20;



## 애노테이션 트랜잭션 속성과 포인트 컷

### 트랜잭션 애노테이션&#x20;

### 트랜잭션 애노테이션 적용 &#x20;



## 트랜잭션 지원 테스트

### 선언적 트랜잭션과 트랜잭션 전파 속성&#x20;

### 트랜잭션 동기화와 테스트&#x20;

### 테스트를 위한 트랜잭션 애노테이션&#x20;



## 정리



