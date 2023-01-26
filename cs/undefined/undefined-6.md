# 추상 팩토리 매소드 패턴

## 1. 개념&#x20;

* 서로 관련있는 여러 객체를 만들어주는 인터페이스
* 구체적으로 어떤 클래스의 인스턴스를 사용하는지 감출 수 있다. &#x20;
  * 클라이언트 코드에서 구체적인 클래스 의존성을 제거한다.&#x20;

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p>인프런 백기선님의 &#x3C;<a href="https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4">코딩으로 학습하는 GoF의 디자인 패턴</a>></p></figcaption></figure>



## 2. 샌드위치 만들기 예시

* 패턴 적용 전에는 Customer 에서 직접 새로운 샌드위치 요소인 Beverage 와 Sidemenu 를 직접 생성해주었다.
* 패턴 적용 후에는 SandwichSidePartsFactory 를 통해 직접적으로 어떤 인스턴스가 생성되고 있는지를 숨겨주었다.&#x20;
* [코드보기 ](https://github.com/Miniminis/design-patterns/tree/fa715751b288d6012955794da5a35d9c59a9f5a6/src/main/java/me/whiteship/designpatterns/\_01\_creational\_patterns/\_02\_factory\_method\_practice)

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### 적용 전&#x20;

```java

public class Customer {
    public static void main(String[] args) {
        Sandwich sandwich = new Sandwich();
        sandwich.setBeverage(new ColaBeverage());
        sandwich.setSideMenu(new CookieSideMenu());
        System.out.println(sandwich);
    }

}
```

### 적용 후&#x20;

```java

public class BurgerKing {
    public static void main(String[] args) {
        Sandwich sandwich = new TunaSandwichFactory(new TunaSandwichSidePartsFactory()).createSandwich();
        System.out.println(sandwich.getBeverage().getClass());
        System.out.println(sandwich.getSideMenu().getClass());
    }
}
```

## 3. 실제 사례&#x20;

### 3-1. 자바

```java
public class DocumentBuilderFactoryExample {

    public static void main(String[] args) throws ParserConfigurationException, IOException, SAXException {
        DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
        DocumentBuilder builder = factory.newDocumentBuilder();
        Document document = builder.parse(new File("src/main/resources/config.xml"));
        System.out.println(document.getDocumentElement());
    }
}
```



### 3-2. 스프링&#x20;

```java
@Configuration
public class FactoryBeanConfig {

    @Bean
    public ShipFactory shipFactory() {
        return new ShipFactory();
    }
}

public class FactoryBeanExample {

    public static void main(String[] args) {
//        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("config.xml");
//        Ship whiteship = applicationContext.getBean("whiteship", Ship.class);
//        System.out.println(whiteship.getName());

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(FactoryBeanConfig.class);
        Ship bean = applicationContext.getBean(Ship.class);
        System.out.println(bean);
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="hello" class="java.lang.String">
        <constructor-arg value="hello"/>
    </bean>

    <bean id="whiteship" class="me.whiteship.designpatterns._01_creational_patterns._03_abstract_factory._03_java.ShipFactory" />

</beans>
```
