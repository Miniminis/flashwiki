# reflection api 이용하여 spring ioc container 만들기

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}



## 1. 이해하기&#x20;

### 1-1. project : container-flash&#x20;

1. @Inject annotation 을 만들고&#x20;
2. FlashContainer 를 만든다.&#x20;
   1. getObject() 에서는&#x20;
      1. 리플렉션 API 를 이용하여 생성자 및 인스턴스를 만들고&#x20;
      2. @Inject annotation 이 붙은 인스턴스 필드가 있다면, 생성된 인스턴스에 주입시켜주는 역할을 수행한다.
3. `gradle clean build` 하여 외부 프로젝트에서 의존성 추가하여 사용할 수 있도록 한다. &#x20;

### 1-2. project : container-apply&#x20;

1. 신규 프로젝트를 생성하고, libs 폴더를 만들어 1-1 에서 생성한 jar 파일을 옮겨준다.&#x20;
2. build.gradle 에서 설정을 통해 의존성 설정을 해준다.&#x20;
3. Main 클래스에서 FlashConainer 를 통해 의존성 주입이 된 것을 확인한다. &#x20;



## 2. 코드&#x20;

### 2-1. project : container-flash&#x20;

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption><p>source tree 참고 </p></figcaption></figure>

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface Inject {
}
```

```java
public class FlashContainer {

    public static <T> T getObject(Class<T> classType) {
        T instance = createInstance(classType); //생성자 방식으로 클래스 타입에 대한 객체 생성

        Arrays.stream(classType.getDeclaredFields()).forEach(field -> {     //클래스에 정의된 필드들 중, Inject type 어노테이션이 붙어있는 필드 조회
            if (field.getAnnotation(Inject.class) != null) {
                Object injectedInstance = createInstance(field.getType());  //해당 필드에 대해서 객체를 만들고
                field.setAccessible(true);      //접근 제어자를 해제해준 뒤,
                try {
                    field.set(instance, injectedInstance);      //인스턴스에 해당 필드 타입의 인스턴스를 주입시켜준다.
                } catch (IllegalAccessException e) {
                    e.printStackTrace();
                }
            }
        });

        return instance;
    }

    private static <T> T createInstance(Class<T> classType) {
        try {
            return classType.getConstructor(null).newInstance();
        } catch (InstantiationException | IllegalAccessException | InvocationTargetException | NoSuchMethodException e) {
            throw new RuntimeException(e);
        }
    }
}
```

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertNotNull;

class FlashContainerTest {

    @Test
    void getObject_userRepository() {
        UserRepository userRepository = FlashContainer.getObject(UserRepository.class); //reflection 을 반환
        assertNotNull(userRepository);
    }

    @Test
    void getObject_userService() {
        UserService userService = FlashContainer.getObject(UserService.class);
        assertNotNull(userService);
        assertNotNull(userService.userRepository);
    }
}

public class UserRepository {

}

public class UserService {

    @Inject
    UserRepository userRepository;

}

```



```
//terminal 
gradle clean build 
```



### 2-2. project : container-apply&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

```java
public class Main {
    public static void main(String[] args) {
        CustomerService customerService = FlashContainer.getObject(CustomerService.class);
        customerService.join();

        //customerService:join
        //customerRepository:save
    } 
}
```

```java
public class CustomerService {

    @Inject CustomerRepository customerRepository;

    public void join() {
        System.out.println("customerService:join");
        customerRepository.save();
    }
}
```

```java
public class CustomerRepository {

    void save() {
        System.out.println("customerRepository:save");
    }
}
```



## 3. 참고&#x20;

* [https://www.baeldung.com/gradle-dependencies-local-jar](https://www.baeldung.com/gradle-dependencies-local-jar)

