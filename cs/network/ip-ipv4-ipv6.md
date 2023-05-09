---
description: IP 주소는 IPv4, IPv6 로 나뉘어진다.
---

# IP 주소 - IPv4, IPv6

## 1. IPv4

* 32비트로 총 2^32개의 주소를 표현할 수 있다. = 약 41억 9천만개&#x20;
* 32비트를 8비트 단위로 점을 찍어서 4개로 구분한다. = 4개의 옥텟&#x20;
* 8비트를 10진수로 표현한다.&#x20;
* 부족하기 때문에 NAT, 서브네팅 등 부가적인 기술이 필요하다.&#x20;

&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1) (2) (2).png" alt=""><figcaption><p><a href="https://www.c-sharpcorner.com/article/getting-started-with-vpc-virtual-private-cloud-part2/">https://www.c-sharpcorner.com/article/getting-started-with-vpc-virtual-private-cloud-part2/</a></p></figcaption></figure>



## 2.  IPv6

* 128비트, 2^128 개의 주소를 표현할 수 있다.&#x20;
  * 많은 주소를 처리할 수 있기 떄문에 NAT, 서브네팅이 굳이 필요하지 않다.&#x20;
* 16비트씩 8개로 구분된다. 이때 16비트는 16진수로 표현되며, 콜론으로 구분하여 표시&#x20;
* 앞의 연속되는 0은 생략가능
* IPSec 이라는 데이터 패킷을 암호화하는 보안 네트워크 프로토콜 제품군이 내장되어있어서 보안성이 뛰어나다.&#x20;

<figure><img src="../../.gitbook/assets/image (1) (1) (3) (4).png" alt=""><figcaption><p><a href="https://www.orbit-computer-solutions.com/ipv6-address-expression-and-examples/">https://www.orbit-computer-solutions.com/ipv6-address-expression-and-examples/</a></p></figcaption></figure>



## 3. IPv4 vs. IPv6&#x20;

### 3-1. 단순해진 헤더&#x20;

* IPv4 에서 사용되던 불필요한 헤더 필드들을 제거하여 훨씬 단순해졌고, 빨라졌다.&#x20;
* IPv4 는 헤더가 가변길이이지만, IPv6 는 40바이트로 고정되어있어서 인터넷 헤더에 대한 정보도 삭제되어있다.&#x20;
  * 그밖에 여러가지 불필요한 필드들이 삭제됨 &#x20;

<figure><img src="../../.gitbook/assets/image (14) (5).png" alt=""><figcaption><p><a href="https://www.networkacademy.io/ccna/ipv6/ipv4-vs-ipv6">https://www.networkacademy.io/ccna/ipv6/ipv4-vs-ipv6</a></p></figcaption></figure>

### 3-2. Checksum&#x20;

* IPv4에서는 header checksum 이 존재해서 데이터의 오류를 검사했었는데, IPv6 에서는 그것이 빠졌다.&#x20;
* 대신 internet layer 보다 상위 단계인 trasport layer 에서 TCP 나 UDP 가 체크섬을 가지고 있기 때문에 그곳에서 처리된 데이터를 믿는 것으로 한다.&#x20;
* 참고로 UDP 에서는 체크섬이 필수가 아니기 때문에 선택사항이다. 따라서 UDP + IPv6 인 경우, 반드시 체크섬 필드를 사용한다고 설정해야한다.&#x20;
* CRC&#x20;
  * 순환중복검사로 네트워크 상에서 데이터에 오류가 있는지 확인하기 위한 체크값을 결정하는 방식이다.&#x20;
  * 데이터 전송하기 전에 데이터 값에 맞춰 CRC 를 계산하고, 데이터에 붙여서 전송한다.&#x20;
  * 데이터를 수신하고 나면, 다시 CRC 값을 계산한 뒤, 붙여진 이전의 CRC 와 비교한다.&#x20;
  * 만약 두 값이 다르다면 전송 과정에서 오류가 덧붙여 전송된 것임을 알 수 있다.&#x20;

<figure><img src="../../.gitbook/assets/image (62) (2).png" alt=""><figcaption><p><a href="https://skminhaj.wordpress.com/2016/02/15/tcp-segment-vs-udp-datagram-header-format/">https://skminhaj.wordpress.com/2016/02/15/tcp-segment-vs-udp-datagram-header-format/</a></p></figcaption></figure>

### 3-3. TTL -> HOP limit&#x20;

* TTL은 패킷이 네트워크에서 무한순환하지 않도록 하는 변수이다.&#x20;
* 패킷이 네트워크에서 라우터를 거칠때마다 TTL 값은 1씩 감소한다. 값이 0이되면 패킷은 폐기된다.&#x20;
* IPv6 에서는 이러한 값이 장치를 거치는 최대 대수를 제한한다는 의미로 hop limit 으로 대체된 것이다.&#x20;

### 3-4. 속도

* 비록 IPv6 는 IPv4 에 비해서 헤더도 단순해지고 불필요한 필드도 삭제되어 기본적으로 빠르고 IPSec 이라는 네트워크 보안제품군이 내장되어 보안성도 훨씬 우수하다.&#x20;
* 하지만 항상 그런 것은 아니고, 가끔 더 느린 경우도 있다고 한다.&#x20;

