# reflection api

{% hint style="info" %}
인프런 백기선님의 강의 <[더 자바, 코드를 조작하는 다양한 방법](https://www.inflearn.com/course/the-java-code-manipulation/dashboard)> 을 수강하면서 공부한 내용을 정리합니다.&#x20;
{% endhint %}

{% embed url="https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html" %}

## 1. Class API 로 클래스 정보 조회하는 법 익히기&#x20;

```java
package me.flash;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import java.lang.reflect.Modifier;
import java.util.Arrays;

class MainTest {

    @Test
    @DisplayName("1. Class<T>에_접근하는 3가지 방법")
    void approachClass() throws ClassNotFoundException {
        //1. 모든 클래스를 로딩하고 나면, Class<T> 의 인스턴스가 생긴다. 타입.class 형태로 접근 가능하다.
        Class<Number> numberClass1 = Number.class;

        //2. 모든 인스턴스는 getClass() 메소드를 가지고 있다. 인스턴스.getClass() 로 접근할 수 있다.
        Number number = new Number();
        Class<? extends Number> numberClass2 = number.getClass();

        //3. 클래스 full qualified class name 인 문자열로 읽어오는 방법 : Class.forName("FQCN");
        //- 클래스 패스에 해당 클래스가 없다면 ClassNotFoundException 발생한다.
        Class<?> numberClass3 = Class.forName("me.flash.Number");

        System.out.println(numberClass1);
        System.out.println(numberClass2);
        System.out.println(numberClass3);
        System.out.println();

        //class me.flash.Number
        //class me.flash.Number
        //class me.flash.Number
    }

    @DisplayName("2. 필드 목록 가져오기")
    @Test
    void getFields() {
        Class<Number> numberClass = Number.class;

        Arrays.stream(numberClass.getFields()).forEach(System.out::println);   //public 필드만
        System.out.println();
        Arrays.stream(numberClass.getDeclaredFields()).forEach(System.out::println);   //모든 필드
        System.out.println();
    }

    @DisplayName("2-1. 필드 값 가져오기")
    @Test
    void getFieldsValue() {
        Class<Number> numberClass = Number.class;
        Number number = new Number();

        Arrays.stream(numberClass.getDeclaredFields()).forEach(f -> {
            f.setAccessible(true);
            System.out.println(f);
            try {
                System.out.println(f.get(number));  //instance 를 통해서 가져올 수 있다.
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        });
    }

    @DisplayName("2-2. 필드 접근제어자 확인하기")
    @Test
    void getFieldsModifier() {
        Class<Number> numberClass = Number.class;
        Number number = new Number();

        Arrays.stream(numberClass.getDeclaredFields()).forEach(f -> {
            int modifiers = f.getModifiers();
            System.out.println(f);
            System.out.println(Modifier.isPrivate(modifiers));
            System.out.println(Modifier.isPublic(modifiers));
        });
    }

    @DisplayName("3. 메소드 목록 가져오기")
    @Test
    void getMethods() {
        Class<Number> numberClass = Number.class;

        Arrays.stream(numberClass.getMethods()).forEach(System.out::println);
        System.out.println();
        Arrays.stream(numberClass.getDeclaredMethods()).forEach(System.out::println);
        System.out.println();
    }

    @DisplayName("3-1. 메소드 관련 정보 가져오기")
    @Test
    void getMethodInfos() {
        Class<Number> numberClass = Number.class;

        Arrays.stream(numberClass.getDeclaredMethods()).forEach(f -> {
            System.out.println(f);
            Arrays.stream(f.getParameterTypes()).forEach(System.out::println);
            System.out.println(f.getReturnType());
        });
    }

    @DisplayName("4. 상위 클래스 가져오기")
    @Test
    void getSuperClass() {
        Class<Number> numberClass = Number.class;
        System.out.println(numberClass.getSuperclass());
    }

    @DisplayName("5. 인터페이스 목록 가져오기")
    @Test
    void getInterface() {
        Arrays.stream(MyNumber.class.getInterfaces()).forEach(System.out::println);
        //interface me.flash.NumberInterface
    }

    @DisplayName("6. 어노테이션 가져오기")
    @Test
    void getAnnotation() {
        Arrays.stream(MyNumber.class.getAnnotations()).forEach(System.out::println);
    }

    @DisplayName("7. 생성자 가져오기")
    @Test
    void getConstructor() {
        Arrays.stream(Number.class.getConstructors()).forEach(System.out::println);
        //public me.flash.Number()
        //public me.flash.Number(java.lang.String,java.lang.String,java.lang.String)
    }
}
```



## 2. 리플렉션 API 와 어노테이션&#x20;

```java
@Retention(RetentionPolicy.RUNTIME)   //default : RetentionPolicy.CLASS
@Target({ElementType.TYPE, ElementType.METHOD})  //생성자, 메소드에만 적용 가능
@Inherited
public @interface FlashAnnotation {
}

@Retention(RetentionPolicy.RUNTIME)
public @interface SecondAnnotation {
}

@FlashAnnotation
public class Number { ... }
```

```java
@DisplayName("6. 어노테이션 가져오기")
@Test
void getAnnotation() {
    Arrays.stream(MyNumber.class.getAnnotations()).forEach(System.out::println);
}

@DisplayName("6-1. 어노테이션 목록 조회하기")
@Test
void getAnnotationList() {
    /**
     * 커스텀 어노테이션은 java code, java byte code (class file)까지도 남아있지만, runtime 까지 남아있지는 않는다.
     * 그래서 getAnnotations() 에서는 사용자 정의 어노테이션은 조회되지 않는다.
     * 조회 방법 : @Retention 설정하기
     * */
    Arrays.stream(Number.class.getAnnotations()).forEach(System.out::println);
    //@me.flash.FlashAnnotation()
}

@DisplayName("6-2. @Inherited 상속 클래스에서 상위 클래스 어노테이션 조회")
@Test
void getSuperClassAnnotation() {
    Arrays.stream(MyNumber.class.getAnnotations()).forEach(System.out::println);
    //@me.flash.FlashAnnotation()
}

@DisplayName("6-3. 상속받은 어노테이션까지 조회하기/하지않기")
@Test
void getAnnotations() {
    Arrays.stream(MyNumber.class.getAnnotations()).forEach(System.out::println);
    System.out.println();
    //@me.flash.FlashAnnotation()
    //@me.flash.SecondAnnotation()

    Arrays.stream(MyNumber.class.getDeclaredAnnotations()).forEach(System.out::println);
    //@me.flash.SecondAnnotation()
}
```

&#x20;

## 3. Reflection API 로 클래스 정보 수정하거나 실행하기&#x20;

```java
package me.flash;

public class ClassModification {

    public static String A = "A";

    private String b = "B";

    private void c() {
        System.out.println("C");
    }

    public int d (int num1, Integer num2) {
        return num1 + num2;
    }

    public ClassModification() {
    }
    public ClassModification(String b) {
        this.b = b;
    }
}
```

```java
package me.flash;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

class ClassModificationTest {

    @DisplayName("class instance 만들기")
    @Test
    void createInstance() throws InstantiationException, IllegalAccessException {
        /**
         * 주의 : ClassModification.class.newInstance(); 는 Deprecated 되었으므로 사용하지 않도록 한다.
         * */
        ClassModification classModification = ClassModification.class.newInstance();
        System.out.println(classModification);      //me.flash.ClassModification@2f4948e4
    }

    @DisplayName("생성자로 인스턴스 만들기")
    @Test
    void constructorInstance() throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class<?> clazz = Class.forName("me.flash.ClassModification");
        Constructor<?> constructor = clazz.getConstructor(null);
        ClassModification classModification = (ClassModification) constructor.newInstance();

        System.out.println(classModification);      //me.flash.ClassModification@2f4948e4
    }

    @DisplayName("static 필드 접근하기, 수정하기")
    @Test
    void editStaticFields() throws NoSuchFieldException, IllegalAccessException {
        Field a = ClassModification.class.getDeclaredField("A");
        System.out.println(a.get(null));

        a.set("A", "hey");
        System.out.println(a.get(null));
    }

    @DisplayName("instance 필드 접근하기, 수정하기")
    @Test
    void editInstanceFields() throws ClassNotFoundException, InvocationTargetException, InstantiationException, IllegalAccessException, NoSuchMethodException, NoSuchFieldException {
        Class<?> aClass = Class.forName("me.flash.ClassModification");
        Constructor<?> constructor = aClass.getConstructor(String.class);

        ClassModification instance = (ClassModification) constructor.newInstance("hello");

        Field b = ClassModification.class.getDeclaredField("b");
        b.setAccessible(true);
        System.out.println(b.get(instance));

        b.set(instance, "bye");
        System.out.println(b.get(instance));
    }

    @DisplayName("메소드 실행하기")
    @Test
    void methodExecution() throws NoSuchMethodException, ClassNotFoundException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class<?> aClass = Class.forName("me.flash.ClassModification");
        Constructor<?> constructor = aClass.getConstructor(String.class);

        ClassModification instance = (ClassModification) constructor.newInstance("hello");

        Method c = ClassModification.class.getDeclaredMethod("c");
        c.setAccessible(true);
        c.invoke(instance);

        Method d = ClassModification.class.getDeclaredMethod("d", int.class, Integer.class);
        System.out.println(d.invoke(instance, 1, 2));
    }

}
```

