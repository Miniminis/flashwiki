# 다이내믹 프록시

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

## 1. 프록시 패턴의 문제점&#x20;

* [undefined-4.md](undefined-4.md "mention")에서 프록시 패턴을 알아보았고, 그것의 단점을 알 수 있었다. 매번 타입에 맞는 프록시 클래스를 생성하는 방식이 아니라, 런타임에 클래스 타입에 맞는 프록시 객체를 생성하는 방법을 알아본다.&#x20;

## 2. 코드로 확인하기&#x20;

```java
class UserServiceTest {

    private UserService userService;
    private final User user = new User("flash");

    @Test
    void dynamicProxy() {
        userService = (UserService) Proxy.newProxyInstance(UserService.class.getClassLoader(), new Class[]{UserService.class}, new InvocationHandler() {
            UserService userService = new DefaultUserService();

            @Override
            public Object invoke(Object o, Method method, Object[] args) throws Throwable {
                if (method.getName().equals("join")) {  //join() 에만 proxy 적용
                    System.out.println("dynamic proxy start");
                    Object invoke = method.invoke(userService, args);
                    System.out.println("dynamic proxy end");
                    return invoke;
                }

                return method.invoke(userService, args);    //나머지 메소드는 proxy 적용 안됨
            }
        });

        userService.join(user);
        userService.terminate(user);
    }
    ...
}
```



## 3. 문제점&#x20;

* java 에서 제공해주는 dynamic proxy 는 모든 매소드에 일괄적으로 적용되며 일부 매소드에만 적용하고 싶을 때에는 위의 코드처럼 if 분기를 해주어 수동적으로 설정해야한다. 확장성이 매우 안좋고 유연하지 못한 구조라고 할 수 있다.&#x20;
  * 그래서 스프링 AOP 가 등장하게 되었다. 자세항 내용은 스프링 AOP 파트 참고!&#x20;
* 또 하나의 문제점은 위처럼 인터페이스 타입의 프록시만 제공하지, 클래스 타입의 프록시는 제공하지 않는다는 점이다.&#x20;
  * 이 부분은 우회적인 방법으로 접근할 수 있다. -> [undefined-2.md](undefined-2.md "mention")  참고



## 4. 참고&#x20;

* [https://docs.oracle.com/javase/8/docs/technotes/guides/reflection/proxy.html](https://docs.oracle.com/javase/8/docs/technotes/guides/reflection/proxy.html)
* [https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html#newProxyInstance-java.lang.ClassLoader-java.lang.Class:A-java.lang.reflect.InvocationHandler-](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html#newProxyInstance-java.lang.ClassLoader-java.lang.Class:A-java.lang.reflect.InvocationHandler-)

