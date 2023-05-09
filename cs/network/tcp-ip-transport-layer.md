---
description: TCP, UDP
---

# TCP/IP - Transport Layer

* transport layer 에서는 TCP, UDP 가 대표적
* 어플리케이션 계층에서 받은 메시지를 세그먼트 혹은 데이터그램 단위로 분할한다.&#x20;
* 데이터가 오류없이 순서대로 전달될 수 있도록 한다. &#x20;

## 1. TCP&#x20;

### 1-1. 가상회선 패킷 교환 방식

* 송신 호스트 - 수신 호스트 사이의 통신에서 패킷들이 1, 2, 3 순서대로 보내지면 1, 2, 3 순서대로 도착하는 모습을 확인할 수 있다.&#x20;
* 즉, 전송된 데이터의 순서와 정확성을 보장한다. &#x20;

### 1-2. 오류검사 메커니즘&#x20;

* 재전송 : 시간 초과 기간이 지나면 서버는 전달되지 않은 데이터에 대해서 재전송을 시도한다.&#x20;
* 체크섬 : 체크섬을 통해서 데이터의 무결성을 평가한다. 송신된 데이터의 체크섬과 수신된 데이터의 체크섬 값을 서로 비교하여 올바르게 왔는지를 확인한다.&#x20;

### 1-3. header&#x20;

* 20-60 byte 로 길이는 가변적이다.&#x20;

<figure><img src="../../.gitbook/assets/image (12) (3) (2).png" alt=""><figcaption><p><a href="https://www.lifewire.com/tcp-headers-and-udp-headers-explained-817970">https://www.lifewire.com/tcp-headers-and-udp-headers-explained-817970</a></p></figcaption></figure>

## 2. UDP

### 2-1. 데이터그램 패킷 교환 방식&#x20;

* 송신 호스트 - 수신 호스트 사이의 통신에서 패킷들이 1, 2, 3 순서대로 보내진다고 해도, 도착 순서를 보장할 수 없다.&#x20;

### 2-2. 단순 체크섬만 지원&#x20;

* 오류 검사는 단순한 체크섬만 지원한다.&#x20;

### 2-3. 헤더

* 8바이트로 고정된 길이이다. &#x20;

<figure><img src="../../.gitbook/assets/image (7) (2) (2) (1).png" alt=""><figcaption><p><a href="https://cheapsslsecurity.com/blog/how-udp-works-a-look-at-the-user-datagram-protocol-in-computer-networks/">https://cheapsslsecurity.com/blog/how-udp-works-a-look-at-the-user-datagram-protocol-in-computer-networks/</a></p></figcaption></figure>

## 3. 비교하기 : TCP vs. UDP&#x20;

* 패킷의 교환방식&#x20;
  * 가상회선 패킷 교환방식 - 데이터그램 패킷 교환방식&#x20;
* 신뢰성&#x20;
  * 신뢰도가 높음 - 신뢰성 낮음&#x20;
* 오류검사&#x20;
  * 문제 발생시 재전송, 체크섬 지원 - 체크섬만 지원&#x20;
* 패킷의 순서보장&#x20;
  * 순서 보장 - 순서 보장 안함&#x20;
* 헤더 길이&#x20;
  * 20-60byte 가변적 - 8byte 고정적&#x20;
* 연결보장&#x20;
  * 연결을 보장한다. 3way 핸드 쉐이크로 연결을 맺고, 4way 핸드쉐이크로 연결을 해제하는 작업을 한다.&#x20;
  * 연결을 보장하지 않는다. 그냥 데이터를 보낸다. 연결을 유지하고 해제하는데 드는 비용이 없다.&#x20;
* 브로드캐스트 지원&#x20;
  * 지원하지 않는다. - 지원한다.&#x20;
* 속도&#x20;
  * 느림 - 빠름&#x20;
