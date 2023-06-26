# \[Gradle]UncheckedIOException

## 문제 상황&#x20;

> org.gradle.api.UncheckedIOException: java.io.StreamCorruptedException: invalid type code: 00

* Git clone 하고 처음 열어본 Spring Boot 프로젝트에서 위와 같은 오류 메시지가 나오면서 프로젝트 실행 자체가 안되는 현상이 발생&#x20;



## 해결&#x20;

* 이런 저런 블로그글 찾아보는데 딱히 도움되는 글을 못찾다가 clean 했다는 사람의 말에서 힌트를 얻음&#x20;
* Gradle Setting 열어보니까 Gradle 기본 JVM 버전이 프로젝트 세팅의 SDK 버전하고 안맞았었다. 프로젝트 SDK 버전하고 똑같이 맞춰주니까 해결! &#x20;

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

