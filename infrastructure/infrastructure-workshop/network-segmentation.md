---
description: 망분리
---

# network segmentation

## 1. 통신망&#x20;

### 1-1. 정의&#x20;

* IP 로 식별할 수 있는 노드들과 그 노드들을 잇는 물리적 회선인 링크들로 구성된 하나의 시스템을 말한다.&#x20;
* 즉, 하나의 subnet 을 하나의 망이라고 칭할 수 있다.&#x20;

### 1-2. 네트워크 장비&#x20;

* L2 Switch
  * multiple access 를 위한 장비&#x20;
  * 서버에는 network interface card 가 존재하고 그 내부에는 MAC 주소가 있음&#x20;
  * 통신방식&#x20;
    * MAC 테이블에 주소가 존재할 때, forwarding 한다.&#x20;
    * MAC 테이블에 주소가 존재하지 않을 때, flooding 하여 응답하는 장비가 있다면, 그 포트에 장비가 있다고 인지하고 MAC 테이블에 등록한다. &#x20;
  * **왜 multi access 를 위해서는 switch 장비를 써야하는가?**&#x20;
    * **Collision domain** 이란, half duplex 로 multi access(다중접속환경)을 구성했을 경우, 데이터를 동시에 전송했을 때, 충돌이 일어날 수 있는 영역을 말한다.&#x20;
    * 허브를 통해 multi-access 환경을 만들었을 경우, 1계층 장비이기 때문에 신호를 증폭하여 collision domain 의 범위를 확장시킨다.&#x20;
    * 반면에 switch 를 통해서 환경을 구성하였을 경우, 2계층 장비이기 때문에 신호 증폭도 시키지만, 동시에 2계층의 헤더를 열어 MAC 주소를 확인하고 전송하여 Port 별로 collision domain 을 나누기도 한다.&#x20;
    * 이러한 이유 때문에 multi-access 환경을 구성할 경우, Switch 를 사용하는 것이 좋다.&#x20;
* Router&#x20;
  * 서로 다른 네트워크 간의 통신을 중계한다.&#x20;
  * router 처럼 3계층 이상의 네트워크 장비를 이용하여 망을 구성하게 되는데, 3계층의 역할은, 논리적 주소로 최초 출발지로부터 최종 목적지까지의 최적경로를 결정하는 것이다.&#x20;
  * 통신방식&#x20;
    * MAC 테이블에 주소가 존재할때, forwarding 한다.&#x20;
    * MAC 테이블에 주소가 존재하지 않을 때, drop 하여 추가 조치없이 종료한다.&#x20;
  * 라우팅 프로토콜을 활용하여 어떤 대역으로 패킷을 보내는 것이 최적 경로인지 학습한다.&#x20;
* 인터넷 통신&#x20;
  * 외부 네트워크와 통신하기 위해서는 public ip 가 있어야 한다.&#x20;
  * 라우터는 private ip 가 목적지일 경우, 인터넷 구간으로 보내지 않고 drop 해버린다.&#x20;
  * 따라서 nat gateway 를 이용하여 private ip 를 public ip 로 변환해주어야 한다.&#x20;
  * 자신이 속한 subnet 의 서버는 가상 스위치를 통해서 직접 통신할 수 있다.&#x20;
  * 자신이 속하지 않은 subnet 의 서버 역시 가상 라우터를 통해서 직접 통신이 가능하다.&#x20;
  * 그 이외에 0.0.0.0/0 (전체 대역)은 internet gateway 로 통신을 보내도록 학습해야한다.  &#x20;

### 1-3. Collision Domain & Broadcast Domain&#x20;

* Collision Domain
  * 네트워크에서 충돌이 발생할 수 있는 영역이다.&#x20;
  * 물리적인 네트워크 세그먼트를 기준으로 결정된다.&#x20;
  * 이 안에 있는 모든 장치들은 모두 동일한 물리적 세그먼트에서 데이터를 전송하고 수신하기 때문에 둘 이상의 장치가 동시에 데이터를 전송하면 충돌이 발생할 수 있다.&#x20;
  * 이때 충돌이 발생하면 충돌된 패킷들은 모두 손실되고 재 전송되어야 한다.&#x20;
  * 때문에 collision domain 의 범위는 가능한한 작을수록 좋다.&#x20;
