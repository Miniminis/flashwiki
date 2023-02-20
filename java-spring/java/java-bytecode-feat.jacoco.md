---
description: jacoco 같은 테스트 코드 커버리지 툴은 어떻게 만들어지는 것일까?
---

# java bytecode 를 조작해 테스트 코드 커버리지 확인하기 (feat.jacoco)

<figure><img src="../../.gitbook/assets/image (2) (12).png" alt=""><figcaption></figcaption></figure>

## 1. 테스트코드 커버리지 플러그인들은 어떤 원리로 동작하는가&#x20;

테스트 코드 커버리지를 확인하는 많은 툴들이 있다. 구글 검색시 최상단에 노출되는 jacoco plugin 을 살펴보자.&#x20;

> jacoco plugin 사용방법은 [이곳](https://docs.gradle.org/current/userguide/jacoco\_plugin.html)을 참고 &#x20;

위의 링크에 참고된 문서에 따라 차례대로 jacoco 플러그인을 설치한 뒤, gradle java project 에서 테스트 코드를 실행하면, build > 내부에 테스트 결과 리포트가 생성된다. 확인하면 대략 위와 같은 이미지로 테스트 코드가 다루는 범위를 눈으로 확인할 수 있다.&#x20;

### 1-1. 동작원리 &#x20;

* 이러한 툴들은 기본적으로 컴파일된 바이트 코드를 실행할 때, 실행이 되는 곳들과 안되는 곳들을 기억하고, 결과로 표시해주는 것이다.&#x20;



## 2. 느낀점&#x20;

* 부끄럽게도 jacoco 와 같은 테스트 코드 커버리지 툴을 쓰면서 매번 툴 사용법을 익히는데에만 급급했지, 이런 툴들이 어떤 원리로 동작하는 것일까 호기심을 가져본 적이 없었다.&#x20;
* 이번 강의를 들으면서 결국에는 특정 기술은, 특정 라이브러리는, 특정 프레임워크는 어떤 원리로 동작하는 것일까, 호기심을 갖고 탐구하는 연습을 많이 하게 되는 것 같다.&#x20;
* 이런 탐구의 과정이 진심으로 재미있다.&#x20;



## 3. 참고&#x20;

* [https://github.com/jacoco/jacoco](https://github.com/jacoco/jacoco)
