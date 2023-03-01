# TCP/IP - internet layer

## 1. internet layer&#x20;

* IP, ICMP, ARP 가 대표적
* 한 노드에서 다른 노드로 전송계층에서 받은 세그먼트나 데이터그램을 패킷화하여 전송한다.&#x20;

## 2. ICMP&#x20;

* Internet Control Message Protocol&#x20;
* 노드와 노드 사이에서 통신이 잘 되나 확인하기 위해서 사용하는 프로토콜이다.&#x20;
* 대표적으로 우리가 자주 사용하는 ping test!&#x20;
* IP와는 다르게, TCP or UDP 와 같은 전송계층 프로토콜과 연관되어있지 않고, 독립적인 비연결형 프로토콜이다.&#x20;



## 3. ARP

* Address Resolution Protocol (주소 매핑 프로토콜)
* 로컬 네트워크에서 IP 주소를 물리주소 MAC 주소로 매핑하는데 사용되는 프로토콜이다.&#x20;
* OSI 모델의 데이터 링크 계층에서 작동한다. LAN 에서 IP 주소와 MAC 주소의 매핑을 찾고 서로 통신하는데 도움을 준다.&#x20;
