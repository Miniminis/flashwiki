# IP 주소 - Classless,Subnet Mask, Subnetting

## 1. Classful ip addressing 한계&#x20;

* 클래스 3개의 범위가 너무 넓어서 대규모 조직이나 소규모 조직의 경우, 필요한 ip 개수를 확보하지 못하거나 낭비하는 일이 발생하게 된다.&#x20;
  * 안그래도 한정된 ip 개수를 생각한다면, 심각한 문제!&#x20;
* 이를 해결하기위해 등장한 것이 바로 Classless 방식이다.&#x20;

## 2. Classless ip addressing&#x20;

* 클래스리스 방식에서는 클래스가 아니라 서브넷마스크를 중심으로 어디까지가 네트워크 주소고 어디까지가 호스트주소인지를 나눈다.&#x20;
* 현재 우리가 사용하는 주소체계 역시 이 클래스리스 방식이다.&#x20;
* 서브네팅 : 네트워크를 쪼갠다.&#x20;
* 서브넷 : 쪼개진 네트워크&#x20;
* 서브넷마스크 : 서브 네트워크를 위한 비트마스크

### 2-1. 서브넷마스크&#x20;

* 네트워크주소 부분은 1, 호스트주소 부분은 0으로 설정하여 나눈다.&#x20;

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption><p><a href="https://networkencyclopedia.com/subnet-mask/">https://networkencyclopedia.com/subnet-mask/</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption><p><a href="https://www.benq.com/en-ap/business/support/faqs/application/publicdisplay-b2bfaq-k-00034.html">https://www.benq.com/en-ap/business/support/faqs/application/publicdisplay-b2bfaq-k-00034.html</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (2).png" alt=""><figcaption><p><a href="https://www.guru99.com/subnetting-subnet-mask.html">https://www.guru99.com/subnetting-subnet-mask.html</a></p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

* 서브네팅 참고 사이트
  * [https://www.ipaddressguide.com/cidr](https://www.ipaddressguide.com/cidr)
  * [https://cidr.xyz](https://cidr.xyz)

### 2-2. 연습

* 10개의 사무실이 있으며 사무실 당 12개의 장치를 연결해야한다면?&#x20;
  * 한 서브 네트워크 당 123.12.12.12/28 로 16개의 호스트를 확보해야 16-2 = 14개 장치까지 가능하다.&#x20;

