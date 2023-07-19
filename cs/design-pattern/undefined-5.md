# 의존성 주입과 의존 관계 역전 원칙

* Dependency Injection 이란 메인 모듈이 직접 다른 하위 모듈에 대한 의존성을 가지고 있기 보다는, 중간에 의존성 주입자 (dependency injector) 가 이 부분을 가로채 메인 모듈이 간접적으로 의존성을 주입하는 방식을 말한다.&#x20;
* 이를 통해서 메인 모듈과 하위 모듈간의 의존성을 조금 더 느슨하게 만들 수 있으며 모듈을 쉽게 교체 가능한 구조로 만든다.&#x20;

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

## 의존한다는 의미&#x20;

* A가 B에 의존한다 = B가 변하면, A도 변한다.&#x20;
* 아래 코드의 경우, Hi는 Hello 클래스에 의존하고 있는데, 만약 Hello 클래스의 greet 에 매개변수나 다른 요소들이 추가되면, Hi 클래스도 그에 맞춰 같이 바뀌어져야한다.&#x20;

```java
package me.whiteship.designpatterns;

public class DependencyInjectionApp {
    public static void main(String[] args) {
        new Hi().greet();
    }
}

class Hello {
    public void greet() {
        System.out.println("greetings from b");
    }
}

class Hi {
    public void greet() {
        new Hello().greet();
    }
}
```



## Dependency Injection 예시

![](<../../.gitbook/assets/image (2).png>) --->  ![](<../../.gitbook/assets/image (5).png>)

* 아래는 큰돌님의 <[CS 지식의 정석](https://www.inflearn.com/course/%EA%B0%9C%EB%B0%9C%EC%9E%90-%EB%A9%B4%EC%A0%91-cs-%ED%8A%B9%EA%B0%95)> 강의를 보면서 참고한 코드입니다. &#x20;

### DI 적용하지 않고 바로 구현했을 때&#x20;

```java

public class DINotApplied {
    public static void main(String[] args) {
        Project project = new Project(new BackendDeveloper(), new FrontendDeveloper());
        project.implement();
    }

    private static class BackendDeveloper {
        public void writeJava() {
            System.out.println("coding with java");
        }
    }

    private static class FrontendDeveloper {
        public void writeJs() {
            System.out.println("coding with javascript");
        }
    }

    private static class Project {
        private final BackendDeveloper backendDeveloper;
        private final FrontendDeveloper frontendDeveloper;

        public Project(BackendDeveloper backendDeveloper,
                       FrontendDeveloper frontendDeveloper) {
            this.backendDeveloper = backendDeveloper;
            this.frontendDeveloper = frontendDeveloper;
        }

        public void implement() {
            backendDeveloper.writeJava();
            frontendDeveloper.writeJs();
        }
    }
}

```

### DI 적용하여 구현했을 때

```java
package me.whiteship.designpatterns._04_di;

import java.util.ArrayList;
import java.util.List;

public class DIApplied {
    public static void main(String[] args) {
        List<Developer> developers = new ArrayList<>();
        developers.add(new BackendDeveloper());
        developers.add(new FrontendDeveloper());

        Project project = new Project(developers);
        project.implement();
    }

    interface Developer {
        void develop();
    }

    static class BackendDeveloper implements Developer {

        @Override
        public void develop() {
            writeJava();
        }

        void writeJava() {
            System.out.println("coding with java");
        }
    }

    static class FrontendDeveloper implements Developer {

        @Override
        public void develop() {
            writeJs();
        }

        private void writeJs() {
            System.out.println("coding with javascript");
        }
    }

    static class Project {
        private final List<Developer> developers;

        public Project(List<Developer> developers) {
            this.developers = developers;
        }

        public void implement() {
            developers.forEach(Developer::develop);
        }
    }

}

```

### 자세히보기&#x20;

* 위와 같이 구현하면 새로운 개발자 유형 (안드로이드, IOS 등)이 추가되어도 크게 수정하지 않고 바로 적용이 가능하다.&#x20;

## 의존 관계 역전 원칙(Inversion of Dependency Injection)

* 의존성 주입시에는 의존관계역전원칙이 적용된다. 이는 아래의 2가지가 충족되는 상황을 말한다.&#x20;
  * 상위 모듈은 하위 모듈에 의존해서는 안된다. 둘다 추상화에 의존해야한다.&#x20;
  * 추상화는 세부사항에 의존해서는 안된다. 세부사항은 추상화에 따라 달라져야한다.&#x20;

## 의존성 주입의 장점

* 외부에서 모듈을 생성해서 집어넣는 구조가 되기 때문에 모듈을 쉽게 교체할 수 있게 된다.&#x20;
  * 위의 예에서 BackendDeveloper -> FrontDeveloper -> AppDeveloper 등 손쉽게 교체가 가능하다.&#x20;
* 단위 테스트와 마이그레이션이 쉬워진다.&#x20;
* 어플리케이션 의존성 방향이 좀 더 일관되어 코드를 추론하기가 쉬워진다.&#x20;



## 의존성 주입의 단점&#x20;

* 모듈이 더 생기는 것이므로 코드가 복잡해진다.&#x20;
* 종속성 주입이 런타임에 일어나기 때문에 컴파일 할 때에는 종속성 주입에 관한 에러를 사전에 잡기가 어려워진다.&#x20;

