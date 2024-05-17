# 2.1 네트워크 어플리케이션의 원리

## 2.1.1 네트워크 어플리케이션 구조
`어플리케이션 구조` : 어플리케이션 개발자가 설계하며, 다양한 종단 시스템에서 어떻게 조직되어야 하는지를 알려준다. 
또한, 어플리케이션 구조를 선택할 때, Network Application에서 사용되는 두 가지 잘 알려진 구조 중 하나로 작성한다.

`클라이언트-서버 구조`

![](https://velog.velcdn.com/images/calzone0404/post/d5218efe-11bb-4cf4-9c12-e3286ee9ea97/image.png)

- 이 구조에서 항상 서비스를 제공하기 위해 동작하고 있는 호스트를 **Server**라고 부른다.
서버의 서비스는 Client라는 다른 호스트들로부터 서비스 요청을 받는다.
- Server에게 서비스를 요청하는 호스트를 **Client**라고 부른다.

특징 1. 클라이언트는 서로 직접적으로 통신하지 않는다. 즉, 항상 서버를 통해 통신한다.
특징 2. 서버가 고정 IP주소를 이용하여 Client에게 통신할 수 있는 환경을 제공한다.

`P2P구조`

![](https://velog.velcdn.com/images/calzone0404/post/f3a46cd8-be82-4344-8a53-7c9bfbb58cbd/image.png)

- 이 구조에서는 항상 켜져 있는 Infrastructure Server에 최소로 의존하거나 전혀 서버를 사용하지 않는다.
즉, 통신할 때 Client가 **Peer**가 되어 서로 직접 통신하게 된다.
예를 들면, Bittorrent같이 파일 공유 어플리케이션이 해당한다.

특징 1. **자가 확장성**
예를 들어, 파일 공유 어플리케이션에서 각 Peer들이 파일을 요구함으로써 작업 부하를 생성하지만, 각 피어들은 다른 피어들에게 분배함으로써 그 시스템에 서비스 능력도 추가한다.

특징 2. **비용 효율성**
서버에서 서비스를 제공받는것이 아니기 때문에 비용을 절약할 수 있다.

## 2.1.2 프로세스 간 통신

Host에서 실행하는 프로그램이 직접 통신하는것이 아니라, **Process**가 통신하는것이다. 이 2개의 Host에서 통신하기 위해 ``메세지``를 교환하며 서로 통신한다.

``Process`` : 종단 시스템에서 실행되는 프로그램

### 클라이언트와 서버 프로세스

> 두 프로세스 간의 통신 세션에서,
- Client : 다른 프로세스와 세션을 시작하려고 접속을 초기화하는 프로세스
- Server : 세션을 시작하기 위해 접속을 대기하는 프로세스

### 프로세스와 컴퓨터 네트워크 사이의 인터페이스

![](https://velog.velcdn.com/images/calzone0404/post/09387621-8d47-4aa2-a438-07568d2fbc38/image.png)

프로세스끼리는 **Socket**이라고 불리는 출입문을 통해 통신한다.
위 그림에서 보는것과 같이, Socket은 Application 계층과 Transport 계층간의 **Interface**이다. 따라서, Socket은 **API**라고도 불린다.

### 프로세스 주소 배정

패킷을 다른 호스트로 전달하기 위해서는 해당 호스트의 **주소**를 가져야한다. 네트워크에서는, 호스트를 식별하기 위해서 **IP주소**와 어떤 어플리케이션에서 사용중인지를 식별하기 위해 **Port 번호**를 사용한다.

## 2.1.3 어플리케이션이 이용 가능한 트랜스포트 서비스

### 1. 신뢰적 데이터 전송

>패킷이 네트워크에서 오버플로되거나, 버려지는 경우에 대한 데이터 손실을 최소화하기 위해 특정 Host가 전송한 패킷이 완전하게 다른 Host로 전달될 수 있도록 보장하는 조치

### 2. 처리율

> 처리율 : 네트워크 경로를 따라 두 프로세스간의 통신 세션에서 송신 프로세스가 수신 프로세스로 비트를 전달할 수 있는 비율

전송계층은 응용계층에서 특정한 처리율 $r$을 요구하면, 최소한 $r$을 보장하도록 처리한다.

여기서 특정한 처리율을 요구한느 어플리케이션을 **대역폭 민감 어플리케이션**이라고 한다.

반면 **탄력적 민감 어플리케이션**은 가용한 처리율을 자유롭게 이용할 수 있다. SMTP, HTTP, FTP와 같은 프로토콜이 그 예시이다.

### 3. 시간 보장

> 송신자가 소켓으로 내보내는 모든 비트가 수신자의 소켓에 특정 시간 (예: 100ms) 내에 도착하도록 보장하는 것

### 4. 보안

> 송신 프로세스가 전송하는 모든 데이터를 암호화하고, 수신측에서는 이를 복호화할 수 있는 서비스를 제공한다.

## 2.1.4 Transport Protocol이 제공하는 서비스

### TCP Service
![](https://velog.velcdn.com/images/calzone0404/post/bfd22e19-b78e-4fa5-8d16-2c302fd1eaab/image.png)

> TCP 프로토콜은 연결지향형 서비스와 신뢰성 있는 데이터 전송 서비스를 제공한다.

**``연결지향형``** : Client와 Server간의 메세지를 교환하기 이전에, 서로 **전송 제어 정보를 교환하는 3-way-handshake**를 수행하게 한다.

**``신뢰적인 데이터 전송``** : 통신 프로세스는 모든 데이터를 오류 없이 올바른 순서로 전달하게 한다.

### UDP Service

> UDP 프로토콜은 최소한의 서비스를 제공하기 위한 간단한 전송 프로토콜이다.

- TCP와 달리 **handshake**과정이 존재하지 않는다.

- 비신뢰적인 데이터 전송 서비스를 제공한다. 즉, UDP Socket으로 데이터를 전송하면 수신지에 올바르게 도착하는지는 상관하지 않는다.


### TLS

기존 TCP, UDP는 암호화 서비스를 제공하지 않았다. 하지만, Transport Layer Security 서비스를 개발하여 **TCP**가 하는 모든 서비스에 더해서, **암호화, 데이터 무결성, 종단 인증**등의 중요한 보안 서비스를 제공한다.

> TLS는 TCP나 UDP처럼 Transport Layer Protocol이 **아니다!**
TLS는 어플리케이션 계층에 구현된 서비스이다.

![](https://velog.velcdn.com/images/calzone0404/post/cc175f3b-9e3c-48a7-ace8-c9136bfabae3/image.png)


## 2.1.5 어플리케이션 계층 프로토콜

> Application Layer Protocol은 서로 다른 Host에서 실행되는 Application process가 서로 메세지를 보내는 방법을 정의한다.

- **교환 메세지 타입** (요청 메세지, 응답 메세지)
- **여러 메세지 타입의 문법**(메세지 내부 필드와 필드간의 구별 방법)
- **필드의 의미**, 즉 필드에 있는 정보의 의미
- **언제 어떻게 프로세스가 메세지를 전송하고 메세지에 응답하는지 결정하는 규칙**

이 계층 프로트콜은 **RFC**에 명시되어 있으므로, 공중 도메인에서 찾을 수 있다. https://www.rfc-editor.org/standards

만약 웹 개발자가 HTTP RFC 규칙을 따른다면, 다른 모든 HTTP RFC규칙을 따른 웹 서버는 해당 서비스를 이용할 수 있다.

# 2.2 웹과 HTTP
## 2.2.1 HTTP 개요

> HTTP는 서로 다른 종단 시스템에서 수행되는 웹 프로그램이 서로 메세지를 교환할 때 사용하는 프로토콜이다.

HTTP 프로토콜은 메세지의 구조 및 클라이언트와 서버가 어떻게 메세지를 교환하는지 정의한다.

어떤 HTTP 프로젝트 파일에서, HTTP 프로토콜은 각 객체들(html, css, js 등...)을 **URL**을 통해서 접근한다.

``http://www.asdf.com/store/asdf.gif``

- ``www.asdf.com`` : 호스트 이름
- ``/store/asdf.gif`` : 경로 이름

``Client``는 **웹 브라우저**를 통해 웹 페이지를 보여주고, 여러가지 인터넷 검색과 서비스를 제공한다.

``Web Server``는 URL을 통해 각각의 웹 구성요소 객체를 지정할 수 있다. 주로 Apache, IIS가 이에 해당한다.

### HTTP 통신 과정

![](https://velog.velcdn.com/images/calzone0404/post/dd74f306-2c15-4581-be12-a2679861d8c6/image.png)

1. Client가 Web Server에 TCP 연결을 수립한다.
2. Browser와 Server Process는 Socket을 통해 TCP로 접속한다.
3. Client가 **HTTP Request** 메세지를 보내서 특정 서비스 또는 콘텐츠를 요구한다.
4. Server는 **HTTP Response** 메세지로 Client가 요구한 서비스 또는 콘텐츠를 요구한다.

## 2.2.2 Stateless, Stateful Connection

> HTTP 서버는 Client에 대한 정보를 유지하지 않는 **stateless protocol**이다.

- ``Stateless connection`` : 각 Request/Response쌍이 개별된 TCP Connection을 통해 수행되는 경우를 말한다.
(즉, 통신할때마다 새로 TCP 연결 수립 후 요구한 서비스 및 콘텐츠 수신/송신 시 연결 해제)

- ``Stateful connection`` : 각 Request/Response쌍이 같은 TCP Connection을 통해  수행되는 경우를 말한다.

### Stateless HTTP

예를 들어, 11개의 콘텐츠를 요청하는 Client가 있을 때, 동작을 살펴보자.

1. Client-Server간 특정 포트번호 (주로 80번)를 통해 TCP 연결을 수립한다.
2. Client의 Socket에서 HTTP Request를 보낸다.
3. Client가 요구한 콘텐츠를 추출하여  HTTP Response 메세지에 캡슐화하여 Socket을 통해 전달한다.
4. 모든 요구사항을 해결하였으므로 TCP Connection을 끊어버린다.

하나의 작업을 다 수행하면, 연결이 끊어지기 때문에 나머지 10개의 콘텐츠 요구에 대해서도 **전부 일일히 TCP연결을 다시 수립해야한다**

![](https://velog.velcdn.com/images/calzone0404/post/e08e12af-990c-48b5-8d61-fc71b1caa97c/image.png)
위 그림처럼 11번 연결을 한다면 총 2 * 11 RTT가 소요된다.

> RTT(Round-trip-time) : 패킷이 Client에서 Server까지 가고, 다시 되돌아오는데 걸리는 시간

> 만약, 순차적으로 TCP연결을 수행하는 경우, n개의 요구에 대해서 $2*RTT$ + 서버가 파일을 전송하는데 걸리는 시간이소요된다. 하지만, 동시성 연결을 지원하는 경우는 이를 획기적으로 줄일 수 있다.

### Stateful HTTP

> Stateless는 각 TCP연결을 수립해야하므로 수많은 TCP 버퍼가 필요하게 된다. HTTP/1.1 버전부터는 하나의 연결을 계속 유지하도록 하는 Stateful을 지원한다.

일반적으로 Stateful은 지속적으로 연결이 유지되지만, Client-Server간의 통신이 없는 경우, 일정 시간 이후에 연결을 해제한다.

## 2.2.3 HTTP 메세지 포맷

![](https://velog.velcdn.com/images/calzone0404/post/b3463e60-5a28-48e9-b314-f1fedcdd18bb/image.png)

### HTTP Request

> Start line : 요청 라인이라고도 불린다.

요청 라인은 3개의 필드로 구성되는데, ``Method Field``, ``URL Field``, ``HTTP Version Field``로 구성된다.
``Method Field`` : GET, POST, HEAD, PUT, DELETE같은 방법 요소들이 채워진다.
``URL Field`` : URL로 식별되는 객체(html, 표시할 어떤 객체)를 요청하기 위해 사용한다. 위 그림에서 Reqeust의 `/`가 바로 URL이다.
``Version Field`` : HTTP 버전이다.

> HTTP headers

``Host`` : 요청하는 객체를 가지고있는 Host의 주소이다.
``User-Agent`` : 서버에게 요청하고 있는 브라우저의 종류이다.
``Accept`` : 요청하는 객체의 원하는 버전 정보를 기술하고 있다.
``Connection`` :
- keep-alive인 경우 : stateful 연결
- close인 경우 : stateless 연결

> Body : GET, HEAD, DELETE , OPTIONS처럼 리소스를 가져오는 요청은 보통 본문이 비어있고, PUT, POST같은 경우에만 채워놓는다.

### HTTP Response

> Start line

``Version Field`` : HTTP 버전이다.
``Status Code`` : Request의 성공 여부를 나타내는 status code이다. 주로 200번대, 400번대, 500번대를 많이 볼 수 있다. [참고 링크](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
``Status Text`` : 사람이 HTTP의 성공 여부를 쉽게 알 수 있도록 Text로 표시한 Field

> HTTP headers

``Server`` : 메세지가 어떤 웹서버에 의해 만들어졌는지 나타낸다. 그림에서는 Apache WebServer
``Content-type`` : body가 어떤 타입인지 나타낸다. 그림에서는 HTML임을 알 수 있다
``Content-length`` : 송신되는 객체의 Byte 수를 나타낸다./
``Date`` : 서버가 요청메세지를 생성하여 **전송한 시간**을 나타낸다.
``Keep Alive`` : 해당 HTTP 메세지의 유효기간을 나타낸다.
``Connection`` :
- keep-alive인 경우 : stateful 연결
- close인 경우 : stateless 연결

## 2.2.4 사용자와 서버 간의 상호작용 : Cookie

![](https://velog.velcdn.com/images/calzone0404/post/7210c39e-3879-4587-bf7a-3743dfba4f75/image.png)

HTTP는 원래 Stateless하기 하다.
개발자들은 이러한 한계를 극복하기 위해 **Client의 상태를 저장하기 위해서** ``Cookie`` 라는 기능을 개발하였다.

``Cookie``는 사용자의 상태를 기억하여, 해당 사용자를 위해 맞춤형 서비스 또는 기능을 하기 위해 사용된다.
위 사진을 통해 Cookie가 어떻게 설정되는지 한번 살펴보자

<hr>

1. Client는 기존에 Ebay에 방문해서 8734라는 Cookie가 존재한다.
2. Client가 ebay에 접속을 시도하기 위해 HTTP Request를 보낸다.
3. Server는 해당 Client에 대한 새로운 Cookie를 생성한다. (1678)
4. HTTP Response에 해당 Cookie 정보를 담아서 보낸다.
5. 이후 Client가 다시 ebay에 접속했을 때, Cookie정보를 HTTP와 함께 전송한다.
6. Server는 해당 Cookie를 통해 사용자 맞춤 서비스 및 기능을 제공한다.

## 2.2.5 웹 캐싱

> **Web Cache (Proxy server)**은 HTTP Request를 Server 대신에 Response하는 Network 객체이다.

예를 들어, Client가 http://www.asdf.com/asdf.gif라는 객체를 요구한다고 가정해보자

![](https://velog.velcdn.com/images/calzone0404/post/cb7a6194-f732-4c79-9f8b-b16747b999ff/image.png)
1. Client가 HTTP Request를 요청한다.
2. 해당 요청이 먼저 Proxy Server에 들린다.
3. Proxy Server에서 해당 요청에 대한 객체가 존재하는 지 찾는다. 만약 해당 객체가 존재하면, Client로 HTTP Request를 보낸다.
4. 만약 객체가 없다면 Proxy Server가 Web Server에게 HTTP Request를 보낸다.
5. Web Server가 HTTP Request를 Proxy Server로 보내고, Proxy Server는 해당 객체를 받아서 자신의 서버에 저장한 후에, Client에게 HTTP Response를 전달한다.

> 일반적으로 Proxy Server의 사용은 요청 응답속도의 개선을 기대할 수 있다. 

Proxy Server는 주로 ISP 회사가 각 Client들의 LAN대역에 하나씩 설치해준다. 
따라서, Clinet는 먼저 LAN에 존재하는 Proxy Server에서 빠르게 HTTP Request에 대한 Response를 기대할 수 있다.


### 조건부 GET

Proxy Server의 한가지 문제점이, Proxy Server에 존재하는 객체가 **최신 상태가 아닐 가능성이 있다**.

> 따라서 HTTP는 클라이언트가 브라우저로 전달되는 모든 객체가 최신의 것임을 확인하면서 Caching을 하게 해주는 방식을 사용한다. 이것이 바로 **조건부 GET** 방식이다.

HTTP Request Message에 만약 해당 내용이 들어있다면, 조건부 GET 방식이다.
![](https://velog.velcdn.com/images/calzone0404/post/c7be09cb-3f79-4a0f-9400-fa64e7a2de51/image.png)

- GET 방식 이면서
- IF-modified-since 필드가 존재하는 경우

### 조건부 GET의 동작방식

1. Client의 Request에 대해 Proxy Server가 Web Server에 Request 메세지를 보낸다.
2. Web Server는 Proxy Server에게 객체를 가진 응답 메세지를 보낸다.
![](https://velog.velcdn.com/images/calzone0404/post/312c4383-7532-4fef-9416-565628773c4d/image.png)

3. 위 사진처럼, Proxy에 해당 객체를 저장할 때, 객체를 저장하는 시점 **Last-Modified**을 함께 저장한다. 이후, Client에게 Response 한다.
4. 만약 시간이 지난 후에 Clinet가 동일한 객체를 Response하는 경우, Client의 **Browser**에서 조건부 GET으로 조사를 시작한다.
5. **Last-Modified**와 **If-modified-since**가 일치한다면, Web Server는 304 Not Modified와 Empty body로 응답한다. **즉, Proxy Server에 저장된 복사본이 최신상태임을 의미한다.**

# 2.3 인터넷 전자메일

인터넷 전자메일 시스템은 다음과 같이 구성된다.
![](https://velog.velcdn.com/images/calzone0404/post/581f08a9-a411-4c9e-84a0-f9133d674549/image.png)

전자메일은 **비동기적 통신 매체**로, 자신이 편할 때 메세지를 보내거나 읽을 수 있다.

인터넷 메일 시스템은 크게 3가지의 주요 요소로 이루어져 있다.

`User Agent` : 사용자가 메세지를 읽고, 응답하고, 전달하고, 구성하게 해준다. (Outlook, Gmail, Apple mail ... )

예를 들어, A가 메세지 작성을 끝내고 전송 버튼을 누르면 **User agent**는 메세지를 메일 서버로 보내고, 메일 서버의 **출력 메세지 큐**에 들어가게 된다.

만약 B가 A가 보낸 메세지를 받고 싶다면, 메일 서버안의 메일 박스에서 메세지를 가져오게 된다.

`Mail Server` : 전자 메일 인프라의 중심으로, B와 같이 **수신자**들 각각에 대한 **Mail box**가 존재한다.
예를 들어, B 전용 Mail box에서 B는 자신에게 수신된 메세지를 확인할 수 있다.

일반적으로 전자 메일은 **A의 User agent**에서 전달이 시작되고, **A의 Mail Server**를 거쳐서, **B의 Mail Server**로 전달되며 최종적으로 **B의 User agent**를 통해 B가 메세지를 읽게 된다.

> 만약 A의 Mail Server가 B의 Mail Server로 메세지를 전달할 수 없다면, **Output message queue**에 일단 저장해두고, 나중에 서버 설정에 따라서 재전송을 시도한다. (너무 오래 전달이 안되면 버려지고, A에게 메세지 전송 불가 통보를 한다)

`SMTP` : 인터넷 전자메일을 위한 주요 Application Layer Protocol
SMTP는 A의 Mail Server에서 B의 Mail Server로 메세지를 전송하는 데 사용하는 **TCP 프로토콜**을 사용한다.

SMTP는 두가지 Mode로 동작한다.
`Client SMTP` : Mail Server가 상대 Mail Server로 메일을 보내는 경우
`Server SMTP` : Mail Server가 상대 Mail Server로부터 메일을 받는 경우

## 2.3.1 SMTP

### SMTP의 기본 동작

![](https://velog.velcdn.com/images/calzone0404/post/bbf73389-924d-412d-a9f4-0b9abf9d4d6f/image.png)

1. Alice가 User agent를 사용하여 Bob의 전자메일 주소와 함께 보낼 메세지를 작성하여, 전송 명령을 내린다.
2. User agent는 해당 전자메일을 메일 서버에 보내고 (SMTP 또는 HTTP POST), 메일 서버의 큐에 놓는다.
3. Alice의 메일 서버에서 동작하는 **SMTP**가 큐에 있는 전자메일을 확인하고, Bob의 메일 서버에 **TCP**연결을 수행한다.
4. SMTP Handshaking 이후, 전자메일을 **TCP**를 이용하여 송신한다.
5. Bob의 메일 서버의 **SMTP**는 메세지를 수신하고, 이 메세지를 **Bob 전용 Mail box**에 놓는다.
6. Bob은 편한 시간에 **User agent**를 사용하여 해당 메세지를 읽는다.

### SMTP가 메세지를 전달하는 방법
1. **Client SMTP(송신측 메일 서버)**는 **Server SMTP(수신측 메일 서버)**의 `25`번 포트로 TCP 연결을 설정한다.
2. 연결이 성립되면 **Handshaking**을 수행한다.
3. Handshaking 과정에서, **Server Client**가 송신자의 Email address와 수신자의 Email address 정보를 제공한다.
4. 이후, 메세지를 보낸다

위 과정을 좀 더 자세하게 보자. C(crepes.fr)는 Client SMTP이고, S(hamgergur.edu)는 Server SMTP이다.
![](https://velog.velcdn.com/images/calzone0404/post/44e7aaef-73c0-4ff1-95ca-6f69f86a118b/image.png)

> SMTP는 지속(Persistent)연결을 사용하기 때문에, 만약 송신 메일 서버가 수신 메일 서버로 보내는 여러 메세지가 존재한다면, 또 다시 Handshake를 수행하지 않고 여러개의 TCP 송신이 가능하다 (HTTP Stateful)

### 2.3.2 메일 메세지 포맷

전자메일을 전송할 때는 주변 정보가 포함된 Header가 Message 앞에 먼저 오게된다.
주변 정보는 반드시 다음 두개의 헤더라인을 가져야 한다. `From:`, `To:`

![](https://velog.velcdn.com/images/calzone0404/post/b020961d-7af3-481a-a58d-1ace897450a3/image.png)


### 2.3.3 메일 PULL 프로토콜 IMAP, HTTP

SMTP는 사실, User agent에서 어떤 메일을 읽으려고 할 때, 해당 작업을 수행하지 못한다.
그 이유는 SMTP가 **PUSH** 프로토콜이기 때문이다.
즉, 메일 서버에서 자신의 메세지를 가져오는 행위는 **PULL**이기 때문에, 해당 작업을 수행하지 못하는 것이다.

![](https://velog.velcdn.com/images/calzone0404/post/618e01e1-b5ac-4d12-9ac5-9e9e99c1f821/image.png)

이를 해결하기 위한 프로토콜이 바로 **`IMAP`**이다.
IMAP을 통해 **PULL**방식을 사용하여 User agent가 메일 서버의 Mail box에서 원하는 전자메일을 가져올 수 있게 된다.

다른 방법으로는, 만약 Bob이 웹 기반 메일서버로부터 메일을 가져온다면 **HTTP GET**을 사용하여 가져올 수 있다. 이 경우에는 물론 메일 서버는 SMTP interface와 HTTP interface가 구현되어 있어야 한다.

# 2.4 DNS: 인터넷의 디렉터리 서비스

>DNS는 인터넷에서 사람이 읽을 수 있는 도메인 이름을 숫자로 된 IP 주소로 변환해주는 서비스이다.

## 2.4.1 DNS가 제공하는 서비스

1. 호스트 이름을 IP주소로 변환해준다.
- 사용자들이 기억하기 쉬운 도메인 이름(www.asdf.com)을 입력하면 DNS는 이 도메인 이름을 숫자로 된 IP주소로 변환해준다.

2. 호스트 별칭: DNS는 하나의 도메인 이름에 대해서 여러개의 별칭을 설정할 수 있다. 
- 예를 들어, `www.asdf.com`과 `asdf.com` 모두 동일한 IP주소로 해석될 수 있다.
- 복잡한 이름을 가진 호스트는 하나 이상의 별칭을 가진다 예를 들어, `relay1.asdf.qwer.com`같은 **정식 호스트 이름**은 `qwer.com`, `www.qwer.com`과 같은 여러개의 **별칭**을 가질 수 있다.

3. 메일 서버 별칭 : 도메인 이름을 메일 서버 이름으로 매핑해서 이메일 트래픽을 메일 서버로 보낼 수 있게 한다.

4. 부하 분산 : 여러 서버에 부하를 분산시키기 위해 하나의 도메인 이름을 여러 IP주소에 매핑할 수 있다.


> DNS 프로토콜은 UDP상에서 수행되며, 53번 포트번호를 사용한다.

### DNS 수행 과정
1. 브라우저에 URL을 입력하면, 호스트 이름을 DNS Application client측에 넘긴다.
2. `DNS Application client`는 `DNS Server`로 호스트 이름을 포함하는 질의문을 전송한다
3. `DNS Client`는 호스트 이름에 매핑되는 IP주소 응답을 받는다.
4. 브라우저가 DNS로부터 IP주소를 받으면, 해당 IP주소와 80번 포트를 이용하여 HTTP TCP 연결을 수행한다.

## 2.4.2 DNS 동작 원리 개요
DNS는 계층 구조로 이루어진 서버를 통해서 작동한다.
![](https://velog.velcdn.com/images/calzone0404/post/e7241b13-bfe6-4d5b-a1c5-6d8d48f68f08/image.png)

1. `루트 DNS 서버` : 루트 서버는 최상위 도메인 서버 (TLD)로 쿼리를 전달하는 역할을 수행한다.
2. `최상위 도메인 서버 (TLD)` : `com`, `org`, `net`과 같은 상위 레벨 도메인과 `kr`, `uk`, `fr` 같은 모든 국가의 상위 레벨 도메인에 대한 서버이다. TLD 서버는 특정 도메인에 대해 권한이 있는 DNS서버 (책임 DNS 서버)로 쿼리를 전달한다.
3. `책임 DNS 서버` : 특정 도메인의 실제 도메인 이름과 IP 주소 매핑 정보를 가지고 있는 서버이다.

### DNS 질의 과정 - 반복 쿼리
![](https://velog.velcdn.com/images/calzone0404/post/153b7593-acb0-4897-a7e9-da675a692e65/image.png)

1. cse.nyu.edu를 질의하기 위해 로컬 DNS서버(ISP DNS 서버)로 질의를 보낸다.

2. Local DNS 서버가 질의에 대해 알지 못하면, Root DNS 서버로 질의를 보낸다.

3. Root는 `edu`를 알고있는 TLD서버의 주소를 던져준다.

4. Local DNS 서버에서 TLD 서버로 질의를 보낸다.

5. umass.edu에 해당하는 책임 서버의 주소를 던져준다.

6. umass.edu 책임 서버로 질의를 보낸다.

7. 자신의 DNS 레코드에서 IP를 찾아서 응답한다.

8. Host로 IP를 전달한다.

### DNS 질의 과정 - 재귀 쿼리

![](https://velog.velcdn.com/images/calzone0404/post/a276678e-92ee-48b8-8444-1a6d9907a137/image.png)

1. cse.nyu.edu를 질의하기 위해 로컬 DNS서버(ISP DNS 서버)로 질의를 보낸다.

2. Local DNS 서버가 질의에 대해 알지 못하면, Root DNS 서버로 질의를 보낸다.

3. Root는 `edu`를 알고있는 TLD서버로 질의를 보낸다

4. TLD에서 umass.edu에 해당하는 책임 서버의 주소로 질의를 보낸다.

5. 자신의 DNS 레코드에서 IP를 찾아서 TLD로 응답한다

6. 전달받은 IP를 Root DNS로 응답한다

7. 전달받은 IP를 Local DNS로 응답한다.

8. Host에게 IP를 전달한다.

### DNS 캐싱

위 과정은 DNS 캐싱이 적용되지 않은 상태에 대한 시나리오이다. 네트워크에 돌아다니는 DNS 패킷의 수를 줄이기 위해 각 DNS 서버에서 캐싱을 사용한다.

캐싱을 사용하게 되면, 계속 찾아다니지 않고도 바로 IP주소를 얻을 수 있기 때문에 **서버의 부하를 줄일 수 있으며**, DNS **질의 응답 속도가 향상**된다.

캐시에 저장된 DNS 매핑 정보는 **TTL** 정책에 따라, TTL이 만료될때까지 저장된다.

## 2.4.3 DNS 레코드와 메세지

DNS 레코드는 다음과 같이 구성되어 있다.
```text
(name, value, type, TTL)
```

### TYPE
- `Type = A` : 호스트 이름`Name`을 IPv4`Value`로 매핑한다.
- `Type = AAAA` : 호스트 이름`Name`을 IPv6`Value`로 매핑한다.
- `Type = NS` : 도메인에 대한 권한이 있는 네임서버 (**책임 서버**)를 나타낸다
- `Type = CNAME` : 정식 도메인 네임`Value`에 대한 별칭`Name`을 제공한다.
- `Type = MX` : 도메인에서 이메일을 수신하는 메일 서버를 지정한다.

### DNS 메세지

DNS 메시지는 다음과 같은 구조를 가지고 있다.
![](https://velog.velcdn.com/images/calzone0404/post/03951d2b-da5a-4315-924d-c9bc5e7749a5/image.png)

`헤더`: ID, 플래그(쿼리/응답, 권한 여부 등), 질문 수, 응답 RR 수, 권한 RR 수, 추가 RR 수 등의 필드

`질문 섹션`: 도메인 이름을 포함한 질의문

`응답 섹션`: 질문에 대한 리소스 레코드가 포함

`권한 섹션`: 권한 있는 서버를 가리키는 리소스 레코드

`추가 섹션`: 추가적인 정보.

# 2.5 P2P 파일 분배
지금 배우는 파일 분배구조는 앞서 배운 HTTP, DNS, SMTP같은 프로토콜과는 달리 서버 클라이언트 모델이 아닌, `P2P`구조를 사용한다.

### 클라이언트-서버 vs P2P 구조
- `클라이언트-서버` 구조에서는 서버가 각각의 Peer에게 파일을 개별적으로 전송해야 하므로, 서버 부하 및 대역폭 소비가 크다
- `P2P` 구조에서는 Peer(또는 간헐적으로 연결된 호스트)들이 서로 직접 통신해서 파일을 분배하는 구조이다.
이 구조는 서버의 부담을 줄이며 피어들이 파일의 일부를 서로 재분배할 수 있다.

### P2P 구조의 확장성
- `클라이언트-서버` 구조에서는 서버가 모든 업로드/다운로드 처리를 해야하므로 `분배 시간`이 `피어 수`에 따라 선형적으로 증가한다. 이 때문에, 피어가 많을수록 서버에 과부하가 걸리게 된다. (즉, **확장성**이 부족하다)
- `P2P`구조에서는 Peer들이 각 파일의 조각을 전달받고, 전달받은 조각들을 `재분배`함에 따라, 자체적으로 **확장성**을 가진다.
분배 시간은 모든 Peer들이 참여하기 때문에, 클라이언트-서버 구조보다 훨씬 짧으며, 피어가 늘어나도 관리가 수월하다.

### Bittorrent
- `Torrent`: 파일을 공유하는 피어들의 모음
- `Tracker`: 토렌트에 참여하는 피어들을 추척하는 기능. 피어들이 서로를 발견하고 연결할 수 있도록 돕는다.
- `Peer`: 토렌트 네트워크에 참여하는 모든 사용자. 파일을 다운로드함과 동시에 업로드를 수행한다.
- `Seed`: 파일의 모든 조각을 보유하고 있는 Peer이다. 새로운 Peer가 파일을 다운로드 할 때 처음에 필요한 데이터를 제공한다.

주로, 새로운 Peer가 처음 다운로드하는 조각은 `Rarest first(희소성 기반 다운로드 전략)`기술을 사용하여 가장 희귀한 파일 조각들을 먼저 다운로드 받으며, 그 다음으로 rarest chunck를 다운받으며 점점 전체를 채워나가는 방식을 사용한다.
- `Reacher`: 아직 파일을 모두 다운로드하지 못한 Peer이다.
- `Fragmentation`: 파일은 주로 일정 크기의 작은 조각들로 분할되고, 나누어진 조각들은 각각의 **Hash**값을 가져서 파일의 무결성을 체크할 수 있다.
- `Distribution`: Peer들은 서로 다른 조각을 동시에 다운로드하며 업로드한다. 즉, 네트워크 자원을 매우 효율적으로 사용할 수 있다.
- `Choke`: Peer가 연결된 다른 Peer에게 데이터를 보내지 않기로 결정한 상태 주로, Tit for tat 전략에서 사용한다.
- `Unchoke`: 초크 상태가 해제되어서 다시 데이터를 보내기로 결정한 상태이다.
- `Seeding`: 다운로드가 완료되면, 파일의 모든 조각을 보유하는 상태이므로 해당 파일을 다시 업로드해서 다른 Peer들이 다운로드받을 수 있도록 돕는 행위이다.

### Tit for Tat
- 파일 조각의 공유를 장려하기 위해서 Bittorrent에서는 `Tit for tat` 전략을 사용하고 있다.
- Peer들은 데이터를 주고받는 Peer와 **우선적으로** 조각을 교환하게 된다. 즉, 파일을 다운로드만 하고 업로드하지 않는 피어들을 억제해서 건강한 P2P환경을 유지하도록 한다.

# 2.6 비디오 스트리밍과 CDN
2020년 기준, Youtube, Netflix, Amazon 등을 이용한 스트리밍 비디오 서비스가 전체 인터넷 트래픽의 80%를 차지한다고 한다.
이 챕터에서는, 인터넷에서 가장 많이 사용하는 비디오 스트리밍 서비스가 어떻게 구현되는지 소개할 것이다.

## 2.6.1 인터넷 비디오
> 인터넷 비디오 스트리밍은 **미리 녹화된 비디오**(영화, TV 쇼, 스포츠 경기 등)를 서버에 저장하고, 사용자가 이를 요청하여 원하는 시점에 시청할 수 있도록 하는 방식이다.

주요 스트리밍 서비스 제공 업체로는 넷플릭스, 유튜브(구글), 아마존, 틱톡 등이 있다.

- `비디오의 기본 원리`: 비디오는 일반적으로 초당 24 또는 30 프레임으로 구성된 연속적인 이미지로 구성된다. 
각 이미지는 픽셀 배열로 디지털 인코딩되며, 비트 수로 휘도와 색상을 표현하게 된다.

- `비디오 압축`: 비디오는 압축이 가능하여, 비트 레이트와 비디오 품질 간의 균형을 맞출 수 있습니다. 압축 알고리즘을 통해 다양한 비트 레이트로 비디오를 압축할 수 있으며, **비트 레이트가 높을수록 더 나은 이미지 품질을 제공한다고 생각하자.**

- `네트워킹 관점`: 비디오는 높은 비트 레이트가 필요하다. 압축된 인터넷 비디오는 저품질의 경우 100kbps에서 고화질 영화 스트리밍의 경우 4Mbps 이상까지 다양하게 존재한다. 4K 스트리밍의 경우 10Mbps 이상의 비트 레이트를 요구하고 있다.

## 2.6.2 HTTP Streaming and DASH
### HTTP 스트리밍
HTTP 스트리밍에서는 비디오가 HTTP 서버에 일반 파일로 저장된다. 
사용자가 비디오를 시청하려면, 먼저 클라이언트에서 서버와 TCP 연결을 설정하고 HTTP GET 요청을 보낸다. 
서버는 HTTP 응답 메시지로 비디오 파일을 보내며, 클라이언트는 버퍼에 해당 영상 데이터를 모아서 사용자에게 보여주게 된다.
- 단점: 모든 클라이언트가 동일한 인코딩의 비디오를 받으므로, 사용 가능한 대역폭에 따라 품질이 달라지는 문제점이 있다. 즉, 어떤 사용자는 원활하게 시청이 가능하지만, 어떤 사용자는 영상이 끊기는 (버퍼링) 현상이 많이 발생할 수 있다.

### DASH (Dynamic Adaptive Streaming over HTTP : 동적 적응 스트리밍) 
DASH도 HTTP 프로토콜을 통해 영상 정보를 전달받는다.

> 하지만, 일반적인 HTTP 스트리밍과는 달리 비디오를 **여러 비트 레이트로 인코딩**하고, 클라이언트가 네트워크 상태에 따라 적절한 비트 레이트의 비디오 조각을 선택하여 다운로드하는 방식이다(즉, 유튜브에서 해상도 설정하는거라고 생각하면 된다). 

DASH를 사용할 때, 각 비디오 비트레이트 버전은 HTTP 서버에 서로 다른 URL을 가지고 저장된다.
HTTP 서버에서는 비트레이트에 따른 각 버전의 URL을 **manifest 파일**을 통해 관리한다.
클라이언트에스는 원하는 버전의 비디오 품질을 선택해서, HTTP GET 요청에 URL과 원하는 **bit-range**를 지정하여 request하게 된다.

서버로 전달받은 비디오 데이터를 다운로드하면서, 이제 다음에 다운로드할 비디오 조각을 결정하는데 있어서 현재 클라이언트의 비디오 다운로드가 충분히 버퍼에 저장되어 있는지, 수신 대역폭이 충분하지 확인한다.

> 이를 체크해서 다음 비디오 조각을 더 좋은 품질로 가져올지, 똑같은 품질을 가져올지, 좀 품질이 낮은것으로 가져올지 결정하게 된다.

## 2.6.3 CDN


## 2.6.4 Netflix/Youtube 사례 연구

# 2.7 소켓 프로그래밍
