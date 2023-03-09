---
description: 2. 배포하기
---

# aws로 그럴듯한 인프라 만들기 2 - 배포하기

> 이번 시리즈는 아래와 같이 진행된다. 이 글에서는 3번에 대해서 다룰 것이다. &#x20;
>
> 1. [PEM 키 생성하기 ](aws-2-1.md#1.-pem)
> 2. [망 구성하기 ](aws-1-1.md)
> 3. [배포하기](aws-2-1.md)&#x20;
> 4. [배포 스크립트 작성하기](aws-3-1.md)&#x20;



## 1. 외부망에 웹 어플리케이션 배포

### 1-1. git, java 설치하기

```sh
# git 은 ubuntu 에는 기본으로 설치되어있다. 설치 루트를 확인하자. 
which git 

# java 설치하기 
sudo apt update && upgrade 
sudo apt install default-jdk
```

### 1-2. 소스코드 배포, 빌드 및 실행  &#x20;

```sh
# git repository 를 clone 
git clone https://github.com/next-step/infra-subway-deploy.git
# 특정 브랜치만 클론하기 
git clone -b miniminis --single-branch https://github.com/next-step/infra-subway-deploy.git

# 가져온 프로젝트 빌드
$ ./gradlew clean build
# 테스트 없이 빌드
$ ./gradlew clean build -x test

# 빌드된 jar파일을 찾아보기
$ find ./* -name "*jar"

# jar file 실행하기
$ java -jar [jar파일명] & 

# 어플리케이션이 정상적으로 실행되었는지 확인하기 
$ curl http://localhost:8080

```

* **-Dserver.port=8000** 옵션을 활용하면 port를 변경할 수 있다.
*   **-Djava.security.egd 옵션을 확용하면,** 서버 시작 시간을 줄일 수 있다.&#x20;

    * 이 옵션을 붙이는 이유가 궁금하다면 [tomcat 구동 시 /dev/random 블로킹 이슈](https://lng1982.tistory.com/261) 참고

    ```sh
    $ java -Djava.security.egd=file:/dev/./urandom -jar [jar파일명] &
    ```
*   터미널 세션이 끊어질 경우, background로 돌던 프로세스에 hang-up signal이 발생해 죽는 경우가 종종 있다. 이 경우  [`nohup`](https://youngclown.github.io/2018/10/noup)명령어를 활용하여 실행하면 터미널 세션이 끊어져도 어플리케이션이 정상적으로 운영된다.&#x20;

    ```sh
    $  nohup java -jar [jar파일명] 1> [로그파일명] 2>&1  &
    ```

### 1-3. 로그 확인하기&#x20;

```sh
# java applicaion이 남기는 로그를 확인합니다.
$ tail -f [로그파일명]

# 파일을 압축하고 파일 소유자와 모드를 변경해봅니다.
$ tar -cvf [파일명] [압축할파일 또는 디렉터리]
$ sudo chown [소유자계정명]:[소유그룹명] [file이름] 
$ chmod [옵션] [파일명]

> https://ko.wikipedia.org/wiki/Chmod
```

* 브라우저에서  `http://{서버 ip}:{port}`로 접근해본다.

### 1-4. 어플리케이션 프로세스 종료하기&#x20;

```sh
# 프로세스 pid를 찾는 명령어
$ ps -ef | grep java
$ pgrep -f java

# 프로세스를 종료하는 명령어
$ kill -2 [PID]
```

* **SIGKILL 명령어는 쓰지 않는 것이 권장된다.**&#x20;
  * [https://stackoverflow.com/questions/2541475/capture-sigint-in-java](https://stackoverflow.com/questions/2541475/capture-sigint-in-java)

### 1-5.  실행된 명령어 이력 확인

* 그동안 터미널에서 실행된 모든 명령어 목록을 확인해볼 수 있다.&#x20;

```sh
$ history
```



## 2. DNS 설정

* [무료 도메인 사이트](https://xn--220b31d95hq8o.xn--3e0b707e/) 들을 활용하여 DNS 설정을 해본다.
* 어플리케이션을 배포한 public server 의 public ip 를 매핑한다.&#x20;
  * [http://subway-mhson.kro.kr](http://subway-mhson.kro.kr)

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

## 3. 웹 애플리케이션 앞단에 Nginx 로 Reverse Proxy 구성하기

### 3-1. Reverse Proxy?

<figure><img src="../../.gitbook/assets/image (95).png" alt=""><figcaption><p><a href="https://youtube.com/watch?v=4NB0NDtOwIQ&#x26;feature=shares">https://youtube.com/watch?v=4NB0NDtOwIQ&#x26;feature=shares</a></p></figcaption></figure>

일반적으로 Proxy 서버는 LAN -> WAN 으로의 요청을 대리하여 처리해준다. 예를 들어 내가 집에서 와이파이로 특정 국가의 서비스를 이용하려고 하는데, 해당 서비스에서 한국의 IP를 막아두었다면, 나는 VPN 등의 서비스를 통해서 다른 국가의 IP로 우회하여 해당 서비스에 접근하게 된다.&#x20;

Reverse Proxy의 경우는 그 반대라고 할 수 있다. WAN -> LAN 으로의 요청을 대리하여 처리해준다. 클라이언트로부터 어플리케이션 서버로 요청이 들어올 때, 중간에서 reverse proxy server 가 개입하여 다양한 전후처리를 해준다. 요청을 처리한 뒤, 어플리케이션 서버 역시 응답을 클라이언트로 바로 전달하지 않고, reverse proxy server 로 전달한다. 요청을 받은 reverse proxy server 는 서버 대신 클라이언트에게 응답을 전달한다.&#x20;

Reverse Proxy 를 사용하면 결국 클라이언트와 어플리케이션 서버 사이에서 필요한 다양한 전후처리를 할 수 있다. 어플리케이션 서버는 정말 비즈니스 로직, 그 자체에만 집중하면 된다.&#x20;

#### Reverse Proxy vs. Load Balancer&#x20;

* reverse proxy 는&#x20;
  * TLS 암호화 등을 맡아 보안성을 향상시키거나,&#x20;
  * 확장성을 향상시키거나,&#x20;
  * 압축, SSL 처리로 인한 백엔드 리소스 확보, 캐싱처리 등을 통해 웹의 속도를 향상시키는 역할을 한다.&#x20;
* 반면, load balancer 는&#x20;
  * 부하를 분산하고&#x20;
  * 서버 상태를 체크하며&#x20;
  * 세션을 관리하는 등의 역할을 수행한다.   &#x20;

하지만 Nginx의 경우는 두 가지의 역할을 모두 수행한다.&#x20;



### 3-2. 도커로 Nginx 띄우기&#x20;

도커를 이용하여 nginx를 proxy server로 세워보자.&#x20;

* 혹시 도커가 익숙하지 않다면, [도커 플레이그라운드](https://github.com/brainbackdoor/playground-docker/tree/master/week1)에서 간단한 명령어를 연습할 수 있다.&#x20;

```sh
# 1. 우선 도커를 설치한다. 
$ sudo apt-get update && \
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo apt-key fingerprint 0EBFCD88 && \
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
sudo apt-get update && \
sudo apt-get install -y docker-ce && \
sudo usermod -aG docker ubuntu && \
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
sudo chmod +x /usr/local/bin/docker-compose && \
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose


# 2. 아래와 같은 내용으로 Dockerfile 을 생성한다. 
FROM nginx

COPY nginx.conf /etc/nginx/nginx.conf  


# 3. nginx.conf 파일을 아래와 같이 작성한다. 
events {}

http {
  upstream app {
    server 172.17.0.1:8080;
  }

  server {
    listen 80;

    location / {
      proxy_pass http://app;
    }
  }
}

# 4. nextstep/reverse-proxy 라는 이름으로 도커 이미지를 빌드한 뒤, 80포트로 실행한다.  
$ docker build -t nextstep/reverse-proxy .
$ docker run -d -p 80:80 nextstep/reverse-proxy
```



## 4. Reverse Proxy에 TLS 설정

클라이언트 - 서버 간의 통신은 언제나 해킹의 위험에 노출되어있다. 그냥 평문으로 통신할 경우, 패킷이 가로채질 수 있으므로, 반드시 TLS 암호화를 하여 통신하도록 한다.&#x20;

### 4-1. letsencrypt 를 활용하여 무료로 TLS 인증서 받기&#x20;

```sh
# 1. docker 명령어를 통해서 내 도메인인 "subway-mhson.kro.kr"에 대한 인증서를 요청한다.  
docker run -it --rm --name certbot \
  -v '/etc/letsencrypt:/etc/letsencrypt' \
  -v '/var/lib/letsencrypt:/var/lib/letsencrypt' \
  certbot/certbot certonly -d 'subway-mhson.kro.kr' --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory
```

```sh
# 2. 위의 명령어를 실행하면, 아래와 같은 결과가 나온다. 
# 도메인 구입한 사이트에서 DNS TXT 레코드에 안내된 값을 추가해준다. 
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Requesting a certificate for subway-mhson.kro.kr

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name:

_acme-challenge.infra.subway-mhson.kro.kr.

with the following value:

jiRlP5PscTZ9f_kh1GfNbmBl6YiSt4i3MKo2irIHmgQ

Before continuing, verify the TXT record has been deployed. Depending on the DNS
provider, this may take some time, from a few seconds to multiple minutes. You can
check if it has finished deploying with aid of online tools, such as the Google
Admin Toolbox: https://toolbox.googleapps.com/apps/dig/#TXT/_acme-challenge.subway-mhson.kro.kr.
Look for one or more bolded line(s) below the line ';ANSWER'. It should show the
value(s) you've just added.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

```sh
# 3. 아래와 같은 결과가 나오면 성공! 
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/subway-mhson.kro.kr/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/subway-mhson.kro.kr/privkey.pem
This certificate expires on 2023-06-03.
These files will be updated when the certificate renews.

NEXT STEPS:
- This certificate will not be renewed automatically. Autorenewal of --manual certificates requires the use of an authentication hook script (--manual-auth-hook) but one was not provided. To renew this certificate, repeat this same certbot command before the certificate's expiry date.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```



DNS를 설정하는 사이트에서 DNS TXT 레코드를 추가한 후, 제대로 반영되었는지 dig 명령어로 확인한 후에 인증서 설정 진행을 계속한다.&#x20;

```sh
$ dig -t txt _acme-challenge.example.com +short
```



### 4-2. 생성된 인증서를 활용하여 Reverse Proxy 에 TLS 설정하기&#x20;

```sh
# 1. 우선 인증서 현재 경로로 옮기기 
$ cp /etc/letsencrypt/live/[도메인주소]/fullchain.pem ./
$ cp /etc/letsencrypt/live/[도메인주소]/privkey.pem ./


# 2. 위에서 생성했던 Dockerfile 을 아래와 같이 수정한다. 
FROM nginx

COPY nginx.conf /etc/nginx/nginx.conf 
COPY fullchain.pem /etc/letsencrypt/live/[도메인주소]/fullchain.pem
COPY privkey.pem /etc/letsencrypt/live/[도메인주소]/privkey.pem


# 3. nginx.conf 도 아래와 같이 수정한다. 
events {}

http {       
  upstream app {
    server 172.17.0.1:8080;
  }
  
  # Redirect all traffic to HTTPS
  server {
    listen 80;
    return 301 https://$host$request_uri;
  }

  server {
    listen 443 ssl;  
    ssl_certificate /etc/letsencrypt/live/[도메인주소]/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/[도메인주소]/privkey.pem;

    # Disable SSL
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    # 통신과정에서 사용할 암호화 알고리즘
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:!ADH:!AECDH:!MD5;

    # Enable HSTS
    # client의 browser에게 http로 어떠한 것도 load 하지 말라고 규제합니다.
    # 이를 통해 http에서 https로 redirect 되는 request를 minimize 할 수 있습니다.
    add_header Strict-Transport-Security "max-age=31536000" always;

    # SSL sessions
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;      

    location / {
      proxy_pass http://app;    
    }
  }
}


# 4. 방금 전에 띄웠던 도커 컨테이너를 중지 + 삭제하고, 새로운 설정을 반영하여 다시 띄운다. 
$ docker stop proxy && docker rm proxy
$ docker build -t nextstep/reverse-proxy:0.0.2 .
$ docker run -d -p 80:80 -p 443:443 --name proxy nextstep/reverse-proxy:0.0.2
```



## 5. 운영 데이터베이스 구성하기

일반적으로 데이터베이스를 컨테이너로 다루지는 않는다. 컨테이너는 사라지면 그만이니, 데이터베이스처럼 영속성을 가진 데이터를 다루기에는 적절하지 않은 면이 있다. 하지만, 지금은 데이터베이스가 중요한 것이 아니라 인프라 구축에 대한 실습을 하고 있는 것이므로, 도커를 이용해 빠르게 데이터베이스를 구축해보자.

```sh
# 1. 데이터베이스 실행하기 
$ docker run -d -p 3306:3306 brainbackdoor/data-subway:0.0.1

# 2. 데이터베이스 내부 접속하기  
$ docker ps -al 
$ docker exec -it [container id] /bin/bash

# 3. mysql 실행하여 데이터 확인하기 
mysql -u [id] -p 
(password 입력)

show databases;
use subway;

show tables;
select * from line;
```



## 6. 로컬/운영 설정 파일 나누기

지금까지의 실습에서는 데이터베이스의 데이터를 조회하지는 않았다. 이제 로컬 개발환경과 운영 환경이 구분되었으므로 application.properties 설정파일을 통해 운영 환경별로 데이터베이스 설정을 분리해본다.&#x20;

```yaml
---
# local

spring:
  config:
    activate:
      on-profile: local
  datasource:
    url: jdbc:mysql://localhost:3306/subway?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=Asia/Seoul&characterEncoding=UTF-8
    username: root
    password: masterpw
    driver-class-name: com.mysql.cj.jdbc.Driver

---
# prod

spring:
  config:
    activate:
      on-profile: prod
  datasource:
    url: jdbc:mysql://192.168.11.151:3306/subway?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=Asia/Seoul&characterEncoding=UTF-8
    username: root
    password: masterpw
    driver-class-name: com.mysql.cj.jdbc.Driver
```



어플리케이션 서버를 실행할 때에는 운영 환경 설정을 적용할 수 있도록 옵션을 추가한다.&#x20;

```sh
$ java -jar -Dspring.profiles.active=prod [jar파일명] 
```



## 7. Trouble Shooting&#x20;

### 7-1. letsencrypt 에서는 도메인과 서브도메인은 같은 인증서로 취급하지 않는다.&#x20;

1. 최초로 인증을 받을 때, 도메인을 입력하라고 되어 있어서 subway-mhson.kro.kr 주소로 TLS 인증서를 받았다.&#x20;
2. 그런데, 나는 서브도메인인 infra.subway-mhson.kro.kr 을 이용해서 서비스를 하고 있었다. 서브도메인까지 인증서가 적용될 것으로 기대했으나, 그렇지 않았다.&#x20;
3. 결국 서브 도메인인 infra.subway-mhson.kro.kr 로 인증서를 다시 받아서 TLS 암호화를 적용했다.&#x20;

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>

### 7-2. nginx에 TLS 관련 설정을 추가한 후, docker 컨테이너가 제대로 실행되지 않는다.&#x20;

문제상황은 다음과 같았다.&#x20;

1. TLS 인증서를 발급받은 뒤, Dockerfile 과 nginx.conf 를 수정하였다.&#x20;
2. 수정된 버전으로 다시 docker 이미지를 빌드한 뒤, 컨테이너를 실행시켰더니, 자꾸 상태가 Exit 으로 표시되었다.

해결과정&#x20;

1. docker logs \[container id] 명령어를 통해서 우선 어떤 문제가 있는지 원인을 파악하였다.&#x20;
   1. [이 글](https://velog.io/@swhybein/Docker-%EC%8B%A4%ED%96%89%ED%95%98%EC%9E%90%EB%A7%88%EC%9E%90-exit-%EB%90%A0-%EA%B2%BD%EC%9A%B0)을 보고 docker log 을 읽는 방법을 배웠다.&#x20;
2. 맨 마지막 줄의 메시지가 수상했다.&#x20;
   1. nginx: \[emerg] cannot load certificate key "/etc/letsencrypt/live/subway-mhson.kro.kr/privkey.pem": PEM\_read\_bio\_PrivateKey() failed (SSL: error:0909006C:PEM routines:get\_name:no start line:Expecting: ANY PRIVATE KEY)
   2. 해석해보자면 TLS 인증서의 private key 를 로드할 수 없다는 것으로 보인다.&#x20;
3. 한참을 이리저리 방법을 찾다가 혹시 권한이 없나 싶어서 private key 에 권한을 주었다.&#x20;

```sh
# docker logs [container] 
"nginx.conf" 40L, 1028B                                                                                                                   1,1           All
... [중략] ...
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: IPv6 listen already enabled
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/03/05 21:51:14 [emerg] 1#1: cannot load certificate key "/etc/letsencrypt/live/subway-mhson.kro.kr/privkey.pem": PEM_read_bio_PrivateKey() failed (SSL: error:0909006C:PEM routines:get_name:no start line:Expecting: ANY PRIVATE KEY)
nginx: [emerg] cannot load certificate key "/etc/letsencrypt/live/subway-mhson.kro.kr/privkey.pem": PEM_read_bio_PrivateKey() failed (SSL: error:0909006C:PEM routines:get_name:no start line:Expecting: ANY PRIVATE KEY)
```

```sh
# privkey.pem 의 권한 확인 결과 
-rw-rw-r-- 1 ubuntu ubuntu   54 Sep 27 22:09 Dockerfile
-rw-r--r-- 1 root   root   5599 Sep 27 22:27 fullchain.pem
-rw-rw-r-- 1 ubuntu ubuntu  155 Sep 27 22:10 nginx.conf
-rw------- 1 root   root   1704 Sep 27 22:28 privkey.pem     # 권한을 부여한다.
```

결과는? 정상적으로 동작된다.&#x20;

```
PUBLIC-2a:~$ docker ps -a
CONTAINER ID   IMAGE                          COMMAND                  CREATED         STATUS         PORTS                                                                      NAMES
bc1885093f7d   nextstep/reverse-proxy:0.0.2   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp   proxy
```



## 8. 추가적으로 읽어보면 좋을 글들&#x20;

이번 과제를 진행하면서 읽어보았던 블로그 글들이다. 좋은 내용이 많다.&#x20;

* [Let’s encrypt로 만든 인증서 살펴보기](https://keyhyuk-kim.medium.com/lets-encrypt%EB%A1%9C-%EB%A7%8C%EB%93%A0-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0-8b00a29a8bd3)
  * 인증서 내부의 구조를 자세하게 설명해준다.&#x20;
* [\[라인 개발블로그\] 안전한 SSL/TLS를 운영하기 위해 알아야 하는 것들](https://engineering.linecorp.com/ko/blog/best-practices-to-secure-your-ssl-tls)



## 9. 추가적으로 하면 좋은 것들&#x20;

> 비록 나는 시간이 없어서 적용해보지 못했지만, 아래와 같은 설정들을 추가해보면 개발시나 운영시에 편리해진다.&#x20;

### 9-1. \[추가] 데이터베이스 테이블 스키마 버전관리

보통 운영 데이터베이스는 JPA등과 같은 ORM 을 사용하여 기존의 테이블을 변경하지 않는다. 데이터가 유실되거나 참조 무결성 제약 등으로 인해 어려움이 있기 때문이다. 그래서 보통은 로컬에서는 H2 와 같은 in-memory 데이터베이스를 이용하여 빠르게 개발을 하고, 운영 데이터베이스는 점진적으로 migration 을 해나간다.&#x20;

이때, 운영 데이터베이스의 테이블 스키마에 대해서도 버전관리를 하는 것이 필요한데, 이때 사용하는 것이 바로 Flyway 이다.&#x20;

{% embed url="https://youtube.com/watch?v=pxDlj5jA9z4&feature=shares" %}

flyway 를 연습하고자 한다면 아래를 따라 진행해본다.&#x20;

* [예제 코드](https://github.com/woowacourse/java-deploy/tree/feat/flyway)를 통해 데이터베이스 스키마 관리 전략을 확인한다.
  * 예제코드를 실행하기에 앞서, [도커](https://www.docker.com/products/docker-desktop)를 다운로드한다.
  * **docker/db/mysql/init**에 dump 파일을 넣은 상태로 실행하면 자동으로 초기 데이터를 INSERT할 수 있다.
  * flyway는 **V\_\_\[변경이력].sql**의 형태로 **resources/db/migration/** 경로에서 괸라한다.&#x20;
  * 그리고 flyway\_schema\_history 테이블에 버전별로 checksum 값을 관리하므로 기존 sql 문을 수정해서는 안된다.

```sh
# 터미널에서 docker-compose.yml이 있는 위치로 이동한다.
$ cd docker
$ docker-compose up -d
```

**\* 기존 Database 존재시 flyway 적용 방법**

```sh
# application.properties
spring.flyway.baseline-on-migrate=true
spring.flyway.baseline-version=2
```

이전에 database가 존재할 경우 baseline 옵션을 활용하면 특정 버전(V2\_\_xx.sql 파일) 내용부터 적용이 가능하다.&#x20;

[발표자님의 블로그](https://ecsimsw.tistory.com/entry/Flyway%EB%A1%9C-DB-Migration)를 통해 더 자세한 내용을 확인할 수 있다.  \


### 9-2. \[추가] 설정 별도로 관리하기

키, 계정정보, 접속 URL 등 설정 정보를 소스코드와 함께 형상관리를 하게 되면, 보안상 이슈가 발생할 수 있다.&#x20;

보통의 경우는 어떻게 하는가?&#x20;

* Jenkins, Travis CI 등 배포 서버에 파라미터를 지정하거나, Spring Cloud Config, AWS service manager 등의 외부 서비스를 활용한다.&#x20;

우리는 어떻게 할 것인가?&#x20;

* 우리는 private repository 를 활용하여 설정을 관리해보도록 한다.&#x20;



1. 우선, github private 저장소를 생성한 뒤, application.properties 와 같은 설정 파일을 올린다.&#x20;
2.  git의 [서브모듈 기능](https://git-scm.com/book/ko/v2/Git-%EB%8F%84%EA%B5%AC-%EC%84%9C%EB%B8%8C%EB%AA%A8%EB%93%88)을 활용하여 특정 경로에 private repository를 참조하도록 설정한다.&#x20;

    ```sh
    $ git submodule add [자신의 private 저장소] ./src/main/resources/config
    ```
3.  이후에 소스코드를 받을 때는 서브모듈까지 clone 해야한다.&#x20;

    ```sh
    $ git clone --recurse-submodules [자신의 프로젝트 저장소]
    ```
4.  **설정 파일의 내용이 변경된 경우**

    ```sh
    git submodule foreach git pull origin main

    git submodule foreach git add .

    git submodule foreach git commit -m "commit message"

    git submodule foreach git push origin main
    ```

### 9-3. \[추가] 정적테스트(SonarLint)

* Sonarqube / ESLint 등 **정적 테스트**, Maven / Gradle 등을 활용한 **Build**, JUnit 등을 활용한 **동적 테스트** 등을 통해 Code로 인해 발생하는 문제를 조기에 발견할 수 있습니다. 어떻게 하면 테스트 비용을 줄일 수 있을지 늘 고민해보자.&#x20;
* [SonarLint](http://redutan.github.io/2018/04/11/intellij-sonarlint-plugin)를 활용하면 정적테스트 구축비용을 줄일 수 있습니다.
  * 정적 테스트를 통해 Coding Convention, 중복코드, 소스코드의 복잡도, 잠재적으로 버그 발생 가능성이 있는 코드, 테스트 커버리지 등을 파악할 수 있다.

### 9-4. \[추가] 로컬테스트(MultiRun)

* 로컬에서 서버를 띄울 때, IntelliJ의 [Multirun](https://plugins.jetbrains.com/plugin/7248-multirun) 플러그인을 활용하면 보다 손 쉽게 서버를 띄울 수 있다.
* Multi Run 플러그인 설치\
  ![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/d4a88de08f49446a9d3993bc934ee24a)
* Multi Run 설정
  * IntelliJ -> Run -> Edit Configurations...
  * Docker 설정\
    ![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/018d1274a23a4e75b87da1ed33e67b73)
  * NPM 설정\
    ![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/9de33c2550664ec8840a25b8d56d4d28)
  * Multi Run 설정\
    ![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/1b9970efeb584ddca4fc75f2456eb275)

