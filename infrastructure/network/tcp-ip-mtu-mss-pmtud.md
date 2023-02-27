# TCP/IP - MTU, MSS, PMTUD

## 1. MTU

* Maximum Transmission Unit 의 약자로,&#x20;
* 네트워크에 연결된 장치가 받아들일 수 있는 최대 데이터 패킷의 크기를 말한다.&#x20;
* 이 크기를 기준으로 데이터는 쪼개져서 패킷화가 된다.&#x20;
* 네트워크 경로 상에 있는 아무 장치나 MTU 보다 패킷이 크면, 그 패킷은 분할될 수 있다.&#x20;
* 예를 들어, 아래와 같은 과정을 통해 1500 bytes 의 데이터 패킷이 전송되고 있다고 해보자면,&#x20;
  * Computer
  * Router B(max : 1500b)
  * Router C(max : 1400b)
  * Server&#x20;
* 컴퓨터로부터 데이터 패킷이 출발하게 되면 이미 라우터B 내부에서 다음 라우터C를 걱정하여 패킷이 100 + 1400 처럼 둘로 쪼개져서 전달되게 된다. 그래야 라우터C를 무사히 통화할 수 있기 때문이다. &#x20;

### 1-1. 패킷이 분할되지 않을 때&#x20;

* 모든 패킷이 자동으로 분할되는 것이 아니다. 패킷이 분할될 수 없어서 네트워크 경로 상에 있는 어떠한 라우터나 장치의 MTU 를 초과한다면, 패킷을 분할해서 전달하는 것이 아니라, 아예 전달하지 않을수도 있다.&#x20;
* IPv6 : 아예 분할을 허용하지 않음 &#x20;
* IPv4 : flag 값에 따라 다르다.&#x20;
  * 0: reserved
  * 1: don't fragment
  * 2: more fragments&#x20;

## 2. MTU and MSS&#x20;

* MTU : Maximum Transmission Unit &#x20;
  * IP header + TCP header + data (payload)&#x20;
* MSS : Maximum Segment Size&#x20;
  * only data(payload)&#x20;
* 일반적으로 MTU는 1500 바이트이고, MSS는 1460 바이트이다.&#x20;
  * 20byte : ip header&#x20;
  * 20byte : tcp header&#x20;
* 즉, MTU = MSS + IP 헤더 + TCP 헤더&#x20;
* 따라서 네트워크를 통해 데이터를 보낼 때, MTU 가 1500이더라도, 데이터는 보통 1460 바이트 이하로 보내야 전송이 정상적으로 된다.&#x20;
* 그러나 TCP 등을 안쓴다면 이는 달라질 수 있음! &#x20;

### 2-1. MTU, MSS 테스트하기 (MAC 환경)

```shell
// 전송 데이터 유닛 사이즈가 1500 일때  
ping -D -s 1500 www.google.com
PING www.google.com (142.250.196.132): 1500 data bytes
ping: sendto: Message too long
ping: sendto: Message too long
Request timeout for icmp_seq 0
ping: sendto: Message too long
--- www.google.com ping statistics ---
9 packets transmitted, 0 packets received, 100.0% packet loss


// 전송 데이터 유닛 사이즈가 1460 일때 
 ping -D -s 1460 www.google.com
PING www.google.com (142.250.196.132): 1460 data bytes
76 bytes from 142.250.196.132: icmp_seq=0 ttl=114 time=39.657 ms
wrong total length 96 instead of 1488
76 bytes from 142.250.196.132: icmp_seq=1 ttl=114 time=39.159 ms
^C
--- www.google.com ping statistics ---
5 packets transmitted, 5 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 32.495/35.390/39.657/3.287 ms

```

* `ping`: IP 프로토콜에서 호스트간의 네트워크 연결이 정상적으로 작동하는지 테스트하는 명령어.
* `-D`: IP "Don't Fragment" 비트를 설정하는 옵션. 패킷 프레그먼트가 정상적으로 전송되어야 함을 요구합니다.
* `-s`: 데이터 길이를 지정하는 옵션. 이 경우 "mtu" 변수의 값이 사용됩니다.
* `www.google.com`: 패킷이 전송될 목적지 호스트 이름 또는 IP 주소입니다.
* `-c 1`: 패킷 하나만 전송하는 옵션.
* `> /dev/null 2>&1`: 패킷 테스트의 출력을 무시하도록 지정합니다.

### 2-2. PMTUD

* PMTUD : Path MTU Discovery&#x20;
* 수신자와 송신자의 경로 상에서 장치가 패킷을 누락한 경우, 테스트 패킷의 크기를 낮추면서 MTU에 맞게끔 반복해서 보내는 과정을 말한다.&#x20;



