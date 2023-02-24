---
description: 자바의 다이내믹 프록시는 인터페이스 타입에만 적용 가능했다. 어떻게하면 클래스 타입의 프록시를 적용할 수 있을까?
---

# 클래스의 프록시

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

```java
public class UserClassService {

    void join(User user) {
        System.out.println("join = " + user);
    }

    void terminate(User user) {
        System.out.println("terminate = " + user);
    }

}
```

## 1. cglib 를 이용하는 방법&#x20;

```gradle
dependencies {
    // https://mvnrepository.com/artifact/cglib/cglib
    implementation 'cglib:cglib:3.3.0'
}
```

```java
@Test
void cglib() {
    MethodInterceptor handler = new MethodInterceptor() {

        private final UserClassService userClassService = new UserClassService();

        @Override
        public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
            if (method.getName().equals("terminate")) {
                System.out.println("proxy start");
                Object invoke = method.invoke(userClassService, args);
                System.out.println("proxy end");

                return invoke;
            }

            return method.invoke(userClassService, args);
        }
    };

    UserClassService userClassService = (UserClassService) Enhancer.create(UserClassService.class, handler);
    userClassService.join(user);
    userClassService.terminate(user);

    //join = User@6f306067
    //proxy start
    //terminate = User@6f306067
    //proxy end
}
```



## 2. byteBuddy library를 이용하는 방법

```gradle
dependencies {
    implementation 'net.bytebuddy:byte-buddy:1.14.0'
}
```

```java
@Test
void byteBuddy() throws NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
    Class<? extends UserClassService> proxyClass = new ByteBuddy()
            .subclass(UserClassService.class)
            .method(named("terminate"))
            .intercept(InvocationHandlerAdapter.of(new InvocationHandler() {
                UserClassService userClassService = new UserClassService();

                @Override
                public Object invoke(Object o, Method method, Object[] args) throws Throwable {
                    System.out.println("proxy start");
                    Object invoke = method.invoke(userClassService, args);
                    System.out.println("proxy end");

                    return invoke;
                }
            }))
            .make()
            .load(UserClassService.class.getClassLoader())
            .getLoaded();

    UserClassService userClassService = proxyClass.getConstructor(null).newInstance();
    userClassService.join(user);
    userClassService.terminate(user);
}
```



## 3. 한계&#x20;

* 위의 두 방식 모두 대상 클래스의 서브 클래스를 프록시로 만들어 사용하는 방법이라서 대상 클래스가 상속을 막아놓은 경우 사용할 수 없다.&#x20;
  * final 클래스인 경우&#x20;
  * private 생성자만 존재하는 경우&#x20;
* interface 가 존재하면 interface 를 통해서 구현하는 것이 확장성과 유연성 면에서 좋다. 따라서 인터페이스가 존재하면 인터페이스의 프록시를 만들어 사용하자.&#x20;

```java
//1. final 로 상속을 막은 경우
//public final class UserClassService {
public class UserClassService {

    //2. private 생성자로 상속을 막은 경우
    //private UserClassService() { }

    void join(User user) {
        System.out.println("join = " + user);
    }

    void terminate(User user) {
        System.out.println("terminate = " + user);
    }

}
```



## 4. 참고

* [https://github.com/cglib/cglib/wiki](https://github.com/cglib/cglib/wiki)
* [https://bytebuddy.net/#/](https://bytebuddy.net/#/)

