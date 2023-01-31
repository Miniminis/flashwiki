---
description: TCP 연결 성립 과정
---

# TCP 3way handshake

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption><p><a href="https://velog.io/@jjewqm/TCP-3-Way-HandShaking">https://velog.io/@jjewqm/TCP-3-Way-HandShaking</a></p></figcaption></figure>

## 1. 연결과정&#x20;

1. SYN
   1. 클라이언트는 서버에 클라이언트의 ISN 을 담아 SYN 을 보낸다.&#x20;
2. SYN + ACK &#x20;
   1. 서버는 클라이언트의 SYN 을 수신하고,&#x20;
   2. 자신의 ISN 을 보내게 되는데,&#x20;
   3. 승인의 의미로 클라이언트의 SYN+1 을 함께 담아 전달한다.&#x20;
3. ACK
   1. 클라이언트는 서버의 ISN 을 확인하고,&#x20;
   2. 서버의 ISN + 1을 승인의 의미로 담아 서버로 전달한다.&#x20;

### 즉, 총 과정을 살펴보자면,&#x20;

1. SYN:1 / ACK : 0
2. SYN:1 / ACK : 1
3. SYN:0 / ACK : 1

### 용어 설명&#x20;

* ISN : TCP 기반 데이터 통신에서 각각의 새 연결에 할당된 고유한 32비트 시퀀스 번호를 나타낸다.&#x20;
  * TCP 연결을 통해 전송되는 다른 데이터 바이트와 충돌하지 않는 시퀀스 번호를 할당하는데 도움이 된다.&#x20;
* SYN : Synchronization 의 약자. 연결 요청 플래그&#x20;
* ACK : Acknowledgement 의 약자. 응답 플래그

## 2. 클라이언트와 서버의 상태

* 이처럼 서버와 클라이언트 사이에 연결과정이 있기 때문에 TCP 의 신뢰성이 높다고, UDP 는 그것이 없기 때문에 신뢰성이 없다고 말한다.&#x20;
* closed&#x20;
  * 클라이언트와 서버는 모두 closed 된 상태에서 TCP 통신을 시작할 수 있다.&#x20;
* listen
  * 서버가 클라이언트의 연락을 받을 수 있는 상태. 대기하는 상태.&#x20;

