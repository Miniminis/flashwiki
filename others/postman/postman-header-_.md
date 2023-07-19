# postman 의 header에서 언더바(\_) 변수 인식 안되는 현상

한동안 개발 안하다가 요즘 프로젝트 때문에 API 만들고 있는데 이게 웬걸? header 에 원래 auth\_token 을 변수명으로 사용해서 토큰 인증 방식을 자주 써왔었는데 header 변수명에 언더바(\_) 가 있으니까 원격 서버에서 인식이 안되는 현상을 발견했다.&#x20;

서버 환경은 다음과 같다.&#x20;

* Naver Financial Cloud &#x20;
* Ubuntu 18&#x20;

덕분에 로컬 환경에서는 잘 동작하던 로그인이 원격 서버에서는 header 에서 변수를 읽어들이지 못해 토큰 에러가 계속 발생했다.&#x20;

원인은 좀 더 알아봐야겠지만, 일단 급한대로 auth-token 으로 변수명 수정하여 진행하고 있다. (이런 경우가 있다니...!)&#x20;



### auth\_token 일 때,

* 로그 header 부분에 auth\_token 변수명 찾아볼 수 없음&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

### auth-token 일 때,&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

