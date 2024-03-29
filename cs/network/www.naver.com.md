# www.naver.com 을 주소창에 입력하고 화면에 나타나기까지의 과정

대기열, 캐싱, DNS, 라우팅, ARP, 초기연결을 거쳐 컨텐츠를 다운받고, 이후 브라우저 렌더링 과정을 거쳐 네이버라는 화면이 나타나게 된다. 이러한 과정은 모두 캡슐화, 비캡슐화를 통해 이루어진다.&#x20;



## 1. 대기열&#x20;

* 브라우저는 주소창 입력에 대한 요청을 대기열에 넣는다.&#x20;

## 2. 캐싱&#x20;

바로 서버로 요청이 가지 않고, 캐시를 방문하여 이미 캐시가 존재하는지 확인한다.&#x20;

* 캐싱 : 요청된 결과값을 저장하고 그 값을 다시 요청하면 다시 제공하는 기술&#x20;
* 공유 프록시 캐시
  * 요청한 서버에서 프록시 서버가 캐싱을 하는 것을 말한다.&#x20;
  * node.js로 서버를 구축한다면 보통 nginx 로 프록시 서버를 구축하여 서버를 캐싱 서버로 사용한다.&#x20;
* 브라우저 캐시&#x20;
  * 쿠키, 로컬스토리지 등을 포함하는 캐시. 브라우저 자체가 사용자가 HTTP 를 통해 다운로드하는 모든 문서를 보유하는 것을 말한다.&#x20;
  * 인터넷 사용기록을 삭제하는 것도 바로이 이러한 캐시를 삭제하는 것.&#x20;

## 3. DNS&#x20;

* 브라우저가 요청의 ip 주소를 확인하는 단계이다.&#x20;
* DNS 는 도메인 이름과 ip 주소를 매핑해주는 서버이다. 예를 들면, www.naver.com 에 DNS 쿼리가 오게되면, 오른쪽부터 역순으로 root DNS -> .com DNS -> .naver DNS -> .www DNS 과정을 거쳐서 완벽한 주소를 찾아 IP 주소를 매핑하게 된다.&#x20;
* DNS 서버로 요청을 전달하는 것은 바로 진행되지 않고, 컴퓨터 메모리에 있는 호스트 파일 등 캐시를 확인한 후에 캐시미스가 일어나면 DNS 서버로 요청을 하게 된다.&#x20;

## 4. ip routing /ARP

* ip 주소가 확인되면, 이제 ip routing 그리고 ARP 를 거쳐서 실제 서버를 찾게 된다.  &#x20;

## 5. 초기연결

* 이제 브라우저가 TCP 3way handshake 및 SSL 을 통해서 연결을 설정한다. 이후 요청을 보낸 후 서버로부터 응답을 받게 된다. 여기서는 네이버 서버가 되겠다. &#x20;

## 6. 컨텐츠 다운

* 브라우저는 서버로부터의 응답을 수신한다.&#x20;

## 7. 브라우저 렌더링&#x20;

* 받은 데이터를 바탕으로 브라우저 엔진이 브라우저 렌더링 과정을 거쳐 화면을 만든다.&#x20;

