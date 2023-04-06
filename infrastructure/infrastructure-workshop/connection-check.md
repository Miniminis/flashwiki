---
description: 통신 확인하기
---

# connection check

## 1. OSI 7 Layer&#x20;

{% embed url="https://www.youtube.com/watch?v=1pfTxp25MA8" %}

### 1-1. physical layer

* 역할 : Bit Stream(이진수 흐름)을 전기, 빛, 등의 신호로 변환
* PDU(Protocol Data Unit) :  Bit&#x20;
* 대표장비 : 케이블(LAN-UTP, WAN-Serial), 허브, 리피터, 커넷터(LAN-Rj45) 등&#x20;
* 유형범위 : 로컬 장비간에 전성된 전기 혹은 빛의 신호&#x20;

### 1-2. data-link layer&#x20;

* 역할 : 물리적 주소를 이용해서 노드(L2 스위치에 연결될 수 있는 3계층 이상의 장비) 간 연결&#x20;
* PDU : frame&#x20;
* 대표장비 : 브릿지, L2 스위치 등&#x20;
* 유형범위 : 로컬 장비 간에 전송된 하위 수준 데이터 메시지&#x20;
* 프로토콜 및 기술 : LAN-Ethernet Protocol, WAN-PPP, HDLC 등&#x20;

### 1-3. network layer&#x20;

* 역할 : 논리적 주소로 최초 출발지로부터 최종 목적지까지의 최적 경로를 결정한다.&#x20;
* PDU : packet, 데이터그램&#x20;
* 주소 : 논리적 주소 - IP(4 byte), IPX, Apple Talk&#x20;
* 대표장비 : 라우터, L3 스위치&#x20;
* 유형범위 : 로컬 혹은 원격 장비 간의 메시지&#x20;
* 프로토콜 및 기술 : IPv4, IPv6, ARP, ICMP, IGMP, Routing Protocol(RIP, EIGRP, OSPF)&#x20;

### 1-4. transport layer&#x20;

* 역할 : 포트번호를 이용해서 서비스를 구분하고 데이터의 전송을 담당한다.&#x20;
* PDU : segment&#x20;
* 주소 : Port(2 byte), well-known : 0-1023, 그외 : 1024-65535
* 대표장비 : L4 스위치&#x20;
* 유형범위 : 소프트웨어 프로세스 간의 통신&#x20;
* 프로토콜 및 기술 : TCP(확인응답, 신뢰성), UDP(빠른속도), NetBEUI&#x20;

### 1-5. session layer&#x20;

* 역할 : 응용 프로그램 간 세션 수립, 유지, 종료&#x20;
* 세션 : 두 사용자 간의 작업 시작부터 끝까지의 실시간 논리적 연결을 의미
* 유형범위 : 로컬 혹은 원격 장비 간의 세션&#x20;
* 프로토콜 및 기술 : NetBIOS, 소켓, 네임드 파이프, RPC&#x20;

### 1-6. presentation layer&#x20;

* 역할 : 데이터의 표현 - 확장자 연결, 압축, 암호화, 변환&#x20;
* 유형범위 : 어플리케이션 데이터 표현&#x20;
* 프로토콜 및 기술 : SSL, redirector, MIME&#x20;

### 1-7. application layer&#x20;

* 역할 : 사용자에게 인터페이스를 제공하고 원본 데이터를 생성한다.&#x20;
* PDU(Protocol Data Unit) : 사용자 데이터&#x20;
* 유형범위 : 어플리케이션 데이터&#x20;
* 프로토콜 및 기술&#x20;
  * HTTP(TCP\_80), HTTPS(TCP\_443), SMTP(TCP\_25), POP3(TCP\_110), FTP(TCP\_20,21), TFTP(UDP\_69), Telnet(TCP23), SSH(TCP\_22), DHCP(UDP\_67, 68), DNS(UDP\_53), SNMP(UDP\_161, 162)&#x20;



## 2. Wireshark&#x20;

* packet 을 캡쳐하여 통신 과정에서 일어난 이벤트를 기록할 수 있다.&#x20;

## 3. ping check&#x20;

* ping check 을 통해서 ip 정보만으로 서버에 요청이 가능한지 확인할 수 있다.&#x20;

### 3-1. ICMP&#x20;

* protocol 을 사용하며&#x20;
* IP 가 신뢰성을 보장하지 않아서 네트워크 장애나 중개 라우터 등의 에러에 대처할 수 없을때, 오류정보 발견 및 보고 기능을 담당하는 프로토콜이다.&#x20;
* TCP 가 아니기 때문에 포트번호가 없다.

