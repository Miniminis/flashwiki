# 웹 성능 개선하기

> [nextstep 의 인프라 공방 과정](https://edu.nextstep.camp/c/VI4PhjPA)에 참여하면서 배운 것들을 정리합니다.&#x20;

## 1. 웹 성능 진단, 그 이후

웹 성능을 진단하고 나면, 개선해야할 포인트들은 대략 아래와 같다.&#x20;

### 1-1. 정적 파일과 네트워크 차원에서 시도할 수 있는 것&#x20;

* 불필요한 다운로드를 제거
* 불필요한 작업을 지연로딩
* 다양한 압축 기술을 통해 각 리소스의 전송 인코딩을 최적화
* 스크립트 병합하여 요청수 최소화
* 스크립트 크기를 최소화하여 패킷 크기 자체를 줄임

### 1-2. 웹 서버 차원에서 시도할 수 있는 것&#x20;

* 웹 프로토콜 최적화
* 캐싱을 활용하여 요청 수 최소화
* 애플리케이션 로직 개선
* 데이터베이스 SQL 최적화로 디스크 I/O 개선



## 2. nginx web server 특징

* worker 프로세스 / 싱글 스레드를 채택하여 Context Switch overhead가 발생하지 않음&#x20;
* 비동기 처리로 인해 적은 메모리 사용량으로 동시성을 보장
* 로드밸런스와 같은 캐시 기능

<figure><img src="https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/e964428368184715b3f3c7d4192e7438" alt=""><figcaption><p><a href="https://edu.nextstep.camp/c/VI4PhjPA">nextstep 의 인프라 공방</a></p></figcaption></figure>

즉, nginx는 싱글 프로세스 스레드로 이벤트 구동에 의한 **넌블로킹(Non-Blocking)** 처리를 하므로 처리속도가 매우 빠릅니다. 그러나 넌블로킹 처리에 따라 프로그램의 제어가 이벤트 핸들러(Event Handler)로 넘어왔다고는 해도 실제 데이터를 읽고 쓰는건 OS(커널) 내에 있는 시스템 호출 프로그램과 하드웨어 사이에서 실행되므로, 해당 처리가 너무 길어지면(I/O 시간이 길어지면) 결국 시스템 호출 큐에 요청이 많이 쌓여 성능이 저하될 수 있습니다.

따라서 CPU 자원에 대한 사용량보다는 네트워크 자원에 대한 의존도가 높아, **클라이언트와의 커넥션을 어떻게 효율적으로 관리할 것인지**가 성능 튜닝의 포인트입니다.

\
\


## 3. HTTP1.1 성능 개선

nginx는 기본적으로 keepalive 설정이 되어 있는데요. **keep alive란, 한번 맺은 세션을 요청이 끝나더라도 유지**해주는 기능이에요.\
구체적으로 살펴보면,

1. keepalive timeout 시간이 지나면(tcp\_keepalive\_time 설정값) 서버에서 Keepalive **확인 패킷**을 보내요.
2. 이 패킷에 대한 **응답을 받으면** 타이머는 원래 값으로 돌아가 다시 카운트를 진행합니다.

3-1. 반면  **응답을 받지 못한 경우** tcp\_keepalive\_intvl에 정의된 시간만큼 경과한 후 요청을 다시 보내게 되는데요. 이 때, tcp\_keepalive\_probes에 **정의된 횟수만큼 보내요**.\
3-2. 이 후로도 응답이 없을 경우 클라이언트는 연결이 끊어졌다고 인지하고 **서버에 RST 패킷을 보낸 다음 자신의 소켓을 닫는 것으로 연결을 종료**합니다.

그리하여 Keepalive를 사용할 경우 연결이 끊어졌음에도 FIN 패킷을 받지 못해 정리되지 않고 남아있던 좀비 커넥션을 없애는 효과도 발생한다고 해요. Nginx에 최대 요청 수(keepalive\_requests)는 기본 100개로 설정되어 있고, timeout 시간은 75초로 설정되어 있어요.

추가적으로, **클라이언트 입장**에서는 커널 파라미터(tcp\_tw\_reuse)를 사용하여 TIME\_WAIT 소켓을 재사용할 수 있어요. 불필요한 TCP 3way handshake가 일어날 수 있으므로 요청할 때마다 소켓을 새로 연결하는 방식(Connectionless)가 아닌, 미리 소켓을 열어놓고 처리하는 방식(**Connection Pool**)으로 해결합니다.

```sh
$ netstat -napo
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name     Timer
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      710/systemd-resolve  off (0.00/0/0)
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      867/sshd             off (0.00/0/0)
tcp        0      0 192.168.0.207:22        121.165.253.73:63803    ESTABLISHED 24404/sshd: ubuntu   keepalive (6918.34/0/0)
tcp6       0      0 :::80                   :::*                    LISTEN      25049/docker-proxy   off (0.00/0/0)
```

```sh
$ curl -I edu.nextstep.camp
HTTP/1.1 301 Moved Permanently
Server: nginx/1.14.0 (Ubuntu)
Date: Thu, 13 Aug 2020 05:38:55 GMT
Content-Type: text/html
Content-Length: 194
Connection: keep-alive
Location: https://edu.nextstep.camp/
```

⁉️ 그렇다면, TCP에서 이야기하는 Keep-alive와 nginx에 설정한 HTTP Keep-alive는 어떤 차이가 있는걸까요

> TCP keepalive는 서버간에 ACK 패킷을 보내 세션 테이블이 지워지지 않고 계속 세션 정보를 유지하는데 반해, HTTP Keepalive는 일정시간이 지나면 nginx 등의 서버가 능동적으로 연결을 끊어요.\
> 이에 TCP Keepalive는 mq, kafka 등 TCP 기반의 서비스들을 대상으로 **지속적 연결을 유지해야 하는 경우** 사용되고, HTTP Keepalive는 Apache, Nginx 등 **웹 애플리케이션에서 설정된 기간까지 최대한 연결을 유지하기 위해** 사용됩니다.

\
\
\
\


## 4. HTTP2.0

### \* HTTP2.0에 대한 요구사항

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/80d9210dd96c44d7be0bd575c41e3759)

