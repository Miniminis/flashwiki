# TCP/IP 4계층, OSI 7 layer

## 1. 개념&#x20;

* 인터넷 상에서 장치와 장치 간에 서로 데이터를 주고 받을 때 사용하는 "독립적인" 프로토콜
* TCP : Transmission Control Protocol&#x20;
* IP :  Internet Protocol&#x20;
* 인터넷을 통해 데이터를 보낼 때, 주로 TCP와 IP를 이용해서 보내기 때문에 같이 자주 사용된다.&#x20;



## 2. TCP/IP 4계층&#x20;

1. Application layer
   1. Http/Https, SMTP, FTP, SSH&#x20;
2. Transport layer
   1. TCP, UDP&#x20;
3. Internet layer&#x20;
   1. IPv4/IPv6, ICMP, ARP&#x20;
4. Network Access (=Link) layer



### 2-1. application layer &#x20;

* HTTP, SMTP, FTP, SSH 가 대표적&#x20;
* 웹서비스, 이메일등 서비스를 실질적으로 사람들에게 제공하는 계층이다.&#x20;
* 브라우저라는 인터페이스를 통해 응용계층의 서비스를 이용하는 셈이다. &#x20;

### 2-2. transport layer&#x20;

* TCP, UDP 가 대표적&#x20;
* 에플리케이션 계층에서 받은 메시지를 **세그먼트**(TCP에서) 혹은 **데이터그램**(UCP)으로 쪼갠다.&#x20;
* 데이터가 오류없이 순서대로 전달되도록 도움을 준다.
* 말하자면 송신하는/수신하는 데이터의 오류 및 순서를 체크하는 역할&#x20;

### 2-3. internet layer

* ICMP, IP, ARP 가 대표적이며, 한 노드에서 다른 노드로 전송 계층에서 받은 세그먼트나 데이터그램을 **패킷화하여** 목적지로 전송하는 역할을 담당한다.&#x20;
* 말하자면, 주소로 전송하는 역할을 담당! &#x20;

### 2-4. network access layer (=link layer)&#x20;

* 전선, 광섬유, 무선 등으로 데이터가 네트워크를 통해 물리적으로 전송되는 방식을 정의한다.&#x20;
* 실질적으로 장치와 장치간 연결



{% hint style="info" %}
이처럼 우리가 브라우저를 통해 어떠한 데이터를 전송하고 수신하는 과정에서 우리가 보는 데이터들은 위와 같이 4개의 계층을 거쳐 세그먼트/데이터그램으로 나누어지고 패킷화되어 전송된다.&#x20;
{% endhint %}



## 3. 캡슐화와 비 캡슐화&#x20;

* 네트워크에서 캡슐화는 송신자가 수신자에게 데이터를 보낼 때, 기본 데이터에 각 계층의 특징들이 담긴 헤더가 붙여지는 것을 의미한다.&#x20;
* 예를 들면 구글에서 뭔가를 검색하게 되면, TCP/IP 4계층을 통과하면서 아래와 같은 과정을 거쳐 데이터가 만들어지게 된다.
  * application : 기본 데이터&#x20;
  * transport : TCP header + 기본데이터&#x20;
  * internet : IP header + TCP header + 기본 데이터&#x20;
  * link : frame header + IP header + TCP header + 기본 데이터 + frame trailer&#x20;
  * bit sequence&#x20;
* 비캡슐화는 메시지를 수신하는 쪽에서 위의 과정을 거꾸로 거쳐가며 application 계층까지 도달하는 것을 말한다.&#x20;

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p><a href="https://velog.io/@seonja/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B5%AC%EC%A1%B0-OSI7-Layer-TCPIP-%EC%BA%A1%EC%8A%90%ED%99%94">https://velog.io/@seonja/네트워크-구조-OSI7-Layer-TCPIP-캡슐화</a></p></figcaption></figure>

## 4. PDU&#x20;

* protocol data unit : TCP/IP 4계층을 기반으로 설명할 때, 각 계층에서 부르는 데이터 단위를 의미한다. 예를 들면,&#x20;
* application layer : message&#x20;
* transport layer : segment(tcp), datagram(udp)
  * 세그먼트(데이터그램) : 적절한 크기로 쪼갠 조각&#x20;
* internet layer : packet
  * 페킷 : 세그먼트에 SP(source ip address)와 DP(destination ip address)가 붙은 형태의 조각 &#x20;
* link layer : frame(data link layer), bit(physical layer)
  * 프레임 : MAC 주소 헤더와 CRC/Checksum trailer 가 붙은 조각&#x20;

### 4-1. IP header

* sp : 송신자의 32비트 IP 주소&#x20;
* dp : 수신자의 32비트 IP 주소 &#x20;

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption><p>wikipedia</p></figcaption></figure>

### 4-2. frame&#x20;

* CRC/Checksum trailer
  * CRC (Cyclic Redundancy Check)는 데이터 전송 과정에서 데이터의 정확성을 검증하기 위해 사용되는 에러 검출 기술
  * Checksum trailer 는 데이터 전송과정에서 데이터의 정확성을 검증하기 위해 추가로 전송되는 데이터의 합계를 말한다. 수신자는 받은 데이터와 함께 이 체크섬 트레일러를 확인함으로써 데이터의 정확성을 확인할 수 있다.&#x20;
* 데이터 오류감지를 위한 수학적인 함수가 적용된 값을 의미한다. 과도한 트래픽으로 인해 링크 계층에 오류가 발생하여 데이터가 손상되었다면 이를 감지하는 역할을 한다.&#x20;
* 모든 계층에 전달되는 데이터가 쪼개져서 "패킷"으로 전달된다고 하는 것은 일반적으로는 맞는 말이지만, PDU에 따라서 계층별로 데이터를 다르게 부르는 것이 더 맞는 표현이다.&#x20;
* FCS : Frame Check Sequence. 네트워크에서 프레임의 정확성을 체크하기위해 사용하는 기술.&#x20;

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption><p><a href="https://ipcisco.com/lesson/ethernet-basics/">https://ipcisco.com/lesson/ethernet-basics/</a></p></figcaption></figure>



## 5. OSI 7 layer

TCP/IP 에서는 4개로 계층을 분리한 것에 반해, OSI 에서는 7개의 계층으로 나누어 표현하였다.&#x20;

* TCP/IP 4 layer&#x20;
  * application
  * transport
  * internet
  * network access = link&#x20;
* OSI 7 layer&#x20;
  * application&#x20;
  * presentation&#x20;
  * session&#x20;
  * transport&#x20;
  * network&#x20;
  * data link layer&#x20;
  * physical layer&#x20;