```sh
//사용방법 : ping [대상 ip]
 ping google.com -c 1
PING google.com (142.250.207.110): 56 data bytes
64 bytes from 142.250.207.110: icmp_seq=0 ttl=56 time=36.357 ms

--- google.com ping statistics ---
1 packets transmitted, 1 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 36.357/36.357/36.357/0.000 ms
```

* RTT, Round Trip Time 이란 한 패킷이 왕복한 시간을 의미한다.&#x20;
* 네트워크 시간은 연결시간, 요청시간, 응답시간으로 구성된다.&#x20;
* RTT 가 높을 경우, 어느 구간에서 오래걸리는지 확인해야한다.&#x20;

### 3-2. ARP, Address Resolution Protocol&#x20;

* 논리적인 주소인 IP 주소를 이용해서 물리적인 주소인 MAC 주소를 알아와 통신 가능하도록 해주는 프로토콜이다. ARP Request 를 Broadcast 로 요청하면, 수신한 장비들 중, 자신의 IP에 해당하는 장비가 응답을 한다.&#x20;
* 응답받은 NIC 포트정보와 IP, MAC 주소를 기반으로 이후 통신을 진행하게 된다.&#x20;

## 4. Port Check&#x20;

* 서비스의 정상적인 구동 여부를 확인할 수 있다.&#x20;
* **`telnet [target server ip] [target service port]`**&#x20;
* 서버는 서비스에 하나의 포트번호를 오픈해두고 많은 사용자와 연결을 맺을 수 있다.&#x20;
  * 이유는 [소켓이 이용하는 정보를 확인](http://jkkang.net/unix/netprg/chap2/net2\_1.html)
* 그렇다면, 현재 서버에서 프로세스별로 몇 개의 연결이 가능한지 확인해볼 수 있을까?&#x20;
  * &#x20;[참고하기](https://techblog.woowahan.com/2569/)

```sh
# port forwarding  

## 원격지 서버에서 8080 포트로 소켓을 열어봅니다.
$ sudo socket -s 8080

## iptables 를 활용하여 port forwarding 설정을 합니다. 
## 아래의 설정은 80번 포트로 서버에 요청을 하면 서버의 8080번 포트와 연결해준다는 내용을 담고 있어요
$ sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080

## 서버의 공인 IP 확인해봅니다., 
$ curl wgetip.com

## 자신의 로컬에서 연결해봅니다.
$ telnet [서버의 공인 IP] 80

## 설정 삭제 
$ sudo iptables -t nat -L --line-numbers
$ sudo iptables -t nat -D PREROUTING [라인 넘버]
```

## 5. HTTP Response Check&#x20;

* Http [status code](https://tools.ietf.org/html/rfc2616#section-10) 를 기반으로 이상 유무를 확인할 수 있다.&#x20;
* 서비스 별로 사용하는 응답코드도 [다르고](https://gist.github.com/vkostyukov/32c84c0c01789425c29a), 의미도 [다르다](https://brainbackdoor.tistory.com/137).&#x20;
* [HTTP message](https://github.com/brainbackdoor/http-webserver/blob/master/docs/message/README.md) 형태 참고&#x20;

```sh

 curl -I google.com
HTTP/1.1 301 Moved Permanently
Location: http://www.google.com/
Content-Type: text/html; charset=UTF-8
Date: Mon, 27 Feb 2023 09:06:28 GMT
Expires: Wed, 29 Mar 2023 09:06:28 GMT
Cache-Control: public, max-age=2592000
Server: gws
Content-Length: 219
X-XSS-Protection: 0
X-Frame-Options: SAMEORIGIN
```

### 5-1. 우리는 어떻게 google.com 으로 요청할 수 있는가&#x20;

```sh
// Some code
$ sudo systemd-resolve --flush-caches
$ nslookup google.com
$ sudo tcpdump -nni eth0 port 53

```

1. 우선 /etc/hosts 파일에 정적으로 설정한 정보를 확인한다.&#x20;
2. 로컬의 DNS Cache를 확인한다.&#x20;
3. /etc/resolv.conf에 설정한 정보를 기반으로 DNS 서버에게 질의한다.&#x20;
4. DNS 서버는 정보가 있으면 반환하고 없으면 본인의 상위 DNS에게 질의를 하여 정보를 알아온다.
5. 도메인에 해당하는 IP를 알게되면 DNS Cache에 추가한다.

## 6. 패킷 분석&#x20;

* tcpdump, [wireshark](https://www.wireshark.org/download.html) 등의 도구를 사용하여 패킷을 모니터링할 수 있다.&#x20;

```sh

$ tcpdump host 192.168.0.207
$ tcpdump port 80
$ tcpdump net 192.168

```

