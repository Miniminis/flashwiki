# java19 + spring 3.0.5 + gradle 7.4.1 에서 프로젝트 gradle 설정하기

## Querydsl 설정 &#x20;

* [https://velog.io/@juhyeon1114/Spring-QueryDsl-gradle-설정-Spring-boot-3.0-이상](https://velog.io/@juhyeon1114/Spring-QueryDsl-gradle-%EC%84%A4%EC%A0%95-Spring-boot-3.0-%EC%9D%B4%EC%83%81)
* [http://honeymon.io/tech/2020/07/09/gradle-annotation-processor-with-querydsl.html](http://honeymon.io/tech/2020/07/09/gradle-annotation-processor-with-querydsl.html)



## mysql:mysql-connector-java

* 기존에는 버전이 없었다가 버전을 지정해주니까 정상적으로 빌드 됨&#x20;

```groovy
dependencies {
    //database
    implementation 'mysql:mysql-connector-java:8.0.32'     //mysql
}
```

## 전체 설정&#x20;

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.0.5'
    id 'io.spring.dependency-management' version '1.1.0'
}

group = 'org.project'
version = '0.0.1'
sourceCompatibility = '19'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    //database
    implementation 'mysql:mysql-connector-java:8.0.32'     //mysql
    runtimeOnly 'com.h2database:h2'         //h2 database for local

    //jpa
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'

    //querydsl
    implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
    annotationProcessor("com.querydsl:querydsl-apt:5.0.0:jakarta") // querydsl JPAAnnotationProcessor 사용 지정
    annotationProcessor("jakarta.annotation:jakarta.annotation-api") // java.lang.NoClassDefFoundError (javax.annotation.Generated) 발생 대응
    annotationProcessor("jakarta.persistence:jakarta.persistence-api") // java.lang.NoClassDefFoundError(javax.annotation.Entity) 발생 대응

    //spring-web
    implementation 'org.springframework.boot:spring-boot-starter-web'
    annotationProcessor 'org.springframework.boot:spring-boot-configuration-processor'

    //lombok
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testCompileOnly 'org.projectlombok:lombok'      //테스트에서 lombok 사용
    testAnnotationProcessor 'org.projectlombok:lombok'      //테스트에서 lombok 사용

    //dev-tools
    developmentOnly 'org.springframework.boot:spring-boot-devtools'

    //test
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
    useJUnitPlatform()
}


//querydsl
def querydslDir = "$buildDir/generated/querydsl"

sourceSets {
    main.java.srcDirs += [ querydslDir ]
}

tasks.withType(JavaCompile) {
    options.annotationProcessorGeneratedSourcesDirectory = file(querydslDir)
}

clean.doLast {
    file(querydslDir).deleteDir()

```