* 기존의 스펙 문서를 활용하여 HTTP 메서드, 상태 코드, URI, 헤더 필드를 비롯한 **HTTP 1.1의 기본 틀은 유지**해야 한다.
* **TCP를 사용하며**, 대부분의 경우 HTTP 1.1보다 대폭적으로 사용자단의 레이턴시를 개선해야 한다.
* 병렬화를 위하여 서버에 **다수의 커넥션을 요구하지 않고** 특히 혼잡 제어에 있어서 TCP 사용 효율을 높여야 한다.
* HTTP1.1의 문제점인 **HOL 블로킹을 해결해야 한다.**

\
\
\


### \* HTTP/2 특징

![스크린샷 2021-03-27 오후 9.14.03.png](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/785a0139c41f4674a4a3283e1a621fbc)

* HTTP 요청과 응답은 message 단위로 구성되어 있으며, 메시지는 state line, header, payload 등으로 구성되어 있어요.
* HTTP/2에서는 **message**를 header / data 등의 **frame**으로 나뉘며, 여러 message가 모여 **stream**이 됩니다.
* Stream의 유연한 구조 덕분에 응답 frame 들이 요청 순서에 상관없이 만들어진 순서대로 클라이언트에게 전달되어요. 즉, **하나의 TCP 연결을 통해** 다수의 클라이언트 요청과 서버 응답이 **비동기 방식**으로 이루어지는 멀티플렉싱이 사용됩니다.
* 그 외에도 헤더 압축, 서버 푸시 등의 특징이 있어요.

