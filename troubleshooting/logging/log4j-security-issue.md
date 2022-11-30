---
title: Apache Log4j 보안 이슈 대응하기 (CVE-2021-45105)
date: 2022-01-06 07:22:13
category: logging
---

## 배경
![log4j-security-issue-01.png](/.gitbook/assets/log4j-security-issue-01.png)
지난 12월의 어느 날, 여느때와 다름없이 새벽기상을 했는데, 일어나자마자 시스템을 한번 쭉 둘러보며 모니터링을 하던 중에, 밤 사이 채널톡으로 들어온 문의 중에 다음과 같은 내용이 있는 것을 발견했다. 단전부터 올라오는 불안감을 느낄 수 있었지만, `에이, 로그인이 왜 안되겠어?` 하고 서비스를 접속했는데, 아뿔싸, 정말 로그인이 안되고 있었다 😱

## 문제내용
재빨리 서버 접근해서 spring 앱이 잘 돌고 있는지 확인했는데, 실행되고 있지 않은 것을 발견했다. 갑자기 앱이 왜 중단되었을까, 하고 서버 내 디렉토리를 확인해보니 내가 생성한 적이 없는 `run`, `run.1` 이라는 파일이 발견되었다. 우선은 해당 파일을 로컬로 옮겨두고, 다시 중단된 앱을 실행시켰더니 정상적으로 동작하는 것을 확인하였다. aws cloud watch 상에서도 특정 시간대 이후로 네트워크 통신량 및 CPU 사용량이 0이 었다는 것을 확인했고, API 상 로그 파일 내부도 살펴보았지만 별다른 Exception 없이 그냥 특정 시간대부터 로그가 찍히지 않았고, 앱을 재시작 하고 나서부터 다시 정상적으로 찍히는 것을 확인하였다. 

상황을 정리하자면, 아래와 같았다. 
1. 누군가가 백그라운드에서 실행중인 서비스를 중단시키는 스크립트를 `run`, `run.1` 이라는 파일명으로 UPX 패킹하였다. 
2. 해당 패킹파일을 원격으로 서버에 전달하여 실행까지 시켰다. 
3. 실행중이던 서비스가 중단되었다. 
4. 새벽에 일어난 나는 중단되어있었던 서비스를 다시 시작시켰다. 
5. 다시 정상적으로 운영되었다. 

중단된 앱을 다시 실행시킴으로서 서비스는 잘 돌아가겠지만, 아래와 같은 의문이 남아있었다. 
1. 어떻게 접근 제한이 걸려있는 원격 서버에 접근하여 파일을 전송하고 실행까지 시킬 수 있었는가?
2. 이번 건으로 인한 다른 영향은 없는 것일까?

## 원인/해결방법
옆자리 동료와 함께 이리저리 고민을 해봤지만, 뚜렷한 실마리를 찾지 못했다. 

### 커뮤니티에 도움 요청
![img_1.png](/.gitbook/assets/log4j-security-issue-02.png)
고민하던 중 OKKY 커뮤니티에 글을 올려서 다른 개발자 분들의 도움을 받아보기로 했다. 현재 우리가 처한 상황과 쓰고 있는 기술스택 등 배경을 설명하고 원인에 대해 고민하는 글을 남겼고 올린지 얼마 지나지 않아서 거짓말처럼 답글이 달렸다. 
`log4j 버전을 업데이트 했는지 여부`를 여쭤봐주셨는데, 혹시 하고 log4j 관련 이슈를 검색해보니 보안이슈가 있었다는 것을 알게 되었다. 

### 원인은 log4J 보안 취약점
OKKY 커뮤니티는 물론이고 각종 보안관련 공지를 해주는 [KISA 인터넷 보호나라](https://www.boho.or.kr/data/secNoticeView.do?bulletin_writing_sequence=36397)를 통해서 Apache Log4j 라이브러리 상에 보안 취약점이 발견되었다는 것을 알게 되었다. [관련 뉴스기사](http://www.cctvnews.co.kr/news/articleView.html?idxno=231988)도 있었다. 기존 log4J 라이브러리는 보안 이슈가 있다는 것을 알고 있어서 log4j2 를 사용하고 있었는데, 이마저도 보안상 취약점이 발견되면서 해당 라이브러리로 로깅을 하고 있었던 서비스 사들에서는 난리가 난 상황! 

[QnA 형식으로 알아보는 Apache log4j 취약점 대응 가이드](https://www.boho.or.kr/data/guideView.do?bulletin_writing_sequence=36390&queryString=YnVsbGV0aW5fd3JpdGluZ19zZXF1ZW5jZT0zNjM5MA==)를 참고하여 발생할 수 있는 피해를 확인하였고, 이에 대한 대응 방안을 확인할 수 있었다.
![log4j-security-issue-03.png](/.gitbook/assets/log4j-security-issue-03.png)

### spring-boot-starter-log4j2 도 교체가 필요
사실 spring boot starter 에서 기본적으로 제공해주는 log4j2 라이브러리를 이용하고 있어서 log4j 이슈와 관련이 있나 싶었는데, 해당 라이브러리가 log4j 2.13 버전을 의존하여 이슈와 연관이 있다는 것을 알게 되었다. 
```
implementation 'org.springframework.boot:spring-boot-starter-log4j2'
```
![img.png](/.gitbook/assets/log4j-security-issue-04.png)


## 적용결과
취약점 대응 가이드와 함께 문제를 대응하던 당시 기준으로 [제일 최근의 포스팅](https://veneas.tistory.com/entry/Spring-Boot-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-Log4J2-%EC%B7%A8%EC%95%BD%EC%A0%90-%EC%A1%B0%EC%B9%98-CVE-2021-45105)을 참고하여 아래와 같이 대응할 수 있었다.

### build.gradle
```
//log4j
//implementation 'org.springframework.boot:spring-boot-starter-log4j2'   // 삭제처리

implementation group: 'org.apache.logging.log4j', name: 'log4j-api', version: '2.17.0'
implementation group: 'org.apache.logging.log4j', name: 'log4j-core', version: '2.17.0'
implementation group: 'org.apache.logging.log4j', name: 'log4j-jul', version: '2.17.0'
implementation group: 'org.apache.logging.log4j', name: 'log4j-slf4j-impl', version: '2.17.0'
implementation group: 'org.slf4j', name: 'slf4j-api', version: '1.7.32'
implementation group: 'org.slf4j', name: 'jul-to-slf4j', version: '1.7.32'
```

### 우분투 해킹, 침입 흔적 확인하기
아래와 같은 블로그 포스팅들을 통해서 우분투 서버내 해킹이나 침입 흔적을 확인했고, 다행이도 별다른 이슈는 확인하지 못했다.

- https://doc.raonworks.com/?p=313
- https://yumserv.tistory.com/227
- https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=wjfqkrgo123&logNo=40128975116
- https://kit2013.tistory.com/185

## 느낀점
- 이번 일을 계기로 외부라이브러리 사용에 대해서 좀 더 경각심을 갖게 되었다. 편하다고 막 사용했다간, 보안이슈에 쉽게 노출될 수 있음을 알게 되었다.  😢