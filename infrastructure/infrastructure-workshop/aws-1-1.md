---
description: 1. 망 구성하기
---

# aws로 그럴듯한 인프라 만들기 1 - 네트워크 망 구성

> [nextstep 의 인프라 공방 과정](https://edu.nextstep.camp/c/VI4PhjPA)에 참여하고 있다. 총 4주 과정으로 매 주마다 진행해야할 미션이 주어지고, 미션을 단계별로 해결하면서 인프라에 대한 지식을 자연스럽게 습득하고 경험해볼 수 있다. 매 주 경험하게될 주제는 대략 아래와 같다.&#x20;
>
> 1. 그럴듯한 인프라 만들기&#x20;
> 2. 안정적인 인프라 만들기
> 3. 조회 성능 개선하기&#x20;
> 4. 확장하는 인프라 만들기
>
> 오늘은 그 첫번째 주차인 "그럴듯한 인프라 만들기" 라는 주제로 과제를 진행했던 과정과 그 속에서 겪었던 어려움들을 기록하며 배운 점들을 조금 더 오래 기억하고자 한다.&#x20;



> 1주차는 그럴듯한 인프라를 구축해보는 미션이 주어졌다. 아래와 같이 진행될 예정이다. 이번 글에서는 1번과 2번을 다룬다&#x20;
>
> 1. [PEM 키 생성하기 ](aws-1-1.md#1.-pem)
> 2. [망 구성하기 ](aws-1-1.md)
> 3. [배포하기](aws-2-1.md)&#x20;
> 4. [배포 스크립트 작성하기](aws-3-1.md)&#x20;



## 1. PEM 키 생성

* 미션을 시작하기 전에, 나의 PEM 키를 생성하여 이후 구축할 모든 서버에서 해당 키를 사용한다.&#x20;



## 2. draw.io 로 네트워크 망 설계

첫 회사 재직시절부터 네트워크 망 설계를 할 때, 좀 더 빠르고 직관적인 툴이 없을까 이런 저런 소프트웨어들을 기웃거렸다. 그렇게 여러 곳을 방황하다가 결국 정착한 것은 google 의 [draw.io(Diagrams.net)](https://www.draw.io) 서비스이다.&#x20;

[Draw.io](https://www.draw.io) 는 단순히 네트워크 망 설계 뿐만 아니라 마인드맵, 조직도, ERD 등 굉장히 다양한 용도로 사용될 수 있는 툴이다. 개인적으로는 인터넷 브라우저 상에서 작업이 가능하고, 여러 명이 동시에 작업할 수 있다는 점, 반응 속도도 (데이터가 일정 수준 이하이면) 꽤 나쁘지 않다는 점, 쉽게 요소를 추가하고 삭제하는 것이 가능하다는 점, 그리고 AWS 관련 서비스 아이콘들을 제공해준다는 점 등 다양한 이점 때문에 이 툴을 초기 인프라 설계할 때나 데이터 베이스 모델링 할 때도 사용하고 있다. (강추...👍)



### 2-1. 구성 정리

우리가 구성할 인프라는 대략 아래와 같다. 각 요소별로 만족해야하는 조건이 주어졌기 때문에 그에 맞춰 순서대로 구축을 진행해볼 것이다.&#x20;

1. VPC : CIDR은 C class(x.x.x.x/24)로 생성
2. Subnet&#x20;
   1. 외부망으로 사용할 Subnet : 64개씩 2개 (AZ를 다르게 구성)
   2. 내부망으로 사용할 Subnet : 32개씩 1개
   3. 관리용으로 사용할 Subnet : 32개씩 1개
3. Internet Gateway 연결
4. Route Table 생성
5. Security Group 설정
   1. 외부망
      1. 전체 대역 : 8080 포트 오픈
      2. 관리망 : 22번 포트 오픈
   2. 내부망
      1. 외부망 : 3306 포트 오픈
      2. 관리망 : 22번 포트 오픈
   3. 관리망
      1. 자신의 공인 IP : 22번 포트 오픈
6. 서버 생성
   1. 외부망에 웹 서비스용도의 EC2 생성
   2. 내부망에 데이터베이스용도의 EC2 생성
   3. 관리망에 베스쳔 서버용도의 EC2 생성
   4. 베스쳔 서버에 Session Timeout 600s 설정
   5. 베스쳔 서버에 Command 감사로그 설정

### 2-2. 구성에 맞춰 설계도 작성&#x20;

<figure><img src="../../.gitbook/assets/image (69) (2).png" alt=""><figcaption></figcaption></figure>

위의 그림은 툴을 이용하여 AWS 네트워크 망을 어떻게 구성할 것인지 간단히 설계도를 작성한 것이다. 오른쪽에는 전체적으로 기본적으로 인터넷망부터 VPC, Availability Zone, Subnet, Server, IGW, NAT gateway 까지 필요한 요소들을 적절하게 배치하여 전체적으로 우리 서비스가 어떤 흐름을 통해 사용자들과 소통할 수 있는지를 그려본다.&#x20;

왼쪽의 표는 각 서비스별로 기억해야할 name 별칭과 ip 들이다. AWS 는 워낙 다양한 서비스들을 제공하고 있어서 저렇게 name 으로 별칭을 정해두지 않으면, 서비스가 성장하고 시스템이 커질수록 각 요소들을 적절하게 관리할 수 없게 된다.&#x20;

### 2-3. 네이밍 규칙&#x20;

의외로 네이밍을 대충하는 개발자들을 종종 봤는데, 저 네이밍이 잘 되어있으면 시스템을 파악해야하는 뉴비 입장에서는 정말 감탄스럽다. 맨 처음 일했던 회사에서 이사님과 함께 네트워크 구축을 하며 배웠던 것인데, \[기업의 서비스명]-\[AWS 요소]-\[public/private여부]-\[az영역] 으로 네이밍을 하면, 처음보는 사람도 해당 구성요소에 대해서 많은 정보를 얻을 수 있기 때문에 참 좋은 방법이라고 생각했다. 이후로도 계속 그 방법을 사용하고 있다. (다만, 이번 과제에서는 다른 사람들와 AWS 계정을 공유해서 사용하고 있기 때문에 내 리소스임을 드러내야하므로, 나의 gitub 아이디나 나를 표현할 수 있는 단어를 prefix 로 두어야했다.)

### 2-4. 서브네팅&#x20;

서브네팅도 왼쪽의 표를 작성할 때, 아예 함께 진행한다. 그래야 논리적으로 한번에 계획하고, 나중에 AWS 콘솔 상에서 작업할 때에는 마음 편하게 저 표만 보면서 구성할 수 있다. 그때그때 생각하려면 골치아프고 실수할 가능성도 높아진다. &#x20;



## 3. VPC 생성

### 3-1. VPC 는 결국 땅따먹기&#x20;

VPC는 Virtual Private Cloud 의 약자이다. VPC를 구성한다는 것은 결국 지구상 존재하는 드넓은 네트워크 망 중에서, "나는 이 땅에다가 내 집을 만들거야", "여기까지는 내 공간이야, 침범하지마!"하고 공헌하는 것이라는 생각이 들었다. 한번 땅을 사고 나면, 그 내부에서 집을 지을지, 빌딩을 세울지, 방을 어떻게 나눠서 쓸지, 외부와 소통할 수 있는 창문을 낼지, 손님이 들어올 수 있는 문을 만들지, 아니면 우리 가족들끼리만 소통하게 할지 그 세부적인 것은 오로지 그 땅 주인 마음이다.&#x20;

이렇게 명확하게 영역을 구분하여 선을 긋는 땅따먹기(?)를 하면 니땅 내땅을 주장하며 싸울 논란의 여지도 없고, 좁은 땅을 좀 더 효율적으로 구분하여 사용할 수 있게 된다. 애매한 공간은 필요한 다른 사람에게 주고, 필요없으면 다시 땅을 팔아도 된다.&#x20;

### 3-2. 구성하기&#x20;

사실상 위처럼 설계 계획을 세웠다면, 이제 나머지는 표에 맞게 구축하는 것만 남았다. AWS 콘솔 > VPC 메뉴로 가서 VPC 를 생성해보자.&#x20;

<figure><img src="../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

* 생성할 리소스 : VPC만&#x20;
  * "VPC 등"을 선택하면 서브넷 등 필요한 요소를 한꺼번에 설정하고 생성할 수 있도록 도와주는데, 문제는 내가 잘 모르는 부분까지 AWS 가 알아서 구축해준다는 점이다. 결국 구성하는 내가 잘 모른다면, 나중에 디버깅해야할 때, 어떤 부분에서 문제가 생겼는지 파악하기가 어렵다. 때문에 나는 수동으로 하나하나 설정을 했다.&#x20;
* 위의 설계도에 따라 이름태그, IPv4 CIDR, 태그(Name) 등을 기입한다.&#x20;

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>



## 4. Subnet 생성

### 4-1. Subnet은 방만들기&#x20;

VPC가 내 집을 만들기 위한 토지를 확보하는 과정이라면, Subnet은 집을 짓고 방을 만드는 과정이다. 집에 아무리 넓어도 공간을 효율적으로 분리해야 프라이버시도 보존하고, 많은 사람들이 더 효율적으로 살 수 있다.&#x20;

### 4-2. 구성하기&#x20;

<figure><img src="../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

* 역시 위의 설계도에 따라 Subnet 을 생성한다.&#x20;
* 서브넷을 생성하면서 가용영역(availability zone) 역시 같이 설정하는데, 보통 시스템의 안정을 위해 다양한 가용영역에 서브넷을 분산하여 만들어둔다. 보통은 A-C, B-D를 페어링하여 구성하는 것 같다.&#x20;
* 우리는 A와 C 영역에 public subnet 1개씩을 만들고, A영역에 Bastion 서버가 위치할 public 서브넷, 데이터베이스가 위치할 private 서브넷을 하나씩 만들었다. 역시 위의 설계도에 따른 것이다. &#x20;

<figure><img src="../../.gitbook/assets/image (72) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (58) (1).png" alt=""><figcaption></figcaption></figure>

## 5. Internet Gateway 와 Nat Gateway 생성 및 연결

### 5-1. IGW 는 밖으로 향하는 문&#x20;

VPC에서 토지를 확보하고, Subnet 으로 집 내부의 방을 나눴다면, IGW 는 집에서 바깥세상으로 향하는 문을 만드는 것과 같다는 생각을 했다. 외부로부터의 모든 요청들, 외부로 향하는 모든 요청들은 바로 이 IGW 를 통과한다.&#x20;

### 5-2. Nat Gateway

Nat gateway 는 igw 와 연결되어 내부에서 외부의 인터넷망을 쓸 수 있도록 해주는 것이다. public subnet 에 위치하며, private subnet 이 이와 연결되면, nat gateway -> public subnet -> igw -> internet 까지 접속이 가능해진다. &#x20;

### 5-3. 구성하기&#x20;

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (57) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (62) (1).png" alt=""><figcaption></figcaption></figure>



## 6. Route Table 생성 및 연결&#x20;

### 6-1. RT 는 경비실&#x20;

라우터 테이블은 택배기사분이 경비실에서 목적지를 찾는 과정 같다는 생각을 했다. 사용자로부터 전달된 소포(패킷)을 가지고 있는 택배기사님은 어디쪽으로 전달되어야할지 아파트의 정확한 동과 호수를 경비실에서 한번 확인한다. 경비실에서는 정확한 위치가 파악되면, 그 위치를 알려주되, 위치를 모르겠다면, 그냥 경비실에 맡겨주도록한다.&#x20;

아파트 주민이 택배를 보낼 때도 마찬가지이다. 아파트 주민은 서로 간의 거래라면 당근 직거래처럼 직접 만나서 소통하고 교환할 수 있겠지만, 외부의 사람들과 거래해야할 때면, 아파트 경비실에 맡겨둔다. 경비아저씨가 세상 바깥으로 그 물건을 전달해준다.&#x20;

### 6-2. 구성하기&#x20;

* public rt 에는 public subnet 2개와 igw 를,&#x20;
* bastion rt 에는 bastion 용 public subnet 1개와 igw 를&#x20;
* 그리고 내부망을 위한 internal rt 에는 priavet subnet 1개와 nat gateway 를 연결해준다.&#x20;

<figure><img src="../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (74) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>



## 7. Security Group 설정

### 7-1. SG는 경비대 &#x20;

보안그룹은 말 그대로 우리 인프라에 대한 보안정책을 설정하는 곳이다. 예전에는 네트워크 ACL처럼 서브넷 단에서도 보안정책을 생성했다고 하는데, 이 보안정책이라는게 복잡하게 구성하면 한도끝도 없어지는지라, 요즘에는 그냥 서버 단위로 정책을 설정하는 것이 일반적이라고 한다.&#x20;

### 7-2. 구성하기&#x20;

설계도에 맞게 구성한다.&#x20;

<figure><img src="../../.gitbook/assets/image (60) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (68) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (59) (1).png" alt=""><figcaption></figcaption></figure>

### 7-3. IP 주소 기반이 아닌 SG 기반으로 설정하기&#x20;

보안그룹을 설정할 때, 보통은 특정 프로토콜, 포트, IP 를 허용할 것인지 설정하도록 되어있는데, 이렇게 IP 주소를 기반으로 할 경우에는 서버의 대수가 늘어날 때마다 계속 추가 설정해주어야 하는 불편함이 있다. 이때 우리가 보안그룹을 기반으로 설정하면, 보다 간편하게 설정이 가능하다.&#x20;

<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

예를 들면 위와 같은 식이다.

* bastion server의 보안 정책인 mhson-sg-bastion 은 이미 서버와 연결되어있다.&#x20;
* 이때, public server 인 mhson-sg-public 을 구성할 때, 만약 bastion 서버의 보안정책인 mhson-sg-bastion 를 선택한다면, [_**이는 해당 보안 그룹과 연결된 리소스의 private ip 주소를 기반으로 하는 트래픽을 허용한다는 의미가 된다**_](https://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/security-group-rules.html)_**.**_&#x20;
  * [https://rainbound.tistory.com/entry/AWS-보안그룹-규칙-소스에-보안그룹-넣기](https://rainbound.tistory.com/entry/AWS-%EB%B3%B4%EC%95%88%EA%B7%B8%EB%A3%B9-%EA%B7%9C%EC%B9%99-%EC%86%8C%EC%8A%A4%EC%97%90-%EB%B3%B4%EC%95%88%EA%B7%B8%EB%A3%B9-%EB%84%A3%EA%B8%B0)
* 즉, mhson-sg-bastion 와 연결된 bastion 서버의 private ip 주소를 기반으로 하는 트래픽은 public server 에 허용된다는 것이다.&#x20;
* 따라서 bastion 서버의 private ip 를 통해서 public 서버에 접근할 수 있다는 말!&#x20;
* 그래서 나의 public server 보안그룹 mhson-sg-public 은 아래와 같은 보안 규칙을 가지고 있다.&#x20;
  * ICMP 와 SSH 는 bastion 서버에서만, user 들이 접속하는 포트는 80과 443만 전체 대역대로 허용
* 다만 한 가지 주의할 점은 private ip 를 기반으로 처리되기 때문에 AWS 리소스만 추가할 수 있고, 외부 리소스, public ip 는 사용 불가하다는 점이다. aws 서비스만 소스에 그룹을 넣어 반영이 가능하다. 외부 ip의 경우는 적용이 되지않는다.&#x20;



## 8. Server 생성&#x20;

### 8-1. 서버 구성하기&#x20;

서버는 아래와 같이 구성하도록 하자.&#x20;

* 이름 : 미리 설계도에서 네이밍 해둔대로 적는다.&#x20;
* 어플리케이션 및 OS 이미지 : 우분투 22.0 버전, 64비트 선택&#x20;
* 인스턴스 유형 : git 과 연동하여 어플리케이션 운영해야하므로 t3.medium 으로 생성&#x20;
* 키페어 : 미리 생성해준 PEM 키 선택&#x20;
* 네트워크 설정
  * 적절한 VPC, 서브넷 선택
  * 퍼블릭 IP 자동할당 비활성화&#x20;
  * 방화벽 (보안그룹) : 기본 보안그룹 중 서버에 맞게 선택&#x20;
* 스토리지 구성 : 30GB까지는 프리티어 요금제로 사용 가능하다.&#x20;
* 지금은 고급 세부정보를 건드리지 않지만 종료나 삭제 방지 등 필요한 부분을 나중에 수정할 수 있다.  &#x20;

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

### 8-2. bastion 서버는 왜 만들까 &#x20;

베스천(bastion) 이라는 단어는 원래는 보루, 요새라는 뜻으로, 중세 시대에 영주나 왕이 살고 있는 중요한 기지인 성을 둘러싸고 있는 방어막을 의미한다고 한다. ([참고](https://www.lesstif.com/ws/%EB%B2%A0%EC%8A%A4%EC%B2%9C-%ED%98%B8%EC%8A%A4%ED%8A%B8-43843897.html))

네트워크 망에서 구성하는 베스천 서버도 그와 비슷한 역할을 한다. 보통 퍼블릭 서브넷 상에 퍼블릭한 서버로 베스천 서버를 구성하고, 베스천 서버를 통해서 다른 서버로 접근할 수 있도록 구성을 한다. 외부에서 서버로 직접 접근하는 것이 아니라 꼭 배스천 서버를 통해서만 접근 가능하도록 하는 것이다.&#x20;

이렇게 구성하면 대략 아래와 같은 이점이 있다.&#x20;

1. 보안 설정을 베스천 서버에만 집중하고, 나머지 서버는 베스천 서버를 통해 접근할 수 있도록 하여, 랜섬웨어, 악성 루트킷 등의 피해를 줄인다.&#x20;
   1. 배스천 서버가 공격당할 경우, 이 서버만 재구성하면 되니까, 전체 인프라에 미치는 영향력이 적어지게 된다.&#x20;
   2. 보안상 매우 중요한 22번 포트를 전체 서버가 아니라, 배스천 서버로만 오픈하여 보안 정책을 더 엄격하게 유지할 수 있다.&#x20;
   3. 동시에 서버가 auto scaling 등으로 대수가 늘어나고 확장되더라도 보안 정책에 엄격하게 유지될 수 있다.&#x20;
2. 서비스 정상 트래픽과 관리자용 트래픽을 구분할 수 있다.&#x20;
   1. 서비스가 DDos 공격을 받아 대역폭을 모두 차지하고 있다고 하더라도 관리자는 22번 포트를 통해 배스천 -> 서버 루트로 접근할 수 있게 된다.&#x20;



{% hint style="info" %}
여기까지 구성했다면 필요한 부분은 모두 구성한 것이다. 네트워크가 잘 구성되었는지 통신을 확인해보자.&#x20;
{% endhint %}



## 9. 통신 확인하기&#x20;

통신 확인은 다음과 같은 과정으로 진행한다.&#x20;

1. 먼저 bastion 서버(mhson-ec2-bastion-2a)에 ssh + pem key 로 원격 접속하기&#x20;
2. mhson-ec2-bastion-2a 서버에서 public 서버인 mhson-ec2-public-2a 로 접속하기&#x20;
3. mhson-ec2-bastion-2a 서버에서 private 서버인 mhson-ec2-internal-2a 로 접속하기 &#x20;

### 9-1. bastion 서버 접속하기&#x20;

```
# 터미널 접속한 후 앞 단계에서 생성한 key가 위치한 곳으로 이동한다.
$ chmod 400 [pem파일명]    # user 만 읽을 수 있도록 권한을 변경한다.  
$ ssh -i [pem파일명] ubuntu@[SERVER_IP]
```

```sh
ubuntu@ip-192-168-11-182:~/.ssh$ chmod 400 key-mhson.pem
ubuntu@ip-192-168-11-182:~/.ssh$ ls -al
total 20
drwx------ 2 ubuntu ubuntu 4096 Mar  3 03:34 .
drwxr-x--- 4 ubuntu ubuntu 4096 Mar  3 03:34 ..
-rw------- 1 ubuntu ubuntu  391 Mar  3 03:11 authorized_keys
-r-------- 1 ubuntu ubuntu 1676 Mar  3 03:26 key-mhson.pem    # user 에게만 reading 권한 추가 
-rw-r--r-- 1 ubuntu ubuntu  142 Mar  3 03:34 known_hosts

```

### 9-2. bastion 서버에서 다른 서버로 접속하기 위한 설정&#x20;

```sh
// Bastion 서버에서 공개키를 일단 생성한다. 
bastion $ ssh-keygen -t rsa bastion $ cat ~/.ssh/id_rsa.pub
```

```sh
// 접속하려는 서비스용 서버에 해당 키를 추가한다. 
// 이때 접속을 위해서 22번 포트를 잠시 열고 접근한 뒤, 다시 닫는다. 
public $ vi ~/.ssh/authorized_keys
private $ vi ~/.ssh/authorized_keys
```

```sh
// bastion server 에서 접속을 해본다. 
bastion $ ssh ubuntu@[서비스용 서버 IP] 
```

* bastion server -> public server, bastion server -> internal server 로 접근을 확인하면, 통신 연결 완료! &#x20;
* 보안정책대로 잘 처리되는지 확인한다.&#x20;

### 9-3. ping test 해보기&#x20;

만약 SSH 프로토콜로 접속시 오류가 있다면, ping test 를 통해 서버가 정상 운영되고 있는지 확인한다.&#x20;

```sh
ping [서버 IP]
```

디버깅은 보통 아래와 같이 진행한다.&#x20;

1. SSH 로 접속 시도&#x20;
2. 접속되지 않고 터미널이 그 상태로 응답없이 있다면, 취소하고 ping test&#x20;
3. ping test 결과, 정상적으로 패킷을 받는다면, 보안 정책상 막힌 것은 아닌지 살펴본다.&#x20;
4. pint test 결과, 정상적으로 패킷을 못받는다면, 서버가 running 상태인지 확인한다.  &#x20;



```sh
# 정상
PING google.com (172.217.174.110): 56 data bytes
64 bytes from 172.217.174.110: icmp_seq=0 ttl=110 time=71.854 ms
64 bytes from 172.217.174.110: icmp_seq=1 ttl=110 time=68.993 ms
64 bytes from 172.217.174.110: icmp_seq=2 ttl=110 time=76.598 ms
64 bytes from 172.217.174.110: icmp_seq=3 ttl=110 time=76.201 ms
^C
--- google.com ping statistics ---
5 packets transmitted, 4 packets received, 20.0% packet loss
round-trip min/avg/max/stddev = 68.993/73.411/76.598/3.158 ms


# 비정상
 ping 13.124.81.243
PING 13.124.81.243 (13.124.81.243): 56 data bytes
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
^C
--- 13.124.81.243 ping statistics ---
4 packets transmitted, 0 packets received, 100.0% packet loss
```



## 10. Trouble Shooting&#x20;

### 10-1. 보안그룹으로 보안정책 설정 후, Public IP 로 접근 오류&#x20;

예전에 설정할 때, 분명 보안정책을 보안 그룹으로 설정했던 기억이 있어서 그대로 진행했다. 그런데 접속하려니 계속 오류가 나는 것이 아닌가...? 알고보니 보안그룹 설정에 대해서 제대로 알지 못해서 발생한 이슈였다.&#x20;

[AWS 공식문서](https://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/security-group-rules.html)에 따르면 보안그룹으로 보안정책을 설정할 경우, 접근은 연결된 리소스의 "Private IP"를 기반으로 허용이 된다.&#x20;

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

내가 간과한 것은 private ip 였다.&#x20;

* bastion 서버 + bastion 보안그룹 설정을 완료했고&#x20;
* public 서버 설정을 완료한 뒤, public 보안그룹 설정에 bastion 보안그룹을 연결시켜주었다. 따라서 bastion 서버는 pulbic 서버에 접근이 가능하다.&#x20;
* 그런데 bastion -> public 으로 접근할 때, private ip 를 기반으로 접근을 해야하는데, 나는 자꾸 public ip 로 접속을 시도해서 접속이 안되는 현상이 있었다.&#x20;
* 접속 ip 를 public 서버의 private ip 로 바꾸어주니 정상 접속 할 수 있었다.



### 10-2. 너무 Open 된 PEM key 권한&#x20;

* PEM 키의 권한이 400보다 너무 오픈되어있다면, 아래와 같이 오류 메시지가 뜬다.&#x20;

```sh
ubuntu@ip-192-168-11-182:~/.ssh$ ssh -i key-mhson.pem ubuntu@192.168.11.32
The authenticity of host '192.168.11.32 (192.168.11.32)' can't be established.
ED25519 key fingerprint is SHA256:bf8Up22yCj3R3S7TRbgmG386jaujCC5gTOdAhcAz04w.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.11.32' (ED25519) to the list of known hosts.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0664 for 'key-mhson.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "key-mhson.pem": bad permissions
ubuntu@192.168.11.32: Permission denied (publickey).
```



## 11. 기타 하면 좋은 설정들&#x20;

### 11-1. 환경변수 적용 - 세션 타임아웃 설정

* Session Timeout 설정을 하여 일정 시간 작업을 하지 않을 경우 터미널 연결이 자동으로 해제되도록 한다.
  * 보안에도 좋고, 불필요한 커넥션을 끊을 수도 있다는 점도 좋다.&#x20;

```sh
$ sudo vi ~/.profile
  HISTTIMEFORMAT="%F %T -- "    ## history 명령 결과에 시간값 추가
  export HISTTIMEFORMAT
  export TMOUT=600              ## 세션 타임아웃 설정 
    
$ source ~/.profile
$ env
```

<figure><img src="../../.gitbook/assets/image (6) (2).png" alt=""><figcaption></figcaption></figure>

### 11-2. 쉘 프롬프트 변경

* 서버마다 쉘 프롬프트를 변경하여 관리자가 Bastion 등 서버를 보다 쉽게 구분할 수 있도록 한다. 실수를 사전에 방지할 수 있다. &#x20;
  * [쉘변수](https://webdir.tistory.com/105)
  * [PS1 generator](http://ezprompt.net/)

<figure><img src="../../.gitbook/assets/image (3) (1) (6).png" alt=""><figcaption></figcaption></figure>

### 11-3. [logger](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4\_logger) 를 사용하여 감사로그 남기기

* 서버에 직접 접속하여 작업할 경우, 작업 이력 히스토리를 기록해두어야 장애 발생시 원인을 분석할 수 있다. 감사로그를 기록하고 수집하여 장애 상황을 대비해보자.&#x20;

```sh
$ sudo vi ~/.bashrc
  tty=`tty | awk -F"/dev/" '{print $2}'`
  IP=`w | grep "$tty" | awk '{print $3}'`
  export PROMPT_COMMAND='logger -p local0.debug "[USER]$(whoami) [IP]$IP [PID]$$ [PWD]`pwd` [COMMAND] $(history 1 | sed "s/^[ ]*[0-9]\+[ ]*//" )"'

$ source  ~/.bashrc
```

```sh
$ sudo vi /etc/rsyslog.d/50-default.conf
  local0.*                        /var/log/command.log
  # 원격지에 로그를 남길 경우 
  local0.*                        @원격지서버IP
    
$ sudo service rsyslog restart
$ tail -f /var/log/command.log
```
