---
description: HTTP, FTP, SMTP, SSH
---

# TCP/IP - Application Layer

* Applicion layer 계층에서 쓰이는 프로토콜은 HTTP, SMTP, SSH, FTP 가 대표적이다.&#x20;
* 웹 서비스. 이메일 등 서비스를 실질적으로 사람들에게 제공하는 층이다.&#x20;



## 1. HTTP

* Hypertext Transfer Protocol 은 서버와 브라우저 간에 데이터를 주고 받기 위해 처음 고안된 프로토콜이다.&#x20;
* 하지만 요즘에는 서버 간에도 통신할 때 많이 이용한다.&#x20;

### 1-1. 헤더를 통한 확장이 쉽다.&#x20;

* 헤더 값에다가 어떠한 값을 넣어서 HTTP 요청을 할 때, 쉽게 다른 값을 추가할 수 있다.&#x20;

### 1-2. HTTP는 stateless 하다. &#x20;

* 동일한 연결에서 연속적으로 수행되는 두 요청 사이에 연속적인 상태(state)이 없다.&#x20;
* 즉, 연속되는 두 개의 요청에서 서버와 클라이언트는 서로 그 이전의 송수신자임을 알지 못한다.&#x20;

## 2. SSH&#x20;

* Secure SHell Protocol 은 보안되지 않은 네트워크에서 네트워크 서비스를 안전하게 운영하기 위한 암호화 네트워크 프로토콜이다.&#x20;
* 예를 들면, 나의 로컬 노트북에서 AWS의 원격 EC2 서버에 접속하고자 할 때, 프라이빗키를 통해서 ssh 명령어를 통해 원격 접속을 할 수 있다.
  * ssh \[pem] \[user]@\[serverIP] &#x20;
* SCP 를 이용하면, SSH 를 이용해서 파일을 전송할수도 있다.&#x20;
  * scp \[source] \[destination]&#x20;

## 3. FTP

* File Transfer Protocol 의 약자로, 노드와 노드 간에 파일을 전송하는데 사용되는 프로토콜이다.&#x20;
* 요즘에는 파일을 암호화해서 전송하는 FTPS 나 SFTP로 대체되고 있다.&#x20;
* 예시&#x20;
  * FileZilla 소프트웨어를 통해 FTP 프로토콜로 원격 서버에 파일을 전송하거나 로컬컴퓨터로 다운받을 수 있다.&#x20;

## 4. SMTP&#x20;

* Simple Mail Transfer Protocol&#x20;
* 인터넷을 통해 메일을 보낼 때, 사용되는 프로토콜이다.&#x20;
* 흔하게 서비스를 운영하다보면, 메일링 기능이 필요하게 된다. 이 때, 반드시 SMTP 프로토콜을 이용하여 보내게 된다.&#x20;
  * javascript 진영에서는 Nodemailer 라는 라이브러리가 존재
    * JS 를 기반으로 SMTP 를 통해 메일 전달 할 수 있는 라이브러리

