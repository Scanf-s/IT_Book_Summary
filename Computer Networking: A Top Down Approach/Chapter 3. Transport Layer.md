# 3.1 Introduction and Transport-Layer Services

![](https://velog.velcdn.com/images/calzone0404/post/47d0ae3b-42f3-4030-81fb-2d8295e06c36/image.png)

>전송계층 프로토콜은 각기다른 호스트에서 실행되고 있는 프로세스들간의 논리적인 커뮤니케이션을 제공한다. 

**논리적인 커뮤니케이션**을 통해서 각자 호스트의 어플리케이션의 관점에 있어서 마치 호스트가 지구 정반대편에 서로 존재하더라도, 직접적으로 연결되어있는 것 같은 착각을 불러일으킨다.

전송계층 프로토콜은 **네트워크 라우터**에서 구현되는 것이 아닌, **말단장치**에서 구현된다.

>송신측에서의 전송계층은 응용계층으로부터 전달받은 메세지를 변환하여 **전송계층의 패킷**으로 변환한다. 이 전송계층의 패킷을 **SEGMENT**라고 부른다.

어플리케이션 계층의 메세지를 작은 단위로 쪼개서, 전송계층의 헤더에 각각의 청크를 넣어서 전송계층의 세그먼트를 생성한다. 그리고 네트워크 계층에서 Packet을 Datagram 단위로 캡슐화 작업 후, 목적지로 보내버린다. 수신측에서는 이와 정반대의 과정이 일어난다.

## Relationship Between Transport and Network Layers

> 전송계층은 각기다른 호스트에서 실행되고 있는 **Process**간의 논리적인 커뮤니케이션을 지원하는 계층이다.
- 즉, 전송계층 프로토콜은 종단 시스템에서 구현되며, 응용계층과 네트워크계층 사이에서 메세지를 운반하는 역할을 수행한다.

> 네트워크 계층은 호스트끼리의 논리적인 커뮤니케이션을 지원하는 계층이다

전송 프로토콜이 제공할 수 있는 서비스는 **네트워크 계층 프로토콜의 서비스 모델에 의해 제약을 받는다**
- 만약 네트워크 계층이 안전한 전송을 위한 delay 또는 충분한 대역폭을 제공하지 못한다면 전송계층 프로토콜은 응용계층에서 다른 호스트의 프로세스로 전달하려는 메세지에 대해  delay 또는 충분한 대역폭을 보장할 수 없다.

하지만, 네트워크 프로토콜이 네트워크 계층에서 이러한 delay나 충분한 대역폭을 보장하지 않아도 전송 프로토콜은 특정 서비스를 제공할수는 있다.
- 예를 들어서, 네트워크 계층이 비신뢰적인 상태라면 전송계층이 신뢰성있는 데이터 전송을 보장할 수 있다는 의미이다.
- 즉, 네트워크 프로토콜이 패킷을 손실, 왜곡 또는 패킷을 복제하는 경우에도 전송 프로토콜은 암호화를 사용하여 패킷의 보안을 지킬 수 있다.

## Overview of the Transport Layer in the Internet

인터넷은 애플리케이션 계층에 **UDP(User Datagram Protocol, 사용자 데이터그램 프로토콜)**와 **TCP(Transmission Control Protocol, 전송 제어 프로토콜)** 두 가지 서로 다른 전송 계층 프로토콜을 제공합니다. 

UDP는 **신뢰할 수 없는** 비연결 서비스를, TCP는 **신뢰할 수 있는** 연결 지향 서비스를 제공한다. 네트워크 애플리케이션을 설계할 때, 개발자는 이 두 전송 프로토콜 중 하나를 선택해야 한다.

전송 계층 패킷을 간략히 'Segment'라고 하며, TCP의 경우는 'Segment', UDP의 경우는 일반적으로 'Datagram'으로 불린다.

> 하지만,네트워크 계층 패킷에도 같은 용어를 사용하여 혼동을 방지하기 위해 **TCP와 UDP 패킷 모두를 'SEGMENT'**라고 칭하고, 'Datagram' 용어는 네트워크 계층 패킷에만 사용한다.

인터넷의 네트워크 계층은 **IP(Internet Protocol, 인터넷 프로토콜)**라는 프로토콜을 사용하여 호스트 간의 논리적 통신을 제공한다.

IP는 ***'최선 노력 배달 서비스'***를 제공하는데, 이는 IP가 통신 호스트 간에 세그먼트를 배달하기 위해 최선을 다하지만 배달, 순서대로 배달, 데이터 무결성을 보장하지 않기 떄문에 **IP는 신뢰할 수 없는 서비스로 간주**된다. 모든 호스트는 최소한 하나의 네트워크 계층 주소인 IP 주소를 가지고 있다.

UDP와 TCP의 가장 기본적인 동작 규칙은 IP의 두 end-system 간의 배달 서비스를 **두 process 간의 배달 서비스로 확장**하는 것 (전송계층이 하는 작업)이다. 이를 위해 전송 계층의 **멀티플렉싱과 디멀티플렉싱**을 수행한다.

UDP와 TCP는 또한 세그먼트 header에 "오류 검출 필드"를 포함함으로써 무결성 검사를 제공한다. UDP는 프로세스 간 데이터 배달과 오류 검사만을 제공하며, 신뢰할 수 없는 서비스인 반면, TCP는 신뢰할 수 있는 데이터 전송, 흐름 제어, 순서 번호, 승인, 타이머 등을 사용하여 데이터가 올바르고 순서대로 전달되도록 보장한다. 

TCP는 전체 인터넷을 위한 서비스로서, 연결의 공정한 대역폭 공유를 위해 각 연결의 전송 속도를 조절함으로써 네트워크의 혼잡을 관리하는 **혼잡 제어 기능**을 제공한다.

# 3.2 Multiplexing and Demultiplexing

이 섹션에서는 전송계층의 multiplexing과 demultiplexing에 대해서 소개한다. 

목적지 호스트에서, 전송계층은 네트워크 계층으로부터 세그먼트를 전달받는다. 전송계층은 전달받은 세그먼트들을 현재 실행중인 적절한 응용 프로세스들에게 전달해야만 한다.

예를 들어, 당신이 FTP 세션과 두개의 Telnet 세션을 실행중인 동시에, Web 페이지를 다운로드하는 중이라고 가정해보자. 만약 전송계층에서 하나의 세그먼트를 받았을 때, 이 세그먼트를 HTTP 프로토콜로 보내야할지, FTP로 보내야할지, Telent으로 보내야할지 결정해야 한다.

프로세스는 하나 또는 그 이상의 **소켓**들을 가질 수 있다. 이 **소켓**은 데이터가 네트워크에서 프로세스로, 프로세스에서 네트워크로 전달되는 **문**이 된다. 
![](https://velog.velcdn.com/images/calzone0404/post/7295bf2f-9732-41c7-93d1-2214af266951/image.png)

위 사진에서, 전달받는 측의 전송계층은 실제로 프로세스에게 직접적으로 전달하지 않음을 볼 수 있다. 대신, 중간에 존재하는 소켓을 통해서 전달한다. 따라서 수신측 호스트에는 하나 또는 그 이상의 소켓이 존재한다. 이 소켓은 UDP인지 TCP인지로만 식별된다.

각 전송계층에 전달된 세그먼트는 목적에따른 필드들의 집합을 가지고 있다. 전송계층은 이 필드를 확인하여, 이 세그먼트의 목적지 소켓을 식별한 뒤, 해당 소켓으로 전달한다.

> 위 과정을 **Demultiplexing**이라고 한다.

송신측에서는 전송계층에서 다양한 소켓들로부터 데이터 청크를 모으고, 세그먼트를 생성하기 위해 헤더를 달고, 각 데이터 청크를 encapsulating 한뒤, 네트워크 계층으로 전달하게 된다.

> 위 과정을 **multiplexing**이라고 한다.

![](https://velog.velcdn.com/images/calzone0404/post/74ea9bac-8435-4efa-bb6c-02b76505e95e/image.png)

## Connectionless Multiplexing and Demultiplexing

파이썬 프로그램에서는 다음과 같이 **UDP socket**을 생성할 수 있다.

```python
clientSocket = socket(AF_INET, SOCK_DGRAM)
```
이 방법으로 UDP socket을 생성하게 되면, 전송계층은 자동으로 소켓에 **port number**을 할당하게 된다. 특히, 전송계층은 포트번호를 할당할 때, 다른 UDP가 주로 사용하지 않는 범위인 1024 ~ 65535 사이의 번호를 할당해준다. 물론, 우리가 직접 포트번호를 **bind()**함수를 통해 지정해줄수 있다.

```python
clientSocket = bind(('', 19157))
```

그런데, 만약 개발자가 "Well known protocol"코드를 작성하였다면, "Well know protocol"에 해당하는 "Well known port number"을 할당해주어야만 한다.

일반적으로, 클라이언트측의 어플리케이션은 전송계층이 포트번호를 자동으로 할당하도록 두지만, 서버측의 어플리케이션은 구체적인 포트번호를 지정해야한다.

## 구체적인 UDP 통신 과정

UDP port 19157을 가지는 Host A가 있다고 가정해보자. A는 UDP prot 46428을 사용하고 있는 Host B에게 프로그램 데이터를 송신하고 싶다.

이때, 송신자 A의 전송계층에서 세그먼트를 생성하는데, 세그먼트 안에는 송신자 A의 포트 번호(19157), 수신지 B의 포트번호(46428), 보내려는 어플리케이션 데이터, 또 추가적인 헤더 정보가 담긴다.

이 세그먼트 뿐만이 아니라 여러 세그먼트를 묶어서 (Multiplex) 네트워크 계층에게 넘기면 네트워크 계층에서 IP datagram에 캡슐화 후, 수신지 호스트에게 best-effort로 전달을 시도한다.

이제 B가 이 Datagram을 받게 되면 전송계층에서 목적지 port 번호를 확인하여 해당 포트번호의 소켓(42428)으로 넘긴다 (Demultiplex). 

> 중요한점 : UDP Socket은 목적지 IP주소와 목적지 port number을 통해 식별한다.

그럼 송신자, Source port number가 필요한 이유는 무엇일까? 바로, **Return address**로 작동하기 때문에 필요하다. B가 만약에 다시 A로 데이터를 전송하고 싶다면 A로부터 받은 Source Port number을 통해 다시 전송하면 매우 효율적으로 통신이 가능해진다.

<hr>

## 구체적인 TCP 통신 과정

TCP Demultiplexing을 이해하기 이전에, TCP Socket과 TCP 연결 수립 과정을 알아야만 한다. TCP는 Source ip address와 destination ip, source port, destination port number가 필요하다.

<hr>

### TCP 연결 수립 과정

1. TCP 서버는, 포트번호 12000에서 TCP 클라이언트로부터 연결 요청을 기다리는 "Welcoming socket"을 가진다.

2. TCP 클라이언트에서 소켓을 생성하고, 아래 명령을 통해 연결을 수립하는 요청을 보낸다.

```python
clientSocket = socket(AF_INET, SOCK_STREAM)
clientSocket.connect((serverName, 12000))
```
이때, TCP 헤더에 목적지 포트번호 12000과 특별한 연결 설정 비트를 담고, 클라이언트에서 선택한 출발지 포트번호에서 TCP segment를 전송하게 된다.

3. 서버에서 목적지 포트번호 12000인 세그먼트를 수신하게 되면, 이 세그먼트를 포트 번호 12000을 사용하고 있는 서버 프로세스로 전달한다.

<hr>

## Web Servers and TCP
![](https://velog.velcdn.com/images/calzone0404/post/3b9e03f5-09b7-4fcd-96a5-4838872872b6/image.png)

위 그림의 상황을 한번 살펴보자. 호스트 C가 두 HTTP 연결을 TCP로 B에게 전달하고 있고, 호스트 A가 하나의 HTTP 연결을 TCP로 B에게 전달하고 있다. 이때, 호스트 A의 source port 번호와 C의 source port 번호가 같은 세그먼트가 존재한다.

> 그러나, B는 이 두 세그먼트를 정확히 구별이 가능하다. 그 이유는 두 클라이언트 A와 C가 서로 다른 IP주소를 사용하고 있기 때문이다.

아파치 웹 서버와 같이 80번 포트에서 작동하는 웹 서버를 운영하는 호스트의 예를 들면, 클라이언트(예: 브라우저)가 서버로 세그먼트를 보낼 때 모든 세그먼트의 목적지 포트는 80이다.

이는 초기 연결 설정 세그먼트와 HTTP 요청 메시지를 담은 세그먼트 모두에 해당하며, 서버는 소스 IP 주소와 소스 포트 번호를 사용하여 위 사진처럼 다른 클라이언트의 세그먼트를 구분하게 된다.

클라이언트와 서버가 persistent HTTP를 사용하는 경우, 지속적인 연결 동안 클라이언트와 서버는 동일한 서버 소켓을 통해 HTTP 메시지를 교환하게 된다. 

반면에, non-persistent HTTP를 사용하는 경우, 각 요청/응답마다 **새로운 TCP 연결이 생성되고 종료**되며, 따라서 각 **요청/응답마다 새로운 소켓이 생성되고 나중에 닫힌다.** 

이러한 빈번한 소켓 생성 및 종료는 바쁜 웹 서버의 성능에 심각한 영향을 미칠 수 있다(비록 운영 체제의 여러 가지 기술을 사용하여 이 문제를 완화할 수 있음).

# 3.3 Connectionless Transport : UDP

# 3.4 Principles of Reliable Data Transfer
# 3.5 연결 지향형 프로토콜 TCP

## TCP Connection

> Connection-Oriented(연결 지향형)
- TCP는 어떤 데이터를 다른 프로세스에 보내기 전에, 두 프로세스가 서로 Handshake과정을 거쳐야 한다.

즉, 데이터 전송을 시작하기 전에, 전송을 보장하는 사전 정보를 보낸다.

TCP 연결은 Full-Duplex를 제공하며, 항상 Point to Point 연결을 통해 통신한다.

따라서 Multicasting은 불가능하다.

## TCP 연결 과정

- Client Process : 연결을 초기화하는 프로세스
- Server Process

### 3 Way Handshake (후에 자세히 다룬다)
- 동작과정
1. Client Process는 Server Process와 연결을 설정하려고 시도하는 SYN을 날린다.
2. Server Process는 SYN에 대해 답장 신호 ACK를 날린다.
3. Client Process는 ACK 신호에 대해 ACK 신호를 날린다.
4. 연결이 수립된다.

이러한 과정을 통해 양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장할 수 있게 된다.

## TCP Segment 구조
- TCP Segment : TCP Header + Client가 보내려는 Data

1. Header가 구성하는 요소
   - 출발지와 목적지 포트 번호
   - 체크섬 필드
   - 32비트 SYN 번호 필드
   - 32비트 ACK 번호 필드
   - 16비트 Window 정보
     - Window는 수신자가 받아들이려는 바이트의 크기를 나타내는데 사용한다.
   - 4비트의 TCP Header 길이 필드
   - Option Field
   - Flag Field
     - ACK : ACK 필드에 있는 값이 유효한지 확인하는 Flag
     - RST, SYN, FIN : 연결 설정 및 해제에 사용
     - PSH : 수신자가 데이터를 상위 계층에 즉시 전달해야 한다고 알리는 Flag
     - URG : 긴급 데이터 표시 Flag
     
     ![](https://velog.velcdn.com/images/calzone0404/post/42cc3376-6620-4189-9582-8c937efd7cad/image.png)

## Sequence 번호와 ACK 번호

실제 통신을 시작할 때, 수많은 패킷이 송수신 된다. 그런데, SYN과 ACK 번호가 존재하지 않는다면, 수신한 ACK 패킷이 어떤 패킷에 대한 ACK인지 모른다. 이를 위해서 Sequence 번호가 필요하다.

- **Sequence 번호**는 TCP Segment의 연속된 데이터 번호이다. 이때, Sequence 번호는 전송되는 Segment의 가장 앞에 있는 숫자를 표기한다.
>예를 들어, 1129 ~ 1181까지의 Byte Stream을 전송한다면 Sequence Number는 **1129**이다.
  
- **ACK 번호**는 상대방으로부터 받아야 하는 다음 TCP Segment 데이터 번호이다.

  >해당 번호 앞까지 데이터 처리를 완료하여 해당 번호 TCP Segment를 전송해달라고 요청하는 의미이다.
  
  > 예를 들어, 패킷을 1181까지 처리했다고 했을 때, 다음 번호인 1182부터 TCP Segment 데이터를 요청하기 위해 **ACK번호는 1182**로 표기된다.
  
### Telnet 통신 예시
![telnet](https://velog.velcdn.com/images/calzone0404/post/f85bd5c0-6f46-48eb-800e-040c0a3e8235/image.png)

1.
Seq 42 : A에서 송신한 첫 번째 Segment는 순서 번호 42를 가진다.
ACK 79 : A는 78까지 데이터 처리를 완료했으므로, 다음 순서인 79번째 데이터를 요청한다.

2.
Seq 79 : 79번째 데이터를 A로 송신한다.
ACK 43 : B는 42번째 데이터를 수신했으므로, 43번째에 해당하는 데이터를 보내달라고 **ACK 번호로 43**을 가진다.

3.
Seq 43 : 43번째 데이터를 B로 송신한다.
ACK 80 : A는 79에 대한 Segment 데이터를 받았으므로, 80번째 Segment를 보내달라고 요청한다.

## 왕복시간 Rount Trip Time (RTT) & Timeout
- RTT : 세그먼트가 전송된 시간부터 확인 응답 될 때 까지의 시간

> TCP는 손실된 Segment를 발견하기 위해 **Timeout/재전송 방법**을 사용한다.
이때, Timeout 시간은 연결의 **RTT** 시간보다 당연하게도 더 커야한다.

### 왕복 시간 예측
- SampleRTT : Segment가 송신된 시간으로부터, 그 Segment에 대한 ACK신호가 도착한 시간까지의 시간 길이

  대부분의 TCP는 한 번에 하나의 SampleRTT 측정을 시행한다.
  
- SampleRTT 값은 라우터에서의 혼잡, 종단 시스템에서의 부하 변화 때문에 Segment마다 시간이 전부 다르므로 **평균적인 RTT 시간(EstimatedRTT)**을구한다.

![estRTT](https://velog.velcdn.com/images/calzone0404/post/f2706a40-046f-4579-a0a8-f105250e4e9a/image.png)

### 재전송 타임아웃 주기 설정
- DevRTT : SampleRTT가 EstimatedRTT와 얼마나 벗어나 있는지에 대해서 정의한다.  
![devRTT](https://velog.velcdn.com/images/calzone0404/post/0445d061-d670-40c8-963d-5b889eddee76/image.png)

- Timeout값은 EstimatedRTT에 약간의 여윳값을 더하여 구한다.  
![TimeoutInterval](https://velog.velcdn.com/images/calzone0404/post/4ef67e52-9a77-4d0d-8860-46d871fcdd63/image.png)


## 신뢰성 있는 데이터 전송

> 1. TCP는 IP의 비신뢰적인 최선형 서비스에서 rdt를 생성한다.
> 2. Timeout, 중복 ACK 발생 시, 재전송을 수행한다.

### TCP 송신자 이벤트

- App으로부터 Data 수신 시
  - Sequence 번호를 붙여서 Segment를 생성한다.
  - 아직 다른 Segment에 대해 실행중이 아니라면, Timer를 시작한다.  
- Timer Timeout
  - Timeout 발생 시 Segment를 재전송한다.
  - Timer를 초기화하고, 다시 시작한다.  
- ACK 수신 시
  일단 TCP는 변수 SendBase와 ACK값 $y$를 비교한다.
  
  >SendBase : ACK가 확인 되지 않은 / 가장 오래된 바이트의 순서 번호
  >SendBase-1 : 수신자에게서 정확하게 차례대로 수신되었음을 알리는 마지막 바이트의 순서번호
  
  TCP는 누적된 ACK를 사용하고, $y$는 $y$바이트 이전의 모든 바이트의 수신을 확인한다.
  
  이때, $$y > SendBase$$이면, ACK는 이전에 ACK응답이 안 된 하나 이상의 Segment들을 확인한다.
    1. 송신자는 자신의 SendBase 변수를 갱신
    2. 아직 ACK응답이 안 된 Segment가 존재한다면, Timer를 다시 시작
    
- 예시
![](https://velog.velcdn.com/images/calzone0404/post/173f8eca-18f9-4d39-baef-4d03af6da232/image.png)

**오른쪽 그림 설명**

1. 92 ~ 100까지 데이터를 송신
2. 100 ~ 120까지 데이터를 송신
3. 100까지 받았다고 ACK
4. 120까지 받았다고 ACK
5. ACK 100에 대해 A는 92~100에 대해 응답이 아직 안되어있던 신호이므로 SendBase를 100으로 갱신
6. ACK 120에 대해 A는 100~120에 대해 응답이 아직 안되어있던 신호이므로 SendBase를 120으로 갱신
7. 92~100까지 데이터를 송신
8. 이미 받은 데이터이므로 120까지 받았다고 ACK 송신
9. A는 92~100에 대해 이미 응답을 받았으므로 SendBase를 갱신하지 않음

![examp](https://velog.velcdn.com/images/calzone0404/post/ade317a3-8f90-4281-94eb-53733c3a79e2/image.png)

1. A는 92~100에 대한 데이터 송신
2. A는 100~120에 대한 데이터 송신
3. B는 92~100에 대한 데이터를 송신받았기 때문에 ACK번호 100을 송신했으나, 모종의 이유로 데이터가 날라감
4. B는 100~120에 대한 데이터를 송신받았기 때문에 ACK번호 120을 송신
5. A는 92~100에 대한 ACK응답을 아직 받지 못하여 **Timeout**이 발생함
따라서 호스트 A는 92~100을 **재전송**
6. B는 이미 92~100에 대한 데이터를 받았으므로, 수신된 데이터를 버리고 다시 ACK 100을 송신함

![example2](https://velog.velcdn.com/images/calzone0404/post/18dddea2-5e7b-450f-be25-8d1d5a677ce9/image.png)
1. A는 92~100에 대한 데이터 송신
2. A는 100~120에 대한 데이터 송신
3. B는 92~100에 대한 데이터를 송신받았기 때문에 ACK번호 100을 송신했으나, 모종의 이유로 데이터가 날라감
4. B는 100~120에 대한 데이터를 송신받았기 때문에 ACK번호 120을 송신
5. ACK 120이 먼저 도착하여 SendBase = 120으로 갱신되고, 120 ~ 135에 대한 데이터 송신
6. 그런데, A는 92~100에 대한 ACK응답을 아직 받지 못하여 **Timeout**이 발생함

### TCP 빠른 재전송

- **Timeout주기가 때때로 김**
  긴 Timeout 주기는 Host간의 지연을 증가시킴.
  그러나, 송신자는 중복 ACK에 의한 Timeout이 일어나기 전에 패킷 손실 발견 가능
  <br>
- **중복 ACK**
  송신자가 이미 이전에 받은 ACK에 대한 재확인응답 ACK
  <br>
- **빠른 재전송**
  만약 TCP 송신자가 같은 데이터에 대해 3개의 중복 ACK를 수신했다면,
  ACK된 Segment Data의 다음 3개의 Segment가 분실되었음을 의미한다.
  
  ![fastretrans](https://velog.velcdn.com/images/calzone0404/post/34eea288-0400-417d-9e01-8dfbf4b9459b/image.png)
위 그림을 보면 seg 100 전송이 실패하게 되어, ACK 100이 중복으로 전송되는 모습을 확인할 수 있다. 
따라서 A는 Timeout이 일어나기 전에, 100이후로 데이터 전송이 실패했다고 판단하여 100~120에 대한 데이터를 다시 전송해준다.


## TCP 흐름 제어

>TCP는 송신자가 수신자의 버퍼를 Overflow시키는 것을 방지하기 위해 흐름 제어 서비스를 제공함

따라서 TCP 송신자는 네트워크에서 **혼잡 제어**에 의해 송신이 억제될 수 있다.

***
- 흐름 제어 과정
>TCP는 Receive Window라는 변수를 통해 흐름 제어를 수행한다

 Receive Window는 수신자가 가용한 버퍼 공간이 얼마나 되는지 알려주는데 사용한다.
 이때 TCP는 Full-Duplex 이므로 각 송신자는 별개의 Receive Window를 갖는다.
 
- 예시
  A가 B에게 대용량 파일을 전송한다고 가정하자.
  
  이때 B는 이 연결에 대해 수신 버퍼를 할당한다.
  할당된 수신 버퍼의 크기 : **RcvBuffer**
  
  B의 Process는 Buffer로부터 데이터를 읽으며 변수를 정의한다.
    1. LastByteRead : B의 Process에 의해 Buffer로부터 읽힌 Data Stream의 마지막 Byte 번호
      - LastByteRcvd : B에게 도착하여 Receive Buffer에 저장된 Data Stream의 마지막 Byte 번호
      - **RcvWindow(rwnd)** : 버퍼의 여유 공간
        = RcvBuffer $$-$$ $$($$LastByteRcvd $$-$$ LastByteRead$$)$$
      ![](https://velog.velcdn.com/images/calzone0404/post/474bc390-1af5-45b2-ba0b-2ab815c6ba76/image.png)


## TCP 연결 관리

>TCP는 어떤 데이터를 다른 프로세스에 보내기 전에, 두 프로세스가 서로 Handshake과정을 거쳐야 한다.

### 3-way Handshake - 연결 수립

![](https://velog.velcdn.com/images/calzone0404/post/9110e0ad-a620-48b1-befd-e9d5e4a02247/image.png)

1. Client TCP는 Servver에게 SYN을 송신한다.
   - 이때, SYN bit을 1로 설정한다 (SYN = 1)
   - Client는 최소 순서 번호(client_isn)을 임의로 선정해서 SYN을 보낼 때 같이 보낸다.
   <br>
2. SYN Segment가 Server로 도착하면
   - Server는 SYN 번호를 추출한다.
   - 연결에 필요한 TCP Buffer와 변수를 할당한다.
   - Client TCP에게 SYNACK를 송신한다
     - 이때, SYNACK bit은 1로 설정된다 (SYN = 1)
     - client_isn을 순서 번호로 받았으므로, client_isn + 1을 SYNACK와 함께 송신한다
   <br>  
3. Client가 연결 승인 Segment 수신시
   - Client는 연결에 필요한 TCP Buffer와 변수를 할당한다.
   - Client는 Server에게 SYNACK에 대한 확인 Segment를 송신한다
     - 클라이언트는 client_isn + 1을 수신하였으므로, 이 값을 받았다는 신호로 **ACK = client_isn + 1**과, 연결이 수립되었으므로 **SYN = 0** 신호를 보낸다


### 3-way Handshake - 연결 해제

>TCP 연결에 참여하는 두 Host중 하나가 연결을 끊을 수 있다.
연결이 끊어지면, 각각 할당했던 Buffer와 변수가 회수된다.

![](https://velog.velcdn.com/images/calzone0404/post/ab7fc695-fc43-402a-8497-e443d96b32b4/image.png)


1. Client가 Process 종료 명령을 내리고, 연결 해제를 위해 **FIN = 1** 신호를 보낸다.
2. Server가 FIN을 수신하면, Client에게 **ACK = 1**신호를 보낸다.
3. 또, Server는 연결 해제를 위해 Client에게 **FIN = 1** 신호를 보낸다.
4. Client는 FIN에 대해 **ACK = 1**를 보낸다.
5. 연결이 종료된다.


### TCP Handshake 상태 변이 - Client
![](https://velog.velcdn.com/images/calzone0404/post/3a2881da-ff06-451a-a9aa-21301117b923/image.png)


### TCP Handshake 상태 변이 - Server
![](https://velog.velcdn.com/images/calzone0404/post/72c3b5b0-61a9-4e16-98f6-7ceeb7e78871/image.png)

# 3.6 혼잡 제어의 원리
>Congestion : 너무 많은 송신자가 너무 높은 속도로 데이터를 보내려고 시도할 때 발생

## 혼잡의 원인과 비용 - 예시 1
- 두 Host A, B가 각각 출발지와 목적지 사이에서 단일 Router를 공유
- A와 B의 Process가 $$λ_{in}$$의 전송률로 데이터를 전송
- Output Link의 수용량 : $$R$$
- Router Buffer는 무한하다고 가정하자
![](https://velog.velcdn.com/images/calzone0404/post/8785dc16-82bc-4739-bea3-3592ebd8cd33/image.png)

***
1. 연결 당 처리량
![](https://velog.velcdn.com/images/calzone0404/post/297f5ea7-4d2b-46c3-9865-a67ab95bcf27/image.png)
0 ~ $$R/2$$ 사이에서의 전송 속도 : 수신자 측의 처리량은 송신자의 전송률과 같다.
$$R/2$$ 이상의 전송 속도 : 처리량은 $$R/2$$
즉, A와 B가 전송률을 아무리 높여도 각자 $$R/2$$보다 높은 처리량을 얻을 수 없음

***
2. 평균 지연
![](https://velog.velcdn.com/images/calzone0404/post/8fac3ee9-866e-4266-9da9-c2beca2520e5/image.png)
전송률이 $$R/2$$에 근접할 경우 : 평균 지연은 점점 커진다.
전송률이 $$R/2$$를 초과할 경우 : $$Infinity$$

## 혼잡의 원인과 비용 - 예시 2
- 송신자 A, B
- 유한한 크기의 Buffer를 가진 라우터 1개
- A와 B의 Process가 $$λ_{in}$$의 전송률로 데이터를 전송
- Output Link의 수용량 : $$R$$
![](https://velog.velcdn.com/images/calzone0404/post/7126c959-670e-43c2-a4d1-692f648c6a6a/image.png)

라우터가 버퍼의 양이 유한하므로 버퍼가 가득차게 된다면 도착하는 패킷은 버려진다.
따라서, 패킷이 버려지게 되면 송신자가 해당 패킷들을 재전송한다.
***
![](https://velog.velcdn.com/images/calzone0404/post/394d60cd-fc14-43e9-bf48-085557e8cfe6/image.png)

A. 어떠한 손실도 발생하지 않음
- 연결의 처리량 : $$λ_{in}$$
- 송신률은 $$R/2$$를 초과할 수 없음

B. 패킷이 손실되었다는 것을 알고 송신자가 재전송 하는 경우
- 제공된 부하 λ'in이 R/2일 경우 : 데이터의 전송률은 R/3
- 전송된 데이터의 R/2 중
  0.333R 바이트/초는 원래의 데이터
  초당 0.166R 바이트/초(평균)는 재전송 데이터
  
>송신자는 Buffer Overflow 때문에 버려진 패킷에 대해 다시 재전송을 수행한다.

C. 송신자에서 너무 일찍 타임아웃되어 패킷이 손실되지 않았지만, 큐에서 지연되고 있는 패킷을 재전송하는 경우

- 원래의 데이터 패킷과 재전송 패킷 둘 다 수신자에게 도착한다.
- 각 패킷이 라우터에 의해 두 번씩 전달된다고 가정했을 때, 제공된 부하가 R/2일 때의 처리량은 R/4

> 커다란 지연으로 인한 송신자의 불필요한 재전송은 라우터가 패킷의 불필요한 복사본들을 전송하는 데 Output Link의 대역폭을 사용하는 원인

## 혼잡 제어에 대한 접근법

### 종단 간 혼잡제어
> 네트워크 계층은 Congestion Control을 위해 Transport Layer에게 어떠한 직접적인 지원을 받지 않음

- TCP Segment 손실 및 증가하는 RTT 지연값을 네트워크 혼잡 발생의 신호로 간주함
- TCP는 그에 따라 Window size를 줄여버림

### 네트워크 지원 혼잡 제어
> 라우터들이 네트워크 안에서 Congestion과 관련된 Feedback을 송신자, 수신자 또는 Network 참여자 모두에게 받는다.

#### ATM ABR(Available Bite Rate) 혼잡제어
- 라우터는 자신의 Output Link에 제공할 수 있는 전송률을 송신자에게 명확히 알릴 수 있게 해준다.

- 만약 송신자의 path가 아직 여유롭다면, 이용가능한 대역폭 안에서 송신을 수행한다.

- 만약 송신자의 path가 혼잡하다면, 송신자는 최소 보장 속도로 송신하도록 제어됨

**RM (Resource Management)**
- 송신자가 보낸 데이터 Cell들을 분산하여 전송함
- RM Cell이 가지고 있는 bit들은 **Switch**에서 설정함

# 3.7 혼잡 제어
## 혼잡 제어
- 일단 전송률을 올리고, 패킷 Loss가 발생하기 전까지 사용 가능한 대역폭을 탐색한다.

- **Addictive Increase** : Congestion Window Size (CongWin)을 RTT마타 1MSS씩 loss가 발생하기 전까지 증가시킨다.

- **Multiplicative Decrease** : loss 발생 시 CongWin을 절반으로 줄인다.

### TCP 송신자가 자신의 송신률을 제한하는 방법
> 송신 측에서 동작하는 TCP 혼잡 제어 메커니즘은 추가적인 변수인 **혼잡 윈도(congestion window)**를 추적한다.

이때, 송신하는 쪽에서 ACK가 응답을 받지 못한 데이터의 양은 CongWin과 Receive Window(수신 윈도 = 버퍼의 여유 공간)의 최솟값을 초과하지 않을 것이다.

> LastByteSent - LastByteAcked ≤ min{cwnd, rwnd}

따라서 CongWin의 값을 조절하여 송신자는 링크에 데이터를 전송하는 속도를 조절할 수 있다.

### TCP 송신자가 혼잡을 감지하는 방법
- Loss 발생 = 3개의 중복된 ACK 신호를 받거나, Timeout이 된 경우
이때, 송신자는 Loss 발생 이후 CongWin을 줄여버린다.

## TCP Slow Start
1. TCP 연결이 시작되면, CongWin = 1MSS로 초기화 한다
이때, 초기 전송률은 다음과 같다. **MSS/RTT**

2. 처음 Loss event가 발생하기 전까지는 지수적으로 TCP 전송률을 늘려나간다
   - 즉, RTT마다 CongWin값을 2배씩 증가시킨다
   
> 처음 전송률은 느리지만, RTT가 지날수록, 지수적으로 빠르게 전송률이 증가하게 된다.

### 혼잡 회피 (Inferring Loss)

- 3개의 중복된 ACK가 전달되었다면, **CongWin**은 절반으로 줄고, 선형적으로 증가한다.

- Timeout이 발생하였다면, **CongWin**은 1MSS로 초기화 되고, 지수적으로 증가한다. 이후, 임계값 근처에서는 선형적으로 증가한다.
  - 정확히 언제 지수에서 선형으로 변환되는가?
  > A: CongWin값이 Timeout 이전에 절반으로 줄어들었을 때, 선형으로 변환된다.
  - 따라서 Loss 발생 시 임계값 선은 Loss 발생 직전 CongWin/2 값으로 설정된다.


## 정리

1. CongWin이 임계값보다 낮으면 송신자는 Slow Start 단계에서 Window 크기는 기하급수적으로 증가

2. CongWin이 임계값을 초과하면 송신자는 혼잡 회피 단계에 들어가고, Window 크기는 선형적으로 증가

3. 3중 중복 ACK가 발생하면 임계값이 CongWin의 절반 값으로 설정되고 CongWin이 임계값으로 설정

4. Timeout이 발생하면 임계값은 CongWin의 절반 값으로 설정되고, CongWin은 1 MSS로 설정

## TCP 공정성

각각 다른 종단 간의 경로를 갖지만, 모두 **R bps**의 전송률인 병목 링크(bottleneck link)를 지나는 **K개의 TCP 연결**을 생각해보자.

각 연결은 큰 파일을 전송하고 있고, 병목 링크를 통과하는 UDP 트래픽은 없다고 가정했을 때,
 
>각 연결의 평균 전송률이 **R/K**에 가깝다면 혼잡 제어는 메커니즘이 **공평**하다고 한다.
즉, 각 연결은 링크 대역폭을 동등하게 공유한다.

### 공평성과 UDP

UDP는 혼잡 제어를 갖고 있지 않다.
TCP의 관점에서 보면 UDP 상에서 수행되는 멀티미디어 애플리케이션은 공평하지 못하다.

즉, 다른 연결들과 협력하지도 않으며, 그들의 전송률을 적당하게 조절하지도 않는다.


TCP 혼잡 제어는 혼잡(손실) 증가에 대해 전송률을 감소시키므로,
그럴 필요가 없는 UDP 송신자들이 TCP 트래픽을 밀어낼 가능성이 있다.

> UDP 트래픽으로 인해 인터넷이 마비되는 것을 방지하는 인터넷을 위한 혼잡 제어 방식의 개발이 필요하다.

### 공평성과 병렬 TCP 연결

UDP 트래픽이 공평하게 행동하도록 강요하더라도,
TCP 기반 애플리케이션의 다중 병렬 연결의 사용을 막을 방법이 없기 때문에 공평성 문제는 여전히 완전하게 해결되지 않는다.

애플리케이션이 다중 병렬 연결을 사용할 때는 혼잡한 링크 대역폭의 더 많은 부분을 차지한다.
