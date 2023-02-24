# 프록시 패턴은 무엇인가

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

## 1. 개념&#x20;

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p><a href="https://velog.velcdn.com/images%2Fnewtownboy%2Fpost%2F7eb43ce0-64a3-426c-a6c0-0fb82a4e7f2f%2Fimage.png">https://velog.velcdn.com/images%2Fnewtownboy%2Fpost%2F7eb43ce0-64a3-426c-a6c0-0fb82a4e7f2f%2Fimage.png</a></p></figcaption></figure>

* 프록시 패턴은 위와 같이 구성된다.&#x20;
  * Proxy 클래스와 RealSubject 클래스가 공통의 부모로 인터페이스를 가지고 있다.&#x20;
  * 클라이언트는 인터페이스 타입, Proxy 를 거쳐 RealSubject 에 접근한다.&#x20;
  * 즉, Client -> Interface -> Proxy -> RealSubject 순서로 접근한다.&#x20;
    * RealSubject 에 접근할 필요가 없을 때에는 Proxy 에서 바로 리턴하기도 한다.&#x20;
* 장점&#x20;
  * RealSubject 에서는 그가 맡은 단일 책임에만 집중하면 된다. (SRP)&#x20;
  * Proxy 에서는 RealSubject 에 필요한 접근제한, 로깅, 트랜잭션 등 부가적인 기능을 제공한다. &#x20;

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption><p><a href="https://docs.firstdecode.com/wp-content/uploads/2019/10/Pattern_MSAProxy.png">https://docs.firstdecode.com/wp-content/uploads/2019/10/Pattern_MSAProxy.png</a></p></figcaption></figure>



## 2. 코드로 살펴보자&#x20;

```java
public class User {

    private String name;

    public User(String name) {
        this.name = name;
    }
}


public interface UserService {

    void join(User user);

}


public class DefaultUserService implements UserService {
    @Override
    public void join(User user) {
        System.out.println("user from DefaultUSerService = " + user);
    }
}


public class ProxyUserService implements UserService {

    private final UserService userService = new DefaultUserService();

    @Override
    public void join(User user) {
        System.out.println("proxy start");
        userService.join(user);
        System.out.println("proxy end");
    }
}

```

```java
import org.junit.jupiter.api.Test;

class UserServiceTest {

    private UserService userService;

    @Test
    void defaultUserService() {
        userService = new DefaultUserService();
        userService.join(new User("flash"));
        //user from DefaultUSerService = User@6b943504
    }

    @Test
    void proxyUserService() {
        userService = new ProxyUserService();
        userService.join(new User("flash"));
        //proxy start
        //user from DefaultUSerService = User@1a13a1be
        //proxy end
    }
}
```



## 3. 문제점&#x20;

* 일반적인 프록시 패턴을 이용하게 되면, 각 서비스마다 그에 해당하는 프록시가 하나씩 생성되어, 너무 많은 클래스가 생성되고, 그 내부의 코드도 중복된다는 문제점이 발생하게 된다.&#x20;
* 이를 효과적으로 해결하기 위해 reflection api 에서는 다이내믹 프록시라는 개념을 지원한다.&#x20;
* 다이내믹 프록시를 이용해서 컴파일 타임에 미리 만들어진 클래스가 아니라, 런타임에 유동적으로 클래스 타입에 맞는 프록시 객체를 만들어보자.&#x20;



## 4. 참고&#x20;

* [https://www.oodesign.com/proxy-pattern](https://www.oodesign.com/proxy-pattern)
* [https://en.wikipedia.org/wiki/Proxy\_pattern](https://en.wikipedia.org/wiki/Proxy\_pattern)
* [https://en.wikipedia.org/wiki/Single-responsibility\_principle](https://en.wikipedia.org/wiki/Single-responsibility\_principle)&#x20;
