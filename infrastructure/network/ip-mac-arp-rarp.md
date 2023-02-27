# IP 주소, MAC 주소, ARP, RARP

## 1. IP 주소

* internet protocol address 는 논리적인 주소이다.
* 컴퓨터 네트워크에서 장치들이 서로를 인식하고 통신을 하기 위해서 사용하는 특수한 번호이다.&#x20;
* IP 를 기반으로 통신한다고 알려져있지만, 사실은 IP 주소 밑에 있는 MAC 주소를 통해 통신한다. &#x20;

## 2. MAC 주소&#x20;

* Media Access Control 은 네트워크 인터페이스에 할당된 고유식별자&#x20;
* 보통 장치의 NIC 에 할당된다.&#x20;

### 2-1. 구성&#x20;

* 48비트 (=6 octets) 으로 이루어져 있으며
  * 그중 24비트는 제조사의 고유식별자인 OUI, Organizationally Unique Identifier
  * 24비트는 제조사에서 구별되는 코드인 UAA 으로 이루어져 있다.&#x20;
    * NIC(network interface controller)
* MAC 주소는 보통의 경우 유일하지만, 실수나 의도에 의해서 중복되게 만들수도 있다.&#x20;
  * 동일 네트워크에서 중복되지만 않으면 문제가 없다.&#x20;
  * NIC에 고정된 MAC 주소를 변경할 수 있으나, 하지 않는 것이 좋다. 할 수 없도록 막아놓은 OS 도 있다.&#x20;
* IEEE, Institute of Electrical and Electronics Engineers&#x20;
  * 전기, 전자, 전산 분야의 국제기구 및 학회&#x20;
  * 관련 전문가들이 합병해서 창설한 국제조직&#x20;
  * 관련 기술 공유와 표준 정의 등의 활동을 한다. &#x20;

## 3. ARP - RARP

* ARP, Address Resolution Protocol&#x20;
  * MAC 주소는 ARP 를 통해서 파악한다.&#x20;
  * ARP 를 통해서 논리적 주소인 IP 주소가 물리적 주소인 MAC 주소로 변환된다.&#x20;
* RARP, Reversed Address Resolution Protocol
  * 물리적 주소인 MAC 주소를 논리적 주소인 IP 주소로 변환하기도 한다.&#x20;

### 3-1. ARP&#x20;

* IP 주소에 맞는 MAC 주소를 찾기 위해서 해당 데이터를 **브로드캐스팅을** 통해 연결된 네트워크에 있는 장치한테 모두 보낸다.&#x20;
* 맞는 장치가 있다면, 해당 장치가 **유니캐스트로** 데이터를 전달해 주소를 찾게 된다.&#x20;

### 3-2. RARP&#x20;

* ARP 와 반대의 과정을 거친다.&#x20;



## 4. 나의 MAC 주소 찾기

* 아래의 명령어로 나의 MAC 주소 찾기&#x20;
* 아래의 웹사이트에서 나의 MAC 주소로 제조사 확인하기

```shell
// to find mac address in MAC OS
 ifconfig en0 | grep ether
	ether [mac address] 
```

{% embed url="https://regauth.standards.ieee.org/standards-ra-web/pub/view.html#registries" %}
