# 팩토리 메소드 패턴

## 개념&#x20;

* 객체를 사용하는 코드에서 객체 생성 부분을 떼어내어 추상화한 패턴
* 상속 관계에 있는 두 클래스에서 상위 클래스가 중요한 뼈대를 결정하고, 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정한다.&#x20;
* 구체적으로 어떤 인스턴스를 만들지는 서브 클래스가 결정한다.&#x20;
* 다양한 구현체가 있고, 그 중에서 특정한 구현체를 만들 수 있는 다양한 팩토리를 제공할 수 있다.&#x20;

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>인프런 백기선님의 &#x3C;<a href="https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4">코딩으로 학습하는 GoF의 디자인 패턴</a>></p></figcaption></figure>

## 예시 1. 가장 단순한 형태의 팩토리 매서드 패턴 - 푸트코트 도시락&#x20;

```java

public class FoodCourt {
    public static void main(String[] args) {
        Dosirak defaultDosirak = DosirakFactory.order("Default", "spam");
        Dosirak spamDosirak = DosirakFactory.order("SpamDosirak", "spam");
        Dosirak tunaDosirak = DosirakFactory.order("TunaDosirak", "tuna");

        System.out.println("defaultDosirak = " + defaultDosirak);
        System.out.println("spamDosirak = " + spamDosirak);
        System.out.println("tunaDosirak = " + tunaDosirak);
    }
}


abstract class Dosirak {
    public abstract String mainIngredient();

    @Override
    public String toString() {
        return "Here is " + this.mainIngredient();
    }
}

class DosirakFactory {
    public static Dosirak order(String type, String mainIngredient){
        if ("SpamDosirak".equalsIgnoreCase(type)) {
            return new SpamDosirak(mainIngredient);
        }

        if ("TunaDosirak".equalsIgnoreCase(type)) {
            return new TunaDosirak(mainIngredient);
        }

        return new DefaultDosirak();
    }
}

class SpamDosirak extends Dosirak {

    private final String main;

    public SpamDosirak(String main) {
        this.main = main;
    }

    @Override
    public String mainIngredient() {
        return this.main;
    }
}

class TunaDosirak extends Dosirak {

    private final String main;

    public TunaDosirak(String main) {
        this.main = main;
    }

    @Override
    public String mainIngredient() {
        return this.main;
    }
}


class DefaultDosirak extends Dosirak {

    private final String main;

    public DefaultDosirak() {
        this.main = "김치";
    }

    @Override
    public String mainIngredient() {
        return this.main;
    }
}

/**
 * defaultDosirak = Here is 김치
 * spamDosirak = Here is spam
 * tunaDosirak = Here is tuna
 * */
```



## 예시 2. 조금 더 복잡한 형태의 패턴 - 배 만들기&#x20;

### 적용 전&#x20;