* Broadcast Domain&#x20;
  * 네트워크에서 브로드캐스트 패킷이 전파되는 영역
  * 라우터, 스위치 같은 네트워크 장비의 경계를 기준으로 결정된다.&#x20;
  * 이 내부에서는 브로드캐스트 패킷이 전체 장비에게 전파되기 때문에 브로드캐스트 패킷의 양이 많아질수록 네트워크 성능이 저하될 수 있다.&#x20;
  * 따라서 이 크기 역시 작을수록 네트워크 성능 향상에 도움이 된다.&#x20;
* 스위치&#x20;
  * Collision Domain 과 Broadcast Domain 을 나누는 데 중요한 역할을 한다.&#x20;
  * 물리적인 세그먼트 간에 패킷을 전달하면서, Collision Domain 을 분리해주고, Broadcast Domain 을 제한해준다.&#x20;
  * [Switch 에 대하여](https://brainbackdoor.tistory.com/115)&#x20;
* 라우터&#x20;
  * 브로드캐스트 패킷을 차단하고,&#x20;
  * Broadcast Domain 을 분리해주며,&#x20;
  * 다른 네트워크와 통신하면서 Collision Domain 도 분리해준다.&#x20;



## 2. 망 분리를 해야하는 이유

* 개인정보를 다루는 데이터베이스 서버 등을 위한 내부 망, 사용자가 접근하는 웹 서버를 위한 외부망으로 분리하여 구성합니다.&#x20;
* 참고 : [dense in depth](https://en.wikipedia.org/wiki/Defense\_in\_depth\_\(computing\))



<figure><img src="../../.gitbook/assets/infrastructure.drawio (1).png" alt=""><figcaption><p>AWS 3-Tier Network Example</p></figcaption></figure>

## 3. AWS&#x20;

### 3-1. Region : 국가, 지역 &#x20;

* [AWS region and area](https://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)&#x20;

### 3-2. Availability Zone

* 물리적으로 나뉜 IDC&#x20;
* 데이터 센터를 의미한다. (ap-northeast-2a, ap-northeast-2b 등)
  * 한국은 ap-northeast-2&#x20;
* 서브넷을 구성할 때, AZ 를 함께 설정하는데, 재난 및 재해에 대비하여 Subnet 의 AZ 를 다르게 구성할 것이 권장된다.&#x20;
* 하나의 VPC 내에 구성된 Subnet 들은 물리적으로 다른 IDC 에 구성되더라도 사설망을 통해서 통신이 가능하다. 다른 AZ 간의 통신 비용이 발생하기도 하지만, 미비한 수준이다.&#x20;

### 3-3. VPC

* 하나의 region 에 종속되며 다수의 availability zone 을 설정 가능하다.&#x20;
* VPC IP 대역 내에서 망을 구성한다.&#x20;
* 사설 IP 대역으로 CIDR 을 설정하고 생성한다.&#x20;
* N개의 서브넷을 가질 수 있다.&#x20;
* [Reserved Ip Address](https://en.wikipedia.org/wiki/Reserved\_IP\_addresses)
* [AWS documentation - VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)&#x20;



#### VPC 관련 개념 정리&#x20;

DHCP

* 동적으로 IP 를 할당하는 곳&#x20;

라우팅 테이블

* 서로 다른 대역대들 간에 통신을 할 수 있도록 정책을 관리해주는 곳

ACL(Access Control List)

* 네트워크에서 패킷의 흐름을 제어하기 위한 보안 기술 중 하나.&#x20;
* 서브멧과 VPC 수준에서 설정
* 인바운드, 아웃바운드 트래픽에 대한 제어
* 서버의 보안을 강화하거나 특정 프로토콜이나 IP 주소를 차단하여 네트워크 보안을 유지할 수 있다.&#x20;

Security Group vs ACL

* 공통점 : 네트워크 보안을 강화하는 기술&#x20;
* 차이점&#x20;
  * scope : 보안그룹은 EC2 인스턴스 단위로 적용되자, ACL 은 서브넷 혹은 VPC 단위로 적용된다. 즉, 보안그룹은 인스턴스 간의 트래픽을 제어하지만, ACL은 서브넷이나 VPC 간의 인바운드, 아웃바운드 트래픽을 제어한다.&#x20;
  * state : 보안그룹은 상태를 유지하고, 허용된 패킷에 대한 응답을 자동으로 허용한다. 즉, 요청 패킷에 대한 응답 패킷이 허용된 경우에는 응답 패킷도 자동으로 허용된다. 하지만 ACL 은 stateless 하며, 요청 패킷과 응답 패킷을 모두 명시적으로 허용해야한다.&#x20;
  * 우선순위&#x20;
    * ACL 은 규칙 순서에 따라서 우선순위를 갖지만, 보안그룹은 우선순위가 없다.&#x20;
  * 제어방식
    * 보안그룹은 허용 규칙만 설정할 수 있다. 하지만 ACL 은 차단규칙, 허용규칙을 모두 설정할 수 있다.&#x20;
  * 기능&#x20;
    * 보안그룹은 대부분 OSI 4계층에서 동작하며, IP, port, protocol 등을 기반으로 패킷을 필터링한다.&#x20;
    * 하지만 ACL은 OSI 3계층에서 동작하며, IP주소, 프로토콜 등을 기반으로 패킷을 필터링 한다.&#x20;
* 요즘에는 주로 ACL보다는 보안그룹만으로 네트워크 보안 설정을 하는 편이다. 규칙이 많아질수록 복잡해지며, 유지보수 하기가 어려워진다.&#x20;

### 3-4. Subnet

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>NextSteps &#x3C;인프라 공방> 8기</p></figcaption></figure>

* VPC 내에서 방을 좀 더 세부 그룹으로 나누어놓은 것
* VPC의 CIDR 내에서 구성 가능하다.&#x20;
* [\[홀인원 2.08.16\] IPv4 - 서브넷 마스크](https://www.youtube.com/watch?v=o-NRjtQsJx4)
* 서브네팅 사이트 : [참고 1](https://www.ipaddressguide.com/cidr), [참고 2](https://cidr.xyz)

### 3-5. Routing Table

* 서브넷이 다른 서브넷이나 외부망과 통신하기 위한 정보를 가지고 있다.&#x20;

### 3-6. Internet Gateway

* 외부망과의 연결을 담당
* [AWS docs 참고](https://docs.aws.amazon.com/ko\_kr/vpc/latest/userguide/VPC\_Internet\_Gateway.html)&#x20;
* 서브넷에 속한 EC2가 외부와 통신을 하기 위해서는 자신의 라우팅 테이블에 설정된 정보를 제외한 모든 대역에 대해서 IGW로 통신 요청하도록 설정해두어야 한다.&#x20;
  * 라우팅 테이블을 생성하고, 0.0.0.0/0 대역을 Iternet Gateway 로 매핑한다.&#x20;

### 3-7. NAT Gateway

* private subnet 에 있는 인스턴스들이 인터넷과 통신할 수 있도록 해준다.&#x20;
* public subnet 에 위치하며, public ip 주소를 가지고 있다.&#x20;
* 인스턴스의 요청 -> 인터넷으로 전달 -> 인터넷에서 온 응답-> 인스턴스로 전달&#x20;
* 자동으로 확장되기 때문에 고가용성, 확장성이 보장된다.&#x20;
* 보안에도 유리하다. 인터넷으로 직접적으로 연결되어있지 않기 때문에 인스턴스들은 인터넷에서 직접적으로 접근 받을 수 없다.  &#x20;
* 인터넷 통신을 위해서 추가적으로 비용이 발생할 수는 있다.&#x20;
* 내부망은 별도의 라우팅 테이블을 생성해준다.&#x20;
* 내부망에서 라이브러리 설치 등 외부망에 접속해야할 때에는 Nat Gateway 를 활용한다.&#x20;



## 4. 접근제어&#x20;

* Public Cloud 의 Official OS Image, Spring Framework 등을 사용하고 있다면, 이미 많은 부분의 보안성이 상당히 보장된 상태이다.&#x20;
  * SYN Flooding
    * 커널에 SYN Cookie 가 활성화되어있어서 TCP SYN Flooding 공격이 유효하지 않는다.&#x20;
      * [참고](https://brunch.co.kr/@alden/5)
    * AWS 공식적인 OS 이미지를 사용하고 있다면, 이미 보안패치가 되어있어서 신경쓰지 않아도 된다.&#x20;
  * LAND, Smulf Attack : 라우터에 설정하여 막을 수 있다. 역시 AWS 를 사용한다면 신경쓰기 않아도 된다.&#x20;
  * SQL Injection&#x20;
    * PreparedStatement, or JPA 를 사용할 경우, [pre-compile](https://stackoverflow.com/questions/23845383/what-does-it-mean-when-i-say-prepared-statement-is-pre-compiled) 과정을 거친다.&#x20;
    * 이때 파라미터로 들어가는 값을 바인딩하여 사용한다.&#x20;
    * SQL 문법이 아닌 컴파일 언어로 처리하기 때문에 문법적 의미를 가지지 않으므로, 바인딩 변수에 query 를 넣더라도 의미있는 쿼리로 동작하지 않는다.&#x20;
      * [How to prevent SQL Injection vulnerabilities: How Prepared Statements Work](https://jaredablon-31568.medium.com/how-to-prevent-sql-injection-vulnerabilities-how-prepared-statements-work-f492c369614f)
* 따라서 서버 보안패치 등은 AWS 에 위임하고, 네트워크 보안과 계정 보안(IAM)에만 집중하면 된다.&#x20;
* AWS Security Group 을 통해서 특정 IP, Port 에 대해서 접근 제어를 할 수 있다.&#x20;
  * [나의 공인 IP 확인하기](https://search.naver.com/search.naver?where=nexearch\&sm=top\_hty\&fbm=0\&ie=utf8\&query=%EA%B3%B5%EC%9D%B8+ip)&#x20;



## 5. 참고

* [IGW vs. NAT Gateway](https://medium.com/awesome-cloud/aws-difference-between-vpc-peering-and-transit-gateway-comparison-aws-vpc-peering-vs-aws-transit-gateway-3640a464be2d)&#x20;
* [AWS 를 사용할 때, 할당받는 IP 대역 확인하기](https://ip-ranges.amazonaws.com/ip-ranges.json)&#x20;

### CIDR

* Class vs. CIDR&#x20;
* 기존에는 주소 Class 를 이용하여 IP 주소를 할당했다.&#x20;
* 이 체제에서는 각 클래스마다 사용할 수 있는 IP 주소의 개수가 고정되어있어서 발생하는 많은 문제점들이 있었다.&#x20;
  * 대규모 네트워크에서 IP 주소 낭비됨&#x20;
  * 주소 클래스마다 IP 주소 범위가 다른 크기를 가지고 있어서 관리도 복잡합&#x20;
* 이를 해결하기 위해 등장한 개념이 바로 CIDR 방식이다.
  * Classless Inter-Domain Routing : 인터넷 주소 체계에서 IP 주소를 좀 더 효율적으로 할당하기 위한 방법이다.&#x20;
  * IP 주소의 프리픽스 길이를 사용하여 주소를 할당한다. 이 프리픽스는 네트워크 부분의 비트수를 의미.&#x20;
  * 결국, CIDR = IP 주소 + 프리픽스의 길이&#x20;
  * 예를 들자면, 192.168.0.0/24라는 CIDR 주소는 192.168.0.0부터 192.168.0.255까지의 IP 주소 범위를 의미
  * 장점
    * 주소 클래스와 상관 없이 IP 주소를 할당하는 것이 가능하다. 더 효율적으로 사용 가능하다.&#x20;
    * IP 주소 관리도 더욱 간편해진다.&#x20;
