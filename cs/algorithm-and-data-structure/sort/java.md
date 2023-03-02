# java 에서의 정렬

## primitive type

* Arrays 클래스가 primitive 타입 데이터를 위한 정렬 메소드를 제공한다.&#x20;
* Arrays.sort(data)
  * data 배열이 꽉 차있는 경우&#x20;
* Arrays.sort(data, 0, size)
  * 정렬을 원하는 시작점과 원하는 개수를 매개변수로 추가해준다.&#x20;
* int 이외에 다른 primitive 타입의 데이터도 지원한다.&#x20;
  * double, char&#x20;

## string array

* Arrays.sort(data)
  * 사전식 순서로 데이터 정렬

## string arrayList

* collection 계층 하에 있는 ArrayList 와 같이 Collection 유형으로 저장한 상태라면 Collections.sort(data) 로 정렬한다.&#x20;

## 사용자 정의 객체&#x20;

* Fruit 라는 클래스를 정의했다고 가정해보자.&#x20;
  * name, quantity 등의 필드가 존재한다.&#x20;
* Arrays.sort(fruits) 의 결과는 매우 모호해진다.&#x20;
  * 어떤 기준으로 정렬을 할 것인지 기준이 없는 상태!&#x20;
  * 어떤 값이 크고 작은지 그 기준이 나와있지 않다면, 정렬의 결과가 모호해진다.&#x20;

### 방법 1. comparable interface 를 구현한다

* 아래는 이름값을 기준으로 정렬 규칙을 정의한 예이다.&#x20;
  * compareTo() return 값이&#x20;
    * 음수면 내가 더 작고&#x20;
    * 0이면 같고&#x20;
    * 양수면 내가 더 크다.

<figure><img src="../../../.gitbook/assets/image (36) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (9) (6).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>



### 방법 2. Comparator interface 를 사용한다

* 만약 과일을 정렬하는 방법을 어떨 때에는 이름순으로, 어떨 때에는 재고순으로 정렬하고 싶다면 comparator 를 사용한다.&#x20;
* comparator 를 정의할 때에는 익명 클래스로 만들어 바로 인스턴스 객체로 받을 수 있도록 한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (27) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

#### comparator 들은 어디에다가 정의할 것인가?&#x20;

* 보통은 fruit 클래스 내부에 아예 static 한 클래스 멤버로 가지도록 한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (21) (2).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

