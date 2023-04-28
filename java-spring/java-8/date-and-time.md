# Date and Time

> 백기선 님의 <[더 자바, Java 8](https://www.inflearn.com/course/the-java-java8)>를 보고 공부한 내용을 기록합니다.&#x20;

## 1. 배경&#x20;

* 그동안 우리는 다양한 방법으로 시간과 날짜를 사용해왔다. 하지만 각각의 방법들은 나름대로 문제점들이 있었다.&#x20;
* java.util.Date&#x20;
  * 클래스 이름이 명확하지 않았다. Date인데 시간까지 다룬다.&#x20;
  * thread safe 하지 않다.
    * mutable 하기 때문에 setDate() 등으로 날짜를 마구 변경 가능 &#x20;
* java.util.Calendar
  * 타입 안정성이 없어서 버그가 발생할 여지가 높다.
    * &#x20;월은 0부터 시작하고 day 는 1부터 시작한다.&#x20;
* [joda-time API](https://www.joda.org/joda-time/)&#x20;
  * 그래서 자바8 이전까지는 대부분의 날짜 시간 처리가 복잡한 어플리케이션에서 보통 joda-time API 를 사용해왔다.&#x20;

<figure><img src="../../.gitbook/assets/image (3) (7).png" alt=""><figcaption><p>멀티 쓰레드 환경에서 Date 객체에 동시에 접근하여 변경하는 경우를 보여준다. </p></figcaption></figure>

```xml
<dependency>
    <groupId>joda-time</groupId>
    <artifactId>joda-time</artifactId>
    <version>2.12.2</version>
</dependency>
```

```java
public class BeforeJava8Test {

    @Test
    void DateApiTest() {
        Date date = new Date();
        System.out.println("date = " + date);   //Thu Mar 16 14:16:15 KST 2023

        long time = date.getTime();
        System.out.println("time = " + time);   //1678943775380

        date.setTime(time - 1);                                 //1678944050334
        System.out.println("changed time = " + date.getTime()); //1678944050333
    }

    @Test
    void CalendarApiTest() {
        Calendar calendar = new GregorianCalendar(-1000, 2, 15); //month는 0부터 시작하므로 숫자를 바로 쓸 수 없음
        assertThat(calendar.getWeekYear()).isEqualTo(-1000);

        Calendar calendar2 = new GregorianCalendar(2002, Calendar.FEBRUARY, 15);
        System.out.println(calendar2);
    }

    @Test
    void jodaTimeApiTest() {
        DateTime dateTime = new DateTime();
        assertThat(dateTime.getYear()).isEqualTo(2023);
        assertThat(dateTime.getMonthOfYear()).isEqualTo(3);
        assertThat(dateTime.getDayOfMonth()).isEqualTo(16);
    }
}
```

## 2. Java8 부터 Date-Time API 의 등장&#x20;

* [JSR-310 스펙](https://jcp.org/en/jsr/detail?id=310)의 구현체를 제공한다.&#x20;
* 디자인 철학&#x20;
  * Clear : 명확한 이름&#x20;
  * Immutable : 불변
  * Fluent : Null type 을 반환하지 않아서 chaining 형태로 계속 확장하여 사용 가능&#x20;
  * Extensible

### 2-1. 주요 API 특징&#x20;

* 기계용 시간과 인류용 시간, 두 분류로 나눈다.&#x20;
* 기계용 시간은 EPOCK 부터 현재까지의 타임스탬프를 표현한다.&#x20;
  * EPOCK : 리눅스가 시간을 처음 세기 시작했던 1970년 1월 1일 0시 0분 0초&#x20;
* 인류용 시간은 우리가 흔히 사용하는 년, 월, 일, 시, 분, 초 등을 표현한다.&#x20;
* 타임스탬프는 Instant 를 사용한다.&#x20;
* 특정날짜(LocalDate), 시간(LocalTime), 일시(LocalDateTime)을 사용할 수 있다.&#x20;
* 기간을 표현할 때는 Duration(시간기반), Period(날짜기반)을 사용할 수 있다.&#x20;
* DateTimeFormatter 를 사용해서 일시를 특정한 문자열로 포매팅할 수 있다.&#x20;

### 2-2. 코드로 살펴보기&#x20;

* 파싱 혹은 포메팅 : [미리 정의해둔 포맷 참고](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html#predefined)&#x20;
* LocalDateTime.parse(String, DateTimeFormatter);

```java

public class AfterJava8Test {

    @Test
    void instant로_기계용_시간_표현하기() {
        System.out.println("Instant.now() = " + Instant.now());
        System.out.println("Instant.now().atZone(ZoneId.of(\"UTC\")) = " + Instant.now().atZone(ZoneId.of("UTC")));

        //Instant.now() = 2023-03-16T05:52:42.740808Z
        //Instant.now().atZone(ZoneId.of("UTC")) = 2023-03-16T05:52:42.753796Z[UTC]
    }

    @Test
    void zonedDateTIme으로_타임존_표현하기() {
        ZonedDateTime zonedDateTime = ZonedDateTime.now();
        System.out.println("zonedDateTime = " + zonedDateTime); //2023-03-16T14:52:15.378961+09:00[Asia/Seoul]

        ZonedDateTime zonedDateTime2 = ZonedDateTime.now(ZoneId.systemDefault());
        System.out.println("zonedDateTime2 = " + zonedDateTime2);   //2023-03-16T14:52:15.383428+09:00[Asia/Seoul]
    }

    @Test
    void localDateTime으로_인류용_시간_표현하기() {
        LocalDateTime localDateTime = LocalDateTime.now();
        System.out.println("localDateTime = " + localDateTime); //localDateTime = 2023-03-16T14:53:32.503884

        LocalDateTime localDateTime1 = LocalDateTime.of(2002, 2, 15, 11, 23, 30);
        System.out.println("localDateTime1 = " + localDateTime1);   //localDateTime1 = 2002-02-15T11:23:30

        ZonedDateTime zonedDateTime = ZonedDateTime.of(localDateTime1, ZoneId.of("Asia/Seoul"));
        System.out.println("zonedDateTime = " + zonedDateTime); //zonedDateTime = 2002-02-15T11:23:30+09:00[Asia/Seoul]
    }

    @Test
    void period로_기간표현하기() {
        System.out.println("LocalDate.now() = " + LocalDate.now());

        Period period = Period.between(LocalDate.of(1994, 8, 11), LocalDate.now());
        System.out.println("period = " + period);   //P-28Y-7M-5D
        System.out.println("period.getYears() = " + period.getYears()); //28
        System.out.println("period.getMonths() = " + period.getMonths());   //7

        Period period2 = Period.between(LocalDate.now(), LocalDate.of(2023, 8, 11));
        System.out.println("period2.getYears() = " + period2.getYears());   //0
        System.out.println("period2.getMonths() = " + period2.getMonths()); //4
        System.out.println("period2.getDays() = " + period2.getDays());     //26
        System.out.println("period2.get(ChronoUnit.DAYS) = " + period2.get(ChronoUnit.DAYS));   //26
    }

    @Test
    void duration으로_기간표현하기() {
        Instant now = Instant.now();
        Instant plus = now.plus(10, ChronoUnit.SECONDS);
        Duration duration = Duration.between(now, plus);

        assertThat(duration.getSeconds()).isEqualTo(10);
    }

    @Test
    void parsing하는법() {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        System.out.println("LocalDate.now().format(dateTimeFormatter) = " + LocalDate.now().format(dateTimeFormatter));
        //LocalDate.now().format(dateTimeFormatter) = 2023-03-16

        LocalDate parsed = LocalDate.parse("1998-08-30", dateTimeFormatter);
        System.out.println("parsed = " + parsed);   //parsed = 1998-08-30 (localDate)
    }

    @Test
    void legacyAPI지원() {
        ZoneId pst = TimeZone.getTimeZone("PST").toZoneId();
        System.out.println("pst = " + pst);     //America/Los_Angeles

        TimeZone legacyZoneAPI = TimeZone.getTimeZone(ZoneId.of("Asia/Seoul"));
        System.out.println("legacyZoneAPI = " + legacyZoneAPI);
        //legacyZoneAPI = sun.util.calendar.ZoneInfo[id="Asia/Seoul",offset=32400000,dstSavings=0,useDaylight=false,transitions=30,lastRule=null]

        Instant newInstant = new Date().toInstant();
        System.out.println("newInstant = " + newInstant);   //newInstant = 2023-03-16T07:09:46.767Z

        Date legacyInstant = Date.from(newInstant);
        System.out.println("legacyInstant = " + legacyInstant); //legacyInstant = Thu Mar 16 16:09:46 KST 2023
    }
}
```



## 3. 참고&#x20;

* [https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/](https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/)
  * java8 이전까지 날짜, 시간 관련 API 가 어떤 점이 문제인지 상세하게 설명&#x20;
* [https://docs.oracle.com/javase/tutorial/datetime/overview/index.html](https://docs.oracle.com/javase/tutorial/datetime/overview/index.html)
* [https://docs.oracle.com/javase/tutorial/datetime/iso/overview.html](https://docs.oracle.com/javase/tutorial/datetime/iso/overview.html)