> 자세한 설명은, [HTTP/2 소개](https://developers.google.com/web/fundamentals/performance/http2?hl=ko) 혹은 [HTTP1.1, HTTP2.0, 그리고 QUIC](https://www.youtube.com/watch?v=xcrjamphIp4)를 참고하세요\~

![스크린샷 2020-09-04 오전 3.18.12.png](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/cbfa4bd7823d4fc8877bfe82032a5e77)

![스크린샷 2020-09-04 오전 3.14.46.png](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/68ae4a9b0bbb4f429866685bf643b737)

\
\
\
\


## 5. Contents-encoding

* [텍스트 기반 자산의 인코딩 및 전송 크기 최적화](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer)합니다.
* 텍스트 파일(js, css, html)의 압축률은 70 \~ 80%

\


### \* GZIP을 사용한 텍스트 압축

* GZIP은 텍스트 기반 자산인 CSS, 자바스크립트, HTML에서 최상의 성능을 보입니다.
* 모든 최신 브라우저는 GZIP 압축을 지원하고 이를 자동으로 요청할 수 있어요.
* 서버는 GZIP 압축을 활성화하도록 구성해야 합니다.
* 일부 CDN의 경우 특별히 주의하여 GZIP이 활성화되었는지 확인해보세요.

\
\
\


## 6. HTTP Cache

* [HTTP 캐싱](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching): 캐싱을 활용한 성능 개선 문서로, 이 내용을 바탕으로 설명합니다.

### A. ETag로 캐시된 응답에 대한 유효성 검사 수행

* 서버는 ETag HTTP 헤더를 사용하여 유효성 검사 토큰을 전달합니다.
* 유효성 검사 토큰을 사용하면 효율적인 리소스 업데이트 검사가 가능합니다. 즉, 리소스가 변경되지 않은 경우 데이터가 전송되지 않아요.

\
\


**최초 요청**

![](https://nextstep-storage.s3.ap-northeast-2.amazonaws.com/4b0db36dbf0b498dac9866457d053030)

**두번째 요청**

![](https://nextstep-storage.s3.ap-northeast-2.amazonaws.com/31006899bb5b498bac87b9c69d183f99)\
\


### B. Cache-Control

* 각 리소스는 Cache-Control HTTP 헤더를 통해 캐싱 정책을 정의할 수 있어요.
* Cache-Control 지시문은 응답을 캐시할 수 있는 사용자, 해당 조건 및 기간을 제어합니다.

![](https://nextstep-storage.s3.ap-northeast-2.amazonaws.com/a6df73145df846c187ec415f9d8c8780)\
\


**'no-cache' 및 'no-store'**

```plaintext
Cache-Control: no-cache, no-store
```

* 'no-cache'는 매 요청마다 중간에 있는 캐시 서버들은 ETag를 통해 자원의 유효성 확인하는데요. Cache-Control의 max-age=0과 같아요.
* 'no-store'는 자원을 캐시하지 않아요.
* no-cache는 최신 상태로 유지(로컬 저장소에 저장하는 것을 막지는 않아, 변경이 있을 경우만 바로 캐시에 업데이트하는 방식), no-store는 저장소에 저장되는 것을 막아 데이터가 유출되는 것을 막기 위함이에요.

\
\


**'public' vs. 'private'**

```plaintext
Cache-Control: private, max-age=600
```

* 'public'은 중간 단계를 포함해 모든 캐시 서버에 캐시가 가능합니다.
* 'private'은 요청한 사용자만 캐시할 수 있어요. 즉, CDN 같은 범용 캐시서버에서도 캐시할 수 있긴 하지만, 그 응답을 모든 사용자에게 공유할 수는 없어서 결국 최종 사용자의 브라우저에서만 응답을 캐시할 수 있어요.
* Cache-control: private 이 개인정보를 보호한다는 의미는 아니에요.

\
\


### C. 최적의 Cache-Control 정책 정의

![](https://techcourse-storage.s3.ap-northeast-2.amazonaws.com/03d7194f009e43999c108da9ff94e718)

* 최대한 많이 / 오래 / 가까이 캐시해야 합니다.
* 개인화된 컨텐츠, API 호출 등은 캐시하기 어려워요.
* 위의 의사결정 트리를 기준으로 정책을 설정하며, 캐시 주기는 특별한 이유가 없다면 1년정도로 설정합니다.

\
\
\
\


### D. 정적 자원을 캐싱할 때의 문제점과 해결책

#### \* 캐싱을 사용할 때의 문제 상황

* js, css, image와 같은 정적 파일은 배포 후에 변경이 발생하지 않기 때문에 `max-age=60 * 60 * 24 * 365`로 배포했다.
* 다음 배포에서 js, css 일부 파일에 변경이 발생했다.
* 브라우저는 max-age를 1년으로 설정했기 때문에 재요청을 보내지 않고 캐싱하고 있다.
* 어떻게 해결할 수 있을까요?

#### \* 해결책

배포 시간 또는 버전 등을 활용해 URL 변경하면 새로 받아오기 때문에 위의 문제가 해결됩니다.\
파일명을 변경하지 않으려면, 캐시 무효화 방식으로 해당 이미지만 업데이트할 수도 있습니다.

```java
public class WebMvcConfig implements WebMvcConfigurer {
    public static final String PREFIX_STATIC_RESOURCES = "/resources";

    @Autowired
    private BlogVersion version;

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler(PREFIX_STATIC_RESOURCES + "/" + version.getVersion() + "/**")
                .addResourceLocations("classpath:/static/")
                .setCachePeriod(60 * 60 * 24 * 365);
    }
}
```

#### \* 배포 시간 또는 버전을 사용할 때의 문제 상황

* 서버를 점진적으로 배포하고 있다.
* as-is 서버의 html은 /resources/20200610153420/main.js, to-be 서버의 html은 /resources/20200617172130/main.js을 사용하고 있다.
* 그런데 점진적인 배포 중에 as-is 서버의 /resources/20200610153420/main.js 요청이 to-be 서버로 요청이 간다면 어떻게 될까요?

\
\
\


## 7. CDN

![](https://nextstep-storage.s3.ap-northeast-2.amazonaws.com/8d716f397eb84471bab894e47cbd940c)

CDN(Content Delivery Network)란, 여러 노드를 가진 네트워크에 컨텐츠를 저장하여 제공하는 프록시의 일종이라고 생각하면 되어요.

1. CDN Edge 서버는 사용자와 (Reverse Proxy에 비해) 가까운 곳에 위치하며 캐시된 컨텐츠를 전달하므로, 네트워크 지연과 패킷 손실 현상이 줄어드는 등 RTT(Round Trip Time)이 줄어 컨텐츠를 빠르게 받을 수 있어요.
2. CDN의 Edge 서버가 캐시된 컨텐츠를 전송하므로 원본 서버의 부하를 줄일 수 있어요 (Reverse Proxy와 같은 역할)
3. 인프라를 확충하는데 인력과 경비를 줄일 수 있어요.
4. CDN 서비스는 Edge 서버들간에 캐시를 공유하고 있는데요. [롱테일](https://ko.wikipedia.org/wiki/%EA%B8%B4\_%EA%BC%AC%EB%A6%AC) 컨텐츠의 경우에도 CDN을 사용하면 다양한 지역에서 접근하고 그 캐시가 공유되므로, 캐시 적중률이 높아집니다.
