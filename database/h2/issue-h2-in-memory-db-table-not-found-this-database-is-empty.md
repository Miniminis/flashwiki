# \[issue] h2 in-memory-db Table not found (this database is empty) 해결방법

h2 데이터베이스를 정말 오랜만에 써본다. 한창 JPA 배울 때는 많이 썼었는데, 한동안 안쓰다보니, 업데이트 된 버전을 오랜만에 쓰는데 바뀐 부분이 좀 많았다. 물론 문서를 통해서 더 깊게 알아볼 예정이지만, 일단은 문제 해결한 부분까지만 기억하고자 글을 남긴다.&#x20;

## 1. spec&#x20;

현재 나의 프로젝트 및 h2 데이터베이스 스펙이다.&#x20;

* SpringBoot 3.0.2&#x20;
* h2 database : 2.1.214&#x20;



## 2. 문제상황&#x20;

* 기억나는대로 application.yml 을 설정한 뒤, 아주 기본적으로 구현한 UserRepository 를 테스트하려고 하는데, 자꾸 에러가 났다. 여러가지 문제들을 만났는데 그 중 한가지는 아래와 같다.&#x20;
  * user 키워드는 SQL 표준에서 예약어이기 때문에 user 라는 이름의 테이블을 생성할 수 없다.&#x20;
* (분명 예전에는 언젠가 user 라는 테이블을 생성했던 것 같은데... 흠)

## 3. 해결방법&#x20;

* 아래와 같이 두 가지 방법으로 해결할 수 있다.&#x20;
  * user 테이블 이름을 변경하거나&#x20;
  * application.yml 의 datasource url 에서 user 키워드 해제 설정을 추가하거나.&#x20;
    * ```yaml
      spring:
        datasource:
            url: jdbc:h2:mem:testdb;NON_KEYWORDS=USER
      ```
  * 나는 테스트용으로 간단히 만든 것이어서 user 테이블의 이름을 users 로 지정해주는 방식으로 풀었다.&#x20;

## 4. 결과&#x20;

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



## 5. 주의!&#x20;

* [이 분 블로그](https://jeongkyun-it.tistory.com/184)를 참고하여 문제를 해결하였는데, h2 database 2.1.212 버전부터 user 가 키워드로 지정되어 user 라는 이름의 테이블을 생성할 수 없다고 설명해주신 내용이 있었다.&#x20;
* 그래서 그런가보다 하고 넘어가려다가 왠지 두 눈으로 확인하지 않으면 찝찝하길래 공식문서의 [changelog](http://www.h2database.com/html/changelog.html) 을 둘러보니, 어디에도 그런 내용은 찾아볼 수 없었다.&#x20;
* 그래서 Advanced 문서의 [keywords 파트 내용을](http://www.h2database.com/html/advanced.html#keywords) 보니, 이미 1992 년부터 user 는 키워드로 SQL 표준으로 지정된 상태였고, H2 에서도 이를 이미 적용한 것으로 나왔다.&#x20;
  * 물론 SQL 표준에 있지만 H2 에는 적용되고 있지 않다가 나중에 적용되었을 수도 있다!&#x20;
* 그래서 이러니 저러니해도 역시 키워드는 괜히 우회하는 방향으로 사용하지 말고, 처음부터 피하는게 낫겠다는 생각을 했다.&#x20;
* 이 부분에 대해서는 블로그 글쓴이 분께 댓글을 통해 자료를 요청드려놓은 상태이다. 어디서 볼 수 있을까, 이런 자료는...! (change log에도 없는데...!!!) &#x20;



## 5. 참고&#x20;

* [https://jeongkyun-it.tistory.com/184](https://jeongkyun-it.tistory.com/184)
* [http://www.h2database.com/html/changelog.html](http://www.h2database.com/html/changelog.html)
* [http://www.h2database.com/html/advanced.html#keywords](http://www.h2database.com/html/advanced.html#keywords)
* [https://blog.dkwr.de/development/fix-h2-error-expected-identifeir/](https://blog.dkwr.de/development/fix-h2-error-expected-identifeir/)
* [https://www.h2database.com/html/commands.html#set\_non\_keywords](https://www.h2database.com/html/commands.html#set\_non\_keywords)
* [https://github.com/h2database/h2database/releases](https://github.com/h2database/h2database/releases)