* [코드참고](https://github.com/Miniminis/design-patterns/tree/de523dc233cdb47463ac4d7c111eee35177e55e5/src/main/java/me/whiteship/designpatterns/\_01\_creational\_patterns/\_02\_factory\_method\_practice/\_01\_before)

![](<../../.gitbook/assets/image (2) (11).png>)

### 적용 후

* [코드참고](https://github.com/Miniminis/design-patterns/tree/de523dc233cdb47463ac4d7c111eee35177e55e5/src/main/java/me/whiteship/designpatterns/\_01\_creational\_patterns/\_02\_factory\_method\_practice/\_02\_after)
* product 에 해당하는 ship도, creator 에 해당하는 factory 도 모두 팩토리 매소드 패턴을 적용하여 변경 가능성은 줄이고, 확장가능성은 높인다.&#x20;

### ![](<../../.gitbook/assets/image (4) (1).png>)&#x20;

## 자세히보기&#x20;

* 팩토리 매소드 패턴을 적용했을 때의 장점과 단점
  * 기준점이 되는 ship 인터페이스나 shipFactory 클래스는 전혀 변경하지 않고도, 쉽게 새로운 유형인 BlueShip, BlackShip 등 을 추가할 수 있다. 즉, 확장에는 열려있고, 변경에는 닫혀있다. &#x20;
  * 팩토리 매소드 패턴을 사용하면 확장성이 좋지만, 동시에 해당 패턴을 사용하지 않았을 때보다 클래스 구조도는 훨씬 복잡해지게 된다.&#x20;
* 확장에 열려있고, 변경에 닫혀있는 객체지향의 원칙이란
  * 새로운 유형을 추가할 때, 기준이 되는 인터페이스나 클래스의 코드는 전혀 변경하지 않고, 기존의 유형에 상관없이 새로운 유형을 쉽게 추가할수 있는 구조를 말한다.  &#x20;
  * 기존 코드를 변경하지 않고, 기존 것을 확장하여 새로운 것을 만드는 것이 쉽다.
* 자바 8에 추가된 default 매소드에 대해서 설명&#x20;
  * 자바 8부터는 interface 에서 default 키워드를 붙인 매소드를 미리 구현해놓아 하위 클래스들에서 새롭게 구현하지 않을 경우, 호출되는 디폴트 매소드로 이용할 수 있다.&#x20;
  * 참고로 자바 9부터는 interface에서 private 키워드를 붙여 매소드를 구현해놓을 수 있다.&#x20;
  * 따라서 자바 9 이후로는 사실 추상매소드 쓸 일이 별로 없다.&#x20;



## 실제 예시&#x20;

### 자바&#x20;

* 실제 예시에서는 위의 예시 1에서처럼 단순한 팩토리 패턴이 많이 쓰인다. 매개변수의 값에 따라서 내부에서 분기처리하여 서로 다른 인스턴스를 반환하는 구조.&#x20;
* 예를 들면, 자바의 java.lang.Calendar, java.lang.NumberFormat 등이 그러하다. &#x20;

```java
public class SimpleFactory {

    public Object createProduct(String name) {
        if (name.equals("whiteship")) {
            return new Whiteship();
        } else if (name.equals("blackship")) {
            return new Blackship();
        }

        throw new IllegalArgumentException();
    }
}
```

<pre class="language-java"><code class="lang-java">public abstract class Calendar implements Serializable, Cloneable, Comparable&#x3C;Calendar> {

    ...
<strong>
</strong><strong>    private static Calendar createCalendar(TimeZone zone, Locale aLocale) {
</strong>        CalendarProvider provider = LocaleProviderAdapter.getAdapter(CalendarProvider.class, aLocale).getCalendarProvider();
        if (provider != null) {
            try {
                return provider.getInstance(zone, aLocale);
            } catch (IllegalArgumentException var7) {
            }
        }
    
        Calendar cal = null;
        if (aLocale.hasExtensions()) {
            String caltype = aLocale.getUnicodeLocaleType("ca");
            if (caltype != null) {
                byte var6 = -1;
                switch(caltype.hashCode()) {
                case -1581060683:
                    if (caltype.equals("buddhist")) {
                        var6 = 0;
                    }
                    break;
                case -752730191:
                    if (caltype.equals("japanese")) {
                        var6 = 1;
                    }
                    break;
                case 283776265:
                    if (caltype.equals("gregory")) {
                        var6 = 2;
                    }
                }
    
                switch(var6) {
                case 0:
                    cal = new BuddhistCalendar(zone, aLocale);
                    break;
                case 1:
                    cal = new JapaneseImperialCalendar(zone, aLocale);
                    break;
                case 2:
                    cal = new GregorianCalendar(zone, aLocale);
                }
            }
        }
    
        if (cal == null) {
            if (aLocale.getLanguage() == "th" &#x26;&#x26; aLocale.getCountry() == "TH") {
                cal = new BuddhistCalendar(zone, aLocale);
            } else if (aLocale.getVariant() == "JP" &#x26;&#x26; aLocale.getLanguage() == "ja" &#x26;&#x26; aLocale.getCountry() == "JP") {
                cal = new JapaneseImperialCalendar(zone, aLocale);
            } else {
                cal = new GregorianCalendar(zone, aLocale);
            }
        }
    
        return (Calendar)cal;
    }
}
</code></pre>

### 스프링&#x20;

* spring bean factory&#x20;
  * object 타입의 product 를 만드는 BeanFactory라는 Creator

```java
public class SpringBeanFactoryExample {

    public static void main(String[] args) {
        BeanFactory xmlFactory = new ClassPathXmlApplicationContext("config.xml");
        String hello = xmlFactory.getBean("hello", String.class);
        System.out.println(hello);

        BeanFactory javaFactory = new AnnotationConfigApplicationContext(Config.class);
        String hi = javaFactory.getBean("hello", String.class);
        System.out.println(hi);
    }
}
```

