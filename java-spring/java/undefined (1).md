# 바이트 코드 조작하기

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

## 1. 왜 필요한가

* 자바 바이트 코드를 조작하는 기술은 생각보다 우리 주변에서 아주 널리 사용되고 있다. 이를테면 이런 곳에서.&#x20;
  * 프로그램 분석&#x20;
    * 코드에서 버그를 찾는 툴&#x20;
    * 코드 복잡도를 계산하는 툴 등&#x20;
  * 클래스 파일 생성&#x20;
    * 프록시&#x20;
    * 특정 API 의 호출을 제한
    * 스칼라와 같은 언어의 컴파일러&#x20;
  * 자바 소스코드를 건드리지 않고 할 수 있는 이것 저것들 (transparent 하게 무엇인가를 할 수 있는 곳)&#x20;
    * 프로파일러 (spring 에서 나온 newrelic)&#x20;
    * 최적화&#x20;
    * 로깅 등&#x20;
  * 스프링이 컴포넌트 스캔하는 방법 (6번 참고)
    * 내부적으로 asm(자바 바이트 코드 조작 툴)을 이용한다.&#x20;
    * 컴포넌트 스캔으로 빈 후보에 등록할 클래스를 찾는데 이용한다.&#x20;
* 이런 툴들이 어떤 원리로 동작하는지 알아보는 것은 우리가 사용하는 기술을 좀 더 깊게 이해할 수 있다는 점에서 분명 도움이 될 것이다.&#x20;



## 2. 바이트 코드 조작 라이브러리 비교&#x20;

