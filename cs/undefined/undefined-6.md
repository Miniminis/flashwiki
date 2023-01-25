# 팩토리 패턴

## 개념&#x20;

* 객체를 사용하는 코드에서 객체 생성 부분을 떼어내어 추상화한 패턴
* 상속 관계에 있는 두 클래스에서 상위 클래스가 중요한 뼈대를 결정하고, 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정한다.&#x20;

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





