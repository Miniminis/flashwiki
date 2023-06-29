# logback + webfilter 로 로그설정

## 1. logback-spring.xml (/resources 내부 포함)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    <property name="logPath"		value="/root/logs" />
    <property name="fileName"		value="application" />
    <property name="maxHistory"		value="30" />
    <property name="maxFileSize"	value="10MB" />
    <property name="totalSizeCap"	value="1GB" />
    <property name="consolePattern"	value="[%level] %d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %c{1} - %msg%n"/>
    <property name="filePattern"    value="[%level] %d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %c{1} - %msg%n"/>
    <property name="slackPattern"   value="%d{yyyy-MM-dd HH:mm:ss}:%-3relative][%thread] %-5level %logger{35} - %msg%n"/>
    <property name="slackHookUri"	value="%%%%%%%%%YOUR SLACK WEBHOOK URI%%%%%%%%%"/>

    <!-- Log Appender Module -->
    <springProfile name="console-logging">
        <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
        <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>${consolePattern}</pattern>
            </encoder>
        </appender>
        <root level="INFO">
            <appender-ref ref="console"/>
        </root>
    </springProfile>

    <!-- file Appender Module -->
    <springProfile name="file-logging">
        <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <file>${logPath}//${fileName}.log</file>
            <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
                <fileNamePattern>${logPath}//archive/%d{yyyy-MM-dd}_${fileName}_%i.log</fileNamePattern>
                <maxHistory>${maxHistory}</maxHistory>
                <maxFileSize>${maxFileSize}</maxFileSize>
                <totalSizeCap>${totalSizeCap}</totalSizeCap>
            </rollingPolicy>
            <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
                <pattern>${filePattern}</pattern>
            </encoder>
        </appender>
        <root level="INFO">
            <appender-ref ref="file"/>
        </root>
    </springProfile>

    <!-- Message Appender Module -->
    <springProfile name="message-logging">
        <appender name="slack" class="com.github.maricn.logback.SlackAppender">
            <layout class="ch.qos.logback.classic.PatternLayout">
                <pattern>${slackPattern}</pattern>
            </layout>
            <webhookUri>${slackHookUri}</webhookUri>
            <username>username</username>
            <iconEmoji>:stuck_out_tongue_winking_eye:</iconEmoji>
            <colorCoding>true</colorCoding>
        </appender>
    </springProfile>

    <!-- Log Level Module -->
<!--    <springProfile name="high-level-logging">-->
<!--        <logger name="root" level="error" />-->
<!--        <logger name="org.springframework" level="error" />-->
<!--        <logger name="com.blog.tistory" level="warn" />-->
<!--    </springProfile>-->

<!--    <springProfile name="low-level-logging">-->
<!--        <logger name="root" level="info" />-->
<!--        <logger name="org.springframework" level="info" />-->
<!--        <logger name="com.blog.tistory" level="debug" />-->
<!--    </springProfile>-->

</configuration>
```



## 2. application.yml

```yaml
# common setting
spring:
  profiles:
    group:
      local:
        - console-logging
      dev:
        - file-logging
      prod:
        - file-logging

---
# local

spring:
  config:
    activate:
      on-profile: local
  datasource:
    ...
---
# dev
spring:
  config:
    activate:
      on-profile: dev
  datasource:
    ...

---
# prod
spring:
  config:
    activate:
      on-profile: prod
  datasource:
    ...
```

## 3. GlobalLoggingFilter.java&#x20;

```java
package com.testapp.filter;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.util.ContentCachingRequestWrapper;
import org.springframework.web.util.ContentCachingResponseWrapper;

import java.io.IOException;
import java.util.Collections;

@Slf4j
@WebFilter(filterName = "loggingFilter", urlPatterns = { "/*" })
public class GlobalLoggingFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //request&response caching
        ContentCachingRequestWrapper httpServletRequest = new ContentCachingRequestWrapper((HttpServletRequest) request);
        ContentCachingResponseWrapper httpServletResponse = new ContentCachingResponseWrapper((HttpServletResponse) response);

        //main logic
        chain.doFilter(httpServletRequest, httpServletResponse);

        String url = httpServletRequest.getRequestURI();
        String reqContent = new String(httpServletRequest.getContentAsByteArray());

        log.info("\n\n");
        log.info(" ========== TEST-APP REQUEST ========== ");
        log.info("url : {}", url);
        Collections.list(httpServletRequest.getHeaderNames()).forEach(headerName -> {
            log.info("headerName {} : {}", headerName, httpServletRequest.getHeader(headerName));
        });
        log.info("requestBody : {}", reqContent);

        int httpStatusCode = httpServletResponse.getStatus();
        String resContent = new String(httpServletResponse.getContentAsByteArray());
        httpServletResponse.copyBodyToResponse();       //이 매서드를 호출해줘야 한번 읽은 response 가 클라이언트에게도 복사되어 응답될 수 있다.

        log.info("\n\n");
        log.info(" ========== TEST-APP RESPONSE ========== ");
        log.info("httpStatusCode : {}", httpStatusCode);
        httpServletResponse.getHeaderNames().forEach(headerName -> {
            log.info("headerName {} : {}", headerName, httpServletResponse.getHeader(headerName));
        });
        log.info("responseBody : {}", resContent);
        log.info("\n\n");
    }
}

```



## 4. MainApplication

```java
@SpringBootApplication
@ServletComponentScan
public class TestAppApplication {

    public static void main(String[] args) {
        SpringApplication.run(TestAppApplication.class, args);
    }

}

```

