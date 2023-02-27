---
description: TCP 연결 해제 과정
---

# TCP 4way handshake and TIME\_WAIT

<figure><img src="../../.gitbook/assets/image (5) (3) (1).png" alt=""><figcaption><p><a href="https://sjlim5092.tistory.com/37">https://sjlim5092.tistory.com/37</a></p></figcaption></figure>

## 1. 과정

* 클라이언트와 서버 둘다 ESTABLISED 상태에서 시작한다.&#x20;
* 클라이언트에서 먼저 closed() 호출하여 FIN 을 서버에 보낸다.&#x20;
  * FIN : 전송자가 데이터 전송을 완료했고 더이상 응답을 기대하지 않는다는 것을 알리는 플래그. 세그먼트 형태로 전달된다.&#x20;
  * 클라이언트는 이 직후, FIN\_WAIT\_1 상태로 전환된다.&#x20;
* 서버는 클라이언트에게 ACK 플래그를 전달하고, 자신도 CLOSE\_WAIT 상태로 전환된다. 이때 클라이언트는 FIN\_WAIT\_2 로 전환된다.&#x20;
* 서버는 LAST\_ACK 상태가 되고 **일정한 시간 이후에** 클라이언트에 FIN 이라는 세그먼트를 보낸다.&#x20;
* 클라이언트는 서버로부터 FIN 을 받고 나서 TIME\_WAIT 상태로 전환된다. 다시 서버에게 ACK 플래그를 보낸다. 그러면 서버 역시 CLOSED 상태가 된다.&#x20;
* 클라이언트는 어느정도의 시간 (TIME\_WAIT)을 대기한 후에 연결이 닫히게 된다.&#x20;

## 2. TIME\_WAIT

* 소켓이 바로 소멸되지 않고, 일정 시간 유지되는 상태를 말하며, 지연 패킷 등의 문제점을 해결하는데 쓰인다.   &#x20;
  * 지연 패킷이 존재할 가능성이 있기 때문에 얼마간 기다려서 데이터의 무결성을 보장하기 위함이다.&#x20;
  * 보통 두배의 최대 세그먼트 수명(MSL, Maximum Segment Lifetime) 시간을 기다린다.&#x20;
  * 기본적으로는 MSL은 2분이다.
    * 하지만 OS에 따라서 다르다.&#x20;
    * CentOS, Ubuntu : 60sec
    * Window : 4min &#x20;
* 또한, 연결을 올바르게 닫힌 상태로 만들기 위해서도 존재한다. 만약 클라이언트나 서버가 CLOSED 상태로 종료되지 않고, LASK\_ACK 상태로 남아있다면, 다음 연결때 오류가 난다.&#x20;

