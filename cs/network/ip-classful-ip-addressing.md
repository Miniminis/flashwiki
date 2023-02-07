# IP 주소 - Classful IP Addressing

## 1. 배경&#x20;

* IPv4는 32비트로 이루어진 인터넷 주소이다. 크게 네트워크 주소와 호스트 주소로 나뉘게 된다.&#x20;
* 네트워크 주소는 호스트들을 모아놓은 네트워크를 말한다. &#x20;
  * 즉, 네트워크 주소가 동일하다면 로컬 네트워크에 속하는 것이다. &#x20;
* 호스트 주소는 호스트를 구분하기 위한 주소이다.&#x20;
  * 네트워크 호스트는 컴퓨터 네트워크에 연결된 컴퓨터나 기타 장치를 의미한다.&#x20;
  * 즉, 호스트 주소는 네트워크에 속하는 컴퓨터나 장치를 구분하기 위한 주소

## 2. classful IP addressing&#x20;

* 네트워크 주소를 매기고 그에 따라 네트워크 크기를 다르게 구분하여 클래스를 할당하는 주소체계를 말한다.&#x20;
* 구분하는 첫기준자(1옥텟, 2옥텟, 3옥텟 등)을 서브넷마스크라고한다.&#x20;

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption><p><a href="https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/">https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/</a></p></figcaption></figure>

* 우선, 클래스 D와 E의 경우, 각각 멀티캐스트 및 예비 사용을 위해 따로 구분된 것이므로, 우리가 일반적으로 네트워크를 구성하는데 고려해야할 것은 Class A-C 이다.&#x20;
* 각각의 클래스는 첫번째 옥텟의 값에 따라서 달라지게 되는데, 아래와 같이 나누어진다.&#x20;
  * 0으로 시작하면 ClassA
  * 10으로 시작하면 ClassB
  * 110으로 시작하면 ClassC&#x20;

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption><p><a href="https://www.firewall.cx/networking-topics/protocols/ip-subnetting/168-subnetting-mask.html">https://www.firewall.cx/networking-topics/protocols/ip-subnetting/168-subnetting-mask.html</a></p></figcaption></figure>

### 2-1. ClassA

<figure><img src="../../.gitbook/assets/image (50) (4).png" alt=""><figcaption><p><a href="https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/">https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/</a></p></figcaption></figure>

* 첫번째 옥텟을 제외하고 한 네트워크당 2^24-2 = 16,777,214개(약 16만개)의 호스트 ID 를 가질 수 있다.&#x20;
* 네트워크 주소의 범위는 1 - 126으로 시작
  * 0.0.0.0 은 알수없는 주소에 달아놓는 특수주소이기 때문에 포함하지 않는다.&#x20;
  * 원래는 127.255.255.255 도 포함해야하지만, 127.x.x.x 는 루프백 주소이기 때문에 포함하지 않는다.

### 2-2. ClassB&#x20;

<figure><img src="../../.gitbook/assets/image (4) (7).png" alt=""><figcaption><p><a href="https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/">https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/</a></p></figcaption></figure>

* 앞의 두 옥텟을 제외하고 한 네트워크 당 2^16-2 = 65,534 개(약 6만개)의 호스트 ID 를 가질 수 있다.&#x20;
* 네트워크 주소의 범위는 128-191 로 시작한다. &#x20;

### 2-3. ClassC&#x20;

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption><p><a href="https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/">https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/</a></p></figcaption></figure>

* 앞의 세개의 옥텟을 제외하고 총 2^8-2 = 254개의 호스트 ID 를 가질 수 있다.&#x20;
* 네트워크 주소는 192-223 으로 시작한다. &#x20;

## 3. 정리&#x20;

<figure><img src="../../.gitbook/assets/image (12) (7).png" alt=""><figcaption><p><a href="https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/">https://www.geeksforgeeks.org/introduction-of-classful-ip-addressing/</a></p></figcaption></figure>

## 4. 기타&#x20;

### 4-1. 각 클래스별로 -2 개를 하는 이유는?&#x20;

* 맨 앞자리는 네트워크 주소로 남겨두고&#x20;
* 맨 뒷자리는 브로드캐스팅 주소로 남겨놓는다.&#x20;

### 4-2. 클래스풀 ip addressing 의 문제점

* 네트워크의 크기가 작은 경우, 큰 네트워크를 필요로 하는 조직은 여러개를 확보해야하는 어려움이 있다. &#x20;
* 작은 네트워크가 필요한 조직은 너무 많은 ip 주소를 가져가게 되어 낭비될 수 있다.&#x20;
  * 위의 방식대로하면 결국 최소 ip 주소 풀이 256개이므로, 그보다 적게 필요한 조직에게는 낭비이다.&#x20;



결국은 클래스 3개로 나뉘어지는데 그 사이에 세부 구분이 없어서 범위가 너무 넓다는 단점이 있는 셈이다. &#x20;