* ASM: [https://asm.ow2.io/](https://asm.ow2.io/)
  * 매우 어렵다고 한다. 러닝 커브가 높고, 자바의 바이트코드 구조를 어느정도 알고 있고 디자인 패턴에 익숙해도 이해하고 사용하기거 어렵다고 한다.&#x20;
  * 스프링 프레임워크를 포함하여 많은 곳에서 쓰이고 있음&#x20;
* Javassist: [https://www.javassist.org/](https://www.javassist.org/)
  * ASM 보다는 아니지면 여전히 사용하기가 어렵다고 한다.&#x20;
* ByteBuddy: [https://bytebuddy.net/#/](https://bytebuddy.net/#/)
  * 비교적 가장 최근에 나온 라이브러리이다.&#x20;
  * 자바 바이트 코드에 대해서 깊게 몰라도 충분히 사용 가능하며, 러닝커브가 낮다.&#x20;
  * 배우기가 쉽다.&#x20;
  * 코드를 작성하기는 편하지만, 체이닝이 심해서 가독성은 좀 떨어지는 편&#x20;

> 따라서 나는 ByteBuddy 를 사용해볼 예정이다.&#x20;



## 3. ByteBuddy 사용하여 모자에서 없는 토끼 꺼내기&#x20;

### 3-1. ByteBuddy gradle 의존성 추가&#x20;

* [https://search.maven.org/artifact/net.bytebuddy/byte-buddy](https://search.maven.org/artifact/net.bytebuddy/byte-buddy)
* ```gradle
  dependencies {

      //ByteBuddy - for bytecode manipulation
      implementation 'net.bytebuddy:byte-buddy:1.14.0';
      
      ...
  }
  ```



### 3-2. 파일 내부에서 호출 전에 변경하기 &#x20;

* ByteBuddy 라이브러리를 통해 특정 클래스에 대해 새롭게 정의된 클래스 파일을 덮어씌우면, 이후 코드에서 객체를 생성하고 매소드를 호출할 때, 원래 코드와는 전혀 다른 값이 반환될 수 있다.&#x20;

```java
//Main
package _02_bytecode.masul;

import net.bytebuddy.ByteBuddy;
import net.bytebuddy.implementation.FixedValue;

import java.io.File;
import java.io.IOException;

import static net.bytebuddy.matcher.ElementMatchers.named;

public class Masulsa {

    public static void main(String[] args) {

        //1. Moja.class 내의 pullOut 매소드가 고정값인 Rabbit! 을 반환하도록 조작한 새로운 .class 파일로 덮어 씌우기
        try {
            new ByteBuddy()
                    .redefine(Moja.class)
                    .method(named("pullOut"))
                    .intercept(FixedValue.value("Rabbit!"))
                    .make()
                    .saveIn(new File("/Users/mhson/Documents/github-miniminis/deep-dive-in-java/deep-java/build/classes/java/main/"));
        } catch (IOException e) {
            e.printStackTrace();
        }

        //2. 위의 코드 실행 후 주석처리. 아래 코드 실행 -> Rabbit! 이 반환된다.
        System.out.println(new Moja().pullOut());       //return Rabbit!
    }
}


//Moja
package _02_bytecode.masul;

public class Moja {

    public String pullOut() {
        return "default";
    }
}
```

{% hint style="info" %}
두 코드를 동시에 실행할 수 없는 이유는?&#x20;

* 이미 1번 구간의 코드를 실행할 때, Moja.class 를 한번 읽어들인다. 읽는 시점에는 해당 클래스의 pullOut() 매소드는 바뀌기 전 값인 "default" 값을 가지고 있다.&#x20;
* 이미 클래스 로더에서 Moja 클래스를 읽어들였기 때문에, 1번 구간 수행 후, Moja 의 클래스 파일이 변경되었다고 하더라도 2번 구간을 수행할 때, 다시 새롭게 클래스 파일을 읽어들이지 않는다.&#x20;
* 결과적으로 1번 구간에서 최초 로드 후 클래스 파일은 변경되었지만 (Rabbit 반환코드) 2번 구간에서는 바뀐 클래스 파일을 새롭게 읽어들이지 않아서 이전에 읽어들인 Moja 클래스 파일 (default 반환코드) 을 실행하게 되는 것이다.  &#x20;
{% endhint %}

아래처럼 Moja.class 를 읽지 않는 경우에는 동시에 실행할 수 있다.&#x20;

```java
//Main
package _02_bytecode.masul2;

import net.bytebuddy.ByteBuddy;
import net.bytebuddy.dynamic.ClassFileLocator;
import net.bytebuddy.implementation.FixedValue;
import net.bytebuddy.pool.TypePool;

import java.io.File;
import java.io.IOException;

import static net.bytebuddy.matcher.ElementMatchers.named;

public class Masulsa {

    public static void main(String[] args) {

        //1. 클래스 파일을 로드하지 않고 클래스 파일을 변경
        ClassLoader classLoader = Masulsa.class.getClassLoader();
        TypePool typePool = TypePool.Default.of(classLoader);

        try {
            new ByteBuddy()
                    .redefine(typePool.describe("_02_bytecode.masul2.Moja").resolve(), ClassFileLocator.ForClassLoader.of(classLoader))
                    .method(named("pullOut"))
                    .intercept(FixedValue.value("Rabbit!"))
                    .make()
                    .saveIn(new File("/Users/mhson/Documents/github-miniminis/deep-dive-in-java/deep-java/build/classes/java/main/"));
        } catch (IOException e) {
            e.printStackTrace();
        }

        //2. 변경된 클래스 파일을 로드 후 실행 : Rabbit 반환
        System.out.println(new Moja().pullOut());       //Rabbit!
    }
}

//Moja
package _02_bytecode.masul2;

public class Moja {

    public String pullOut() {
        return "default";
    }
}

```

하지만, 위의 코드는 결국 실행 순서에 따라 결과가 달라지므로, 아직 한계점이 있다. 다른 곳에서 Moja 클래스를 먼저 로드해서 읽어버리는 경우에는 먹히지 않는 방법이다.&#x20;

완벽하게 바이트 코드를 조작하려면, 결국 다른 곳에서 먼저 위의 조치를 취해주어야 한다.&#x20;

javaagent 를 이용해서 완벽하게 바이트코드를 조작해보자.&#x20;



## 4. javaagent 사용하여 어플리케이션 실행시에 바이트 코드 조작하기

> [공식 문서](https://docs.oracle.com/javase/8/docs/api/java/lang/instrument/package-summary.html)를 참고하여 구현한다. &#x20;

### 4-1. 새로운 프로젝트 생성&#x20;

* masulsa-agent&#x20;

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### 4-2. premain() 함수 작성하기&#x20;

```java
package me.flash;

import net.bytebuddy.agent.builder.AgentBuilder;
import net.bytebuddy.implementation.FixedValue;
import net.bytebuddy.matcher.ElementMatchers;

import java.lang.instrument.Instrumentation;

import static net.bytebuddy.matcher.ElementMatchers.named;

public class MasulsaAgent {

    public static void premain(String arguments, Instrumentation instrumentation) {
        new AgentBuilder.Default()
                .type(ElementMatchers.any())
                .transform(
                        (builder, typeDescription, classLoader, module, protectionDomain)
                                -> builder.method(named("pullOut")).intercept(FixedValue.value("Rabbit!"))
                ).installOn(instrumentation);
    }
}
```

### 4-3. jar manifest 변경하기&#x20;

```gradle
plugins {
    id 'java'
}

//jar file manifest change
jar {
    manifest.attributes(
            'Premain-Class' : 'me.flash.MasulsaAgent',
            'Can-Redefine-Classes' : true,
            'Can-Retransform-Classes' : true
    )
}

group 'me.flash'
version '1.0-SNAPSHOT'

```



### 4-4. 터미널에서 gradle jar 실행하여 build 결과물 확인&#x20;

* `gradle jar`
* jar 파일은 확장자를 .zip 으로 수정하면 내부 내용을 살펴볼 수 있다.&#x20;

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### 4-5. 기존 application 에서 VM 옵션 추가

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>다시 jar 로 빌드 </p></figcaption></figure>

agent 프로젝트를 다시 jar 로 빌드한 뒤, 원래의 어플리케이션 코드로 돌아와 기존의 bytebuddy 코드를 모두 제거한다.&#x20;

```java
//Main
package _02_bytecode.masul3;

public class Masulsa {

    public static void main(String[] args) {
        //javaagent changes bytecode in premain()
        System.out.println(new Moja().pullOut());       //Rabbit!
    }
}


//Moja
package _02_bytecode.masul3;

public class Moja {

    public String pullOut() {
        return "default";
    }
}

```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption><p>오른쪽 상단 Modify Options 선택하여 VM options 추가 </p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

VM Options 칸에 아래의 명령어 추가

* `-javaagent:/Users/mhson/Documents/github-miniminis/deep-dive-in-java/masulsa-agent/build/libs/masulsa-agent-1.0-SNAPSHOT.jar`



그리고 실행하면, 아무 코드를 적지 않았음에도, javaagent 에 의해서 Rabbit 으로 바뀐 코드가 출력된다.&#x20;

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

물론, 원래 코드의 바이트 코드는 전혀 바뀌지 않았다.&#x20;

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

## 5. 정리&#x20;

* 총 3가지 방법을 통해서 java class 파일을 조작해보았다.&#x20;
  * 클래스 파일을 읽고 변경한 뒤, 재호출시에는 다시 클래스파일을 로드하지 않고, 변경된 클래스 파일을 사용하도록 하는 방식  -> 변경된 클래스 파일을 사용한다.&#x20;
  * 클래스 로드 전, 메모리에 있는 바이트 코드 파일을 직접 건드려 클래스 로드 시에는 처음부터 바뀐 파일이 읽히도록 하는 방식  -> 클래스 파일이 변경된다.&#x20;
  * 외부에서 javaagent jar 파일 내 premain() 매소드를 통해서 클래스 로드 시에 바이트 코드 파일 (클래스파일)을 변경하는 방법 -> 클래스 파일이 변경되지 않고 바꿔치기한 파일이 사용된다.&#x20;
    * 클래스 로더가 클래스를 읽어올 때, javaagent 를 거쳐서 변경된 바이트코드를 읽어들여 사용한다. 따라서 실제 바이트 코드는 변경이 없다.&#x20;
* 처음 두 방법은 코드의 실행 순서에 따라서 결과가 달라지므로, 3번 방법이 가장 정확하고 transparent 한 방법이라고 할 수 있겠다.&#x20;
* 시중에 바이트 코드를 조작하여 만든 툴들은 결국 이처럼 기존 소스코드를 건드리지 않는 방법을 사용하여 구현된 형태일 것이다.&#x20;



## 6. Spring 에서 asm 을 이용하여 컴포넌트 스캔&#x20;

SpringApplication 의 시작점에 붙은 @SpringBootApplication 내부를 살펴보면, @ComponantScan 이라고 하는 어노테이션이 있다.&#x20;

해당 어노테이션은 해당 위치부터 모든 하위 디렉토리를 돌면서 @Component 어노테이션이 붙은 모든 클래스들을 스캔하여 빈으로 등록해준다. &#x20;

```java
@SpringBootApplication
public class ExampleApplication {
    public static void main(String[] args) {
        SpringApplication.run(ExampleApplication.class, args);
    }
}

@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {...}

```

이처럼 컴포넌트를 스캔하는 과정에서 사용되는 것이 바로 ClassPathScanningCandidateComponentProvider, SimpleMetadataReader 이다.&#x20;



### 6-1. ClassPathScanningCandidateComponentProvider

실제로 문서를 보면 ASM ClassReader 를 이용하고 있다는 것을 알 수 있다.&#x20;

<figure><img src="../../.gitbook/assets/image (2) (9).png" alt=""><figcaption></figcaption></figure>

type 으로부터 메타데이터를 읽어온다. 메타데이터를 읽는 reader가 바로 SimpleMetadataReader

<figure><img src="../../.gitbook/assets/image (1) (6).png" alt=""><figcaption></figcaption></figure>

### 6-2. SimpleMetadataReader

* annotationMetadata 를 읽어온다.&#x20;

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### 6-3. ClassReader&#x20;

* asm 라이브러리를 스프링에서 아예 패키지 내부로 포함시킴&#x20;

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

### 6-4. 참고&#x20;

* [https://www.youtube.com/watch?v=39kdr1mNZ\_s](https://www.youtube.com/watch?v=39kdr1mNZ\_s)
* 더 알아보기&#x20;
  * ASM, Javassist, ByteBuddy, CGlib
