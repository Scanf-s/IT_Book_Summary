# 2.1 네트워크 어플리케이션의 원리

## 2.1.1 네트워크 어플리케이션 구조
``어플리케이션 구조`` : 어플리케이션 개발자가 설계하며, 다양한 종단 시스템에서 어떻게 조직되어야 하는지를 알려준다. 
또한, 어플리케이션 구조를 선택할 때, Network Application에서 사용되는 두 가지 잘 알려진 구조 중 하나로 작성한다.

``클라이언트-서버 구조`` 
![](https://velog.velcdn.com/images/calzone0404/post/d5218efe-11bb-4cf4-9c12-e3286ee9ea97/image.png)

- 이 구조에서 항상 서비스를 제공하기 위해 동작하고 있는 호스트를 **Server**라고 부른다.
서버의 서비스는 Client라는 다른 호스트들로부터 서비스 요청을 받는다.
- Server에게 서비스를 요청하는 호스트를 **Client**라고 부른다.

특징 1. 클라이언트는 서로 직접적으로 통신하지 않는다. 즉, 항상 서버를 통해 통신한다.
특징 2. 서버가 고정 IP주소를 이용하여 Client에게 통신할 수 있는 환경을 제공한다.

``P2P구조``
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

