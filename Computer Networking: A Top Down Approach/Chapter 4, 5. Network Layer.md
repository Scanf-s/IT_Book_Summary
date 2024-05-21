# 4.1 소개

## 네트워크 계층

- 전송계층의 **Segment**를 송신측에서 수신측으로 전송하는 역할
- 송신측은 Segment를 **Datagram** 또는 **Packet**으로 캡슐화
- 수신측은 Datagram을 수신하여, Segment로 변환 후 상위 계층인 전송 계층으로 전달
- 모든 호스트와 라우터는 네트워크 계층 프로토콜을 사용함
- 라우터는 자신을 지나가는 모든 IP Datagram의 헤더를 보고 어디로 보내는지 확인함

## 네트워크 계층의 주요 기능

>1. Forwarding: 패킷을 라우터의 입력에서 적절한 라우터의 출력으로 옮기는 작업

- 포워딩 테이블

  ![Forwarding Table](https://velog.velcdn.com/images/calzone0404/post/a58b6b31-c939-489c-981b-7b970b10ba1e/image.png)
  
  라우터는 도착하는 패킷 헤더의 필드값을 통해 포워딩 테이블의 내부 색인으로 사용하여 패킷을 전달한다. 포워딩 테이블 엔트리에 저장되어 있는 헤더의 값은 해당 패킷이 전달되어야 할 라우터의 외부 링크 인터페이스를 나타낸다.

>2. Routing: 패킷의 송신지에서 수신지까지 가는 경로를 결정함. 주로 라우팅 알고리즘에 의해 결정됨

- 라우팅 알고리즘은 패킷이 송신지에서 수신지로 가는 경로를 결정한다. 예를 들어, 호스트 H1에서 H2로 패킷이 흐르는 경로를 결정한다. 라우팅은 네트워크 계층의 제어 평면에서 구현된다.

## 데이터 평면과 제어 평면

- **데이터 평면**: 포워딩 작업을 수행하며, 패킷을 입력 링크에서 적절한 출력 링크로 이동시킨다. 포워딩은 일반적으로 하드웨어에서 매우 짧은 시간(몇 나노초) 내에 수행된다.
- **제어 평면**: 네트워크 전반에 걸친 경로를 결정하며, 패킷이 송신지에서 수신지로 가는 전체 경로를 설정한다. 라우팅 알고리즘은 제어 평면에서 실행되며, 이는 일반적으로 소프트웨어에서 더 긴 시간(몇 초) 내에 수행된다.

## 네트워크 서비스 모델

네트워크 계층이 제공할 수 있는 서비스는 다음과 같다:

- **보장된 전달**: 송신 호스트가 보낸 패킷이 결국 수신 호스트에 도착하도록 보장하는 서비스
- **지연 시간 보장된 전달**: 패킷의 전달을 보장할 뿐만 아니라, 지정된 호스트 간 지연 시간 내에 전달을 보장하는 서비스 (예: 100밀리초 이내)
- **순서 보장된 패킷 전달**: 패킷이 전송된 순서대로 수신 호스트에 도착하도록 보장하는 서비스
- **최소 대역폭 보장**: 송신 호스트와 수신 호스트 간의 특정 비트 전송률(예: 1 Mbps)을 보장하는 서비스. 송신 호스트가 지정된 비트 전송률 이하로 비트를 전송하면 모든 패킷이 결국 수신 호스트에 전달됨
- **보안**: 네트워크 계층에서 모든 데이터그램을 송신지에서 암호화하고 수신지에서 복호화하여 모든 전송 계층 세그먼트에 대해 기밀성을 제공하는 서비스

인터넷의 네트워크 계층은 **최선형 서비스(Best Effort)**를 제공한다. 최선형 서비스는 패킷이 전송된 순서대로 수신되거나, 최종적으로 전달될 것이라는 보장을 하지 않는다. 엔드 투 엔드 지연에 대한 보장도 없고, 최소 대역폭에 대한 보장도 없다.

    
# 4.2 Virtual Circuit과 Datagram Network

## Connection, Connection-less service

- Datagram을 전송하는 네트워크는 비연결지향형 서비스를 제공한다 (UDP)
- 가상 회로 (Virtual Circuit) 네트워크는 연결지향형 서비스를 제공한다 (TCP)
- 전송계층이 하는 역할과 비슷하다.

## Virtual Circuit
- 네트워크 계층의 연결형 서비스를 제공한다.

1. 데이터를 보내기 전 반드시 Virtual Circuit을 설립한다.

2. 각 Packet은 각자의 Virtual Circuit을 가진다.
   - 따라서, 각 Packet은 각자의 VC Identifier를 통해 전달된다.
   - Path 내에 존재하는 Packet에 대해, **VC Identifier (VC Number)는 반드시 각 Packet에 대해 Unique하게 할당되어야 한다.**
   
3. 송신지에서 수신지로 가는 길에 존재하는 모든 Router는 자신으로 전달되는 각 연결에 대해 State를 유지함

4. Link, Router 자원이 VC에 의해 할당될 수 있음

### Virtual Circuit 구성요소

1. 송신지에서 수신지까지 Data가 전달되는 Path
2. Path에 연결되어 있는 모든 Link에 대한 VC 번호
3. 각 Router의 Forwarding Table Entry (Table에 Mapping하기 위한 Key)

![](https://velog.velcdn.com/images/calzone0404/post/4b7ec4a8-1336-44c8-8fd8-739bcd56f986/image.png)

- 예를 들어
  $[1, 12, 3, 22]$
  >- 1번 Interface에서 들어온 12번 VC Num을 가진 Packet은
  >- 3번 Interface로 나가서, 22번 VC Num을 부여받는다.

### VC Signaling 프로토콜

- VC 연결, 해제, 정보 유지 프로토콜
- 현재 사용하지 않는다.

## Datagram Networks

- Connection 과정이 존재하지 않는다.
- Packet의 목적지 주소를 통해서 Forwarding한다.
- Packet을 독립적으로 전송한다.
   - 즉, 각 Packet은 각각 독립적인 Path(서로 다른 경로)를 갖는다.
   - 이렇게 전송하면, 목적지에서는 Packet의 순서가 달라질 수 있다.

   ![](https://velog.velcdn.com/images/calzone0404/post/7634eda1-8056-4bec-aff8-a685254d3c54/image.png)

### Datagram Forwarding Table

![](https://velog.velcdn.com/images/calzone0404/post/78c6681a-c694-455b-b431-34b3ea90a144/image.png)

- DFT는 목적지 주소의 범위를 통해 Packet을 다음 Link로 전달한다.

예를 들어, 위 사진을 보면

- 하위 16bit에 대해서 00010000 00000000 ~ 00010111 11111111 사이에 목적지 주소가 존재하면 Link 0으로 전달한다.

> Longest Prefix Matching (가장 긴 주소 접두사 Matching)
- 목적지 주소를 DFT에서 찾을 때, 앞에서부터 주소와 일치하는 가장 긴 주소 접두사를 사용하는 방법

예를 들어,

![](https://velog.velcdn.com/images/calzone0404/post/223f73ae-dff4-476a-b1a4-e0d48b6e0f82/image.png)

![](https://velog.velcdn.com/images/calzone0404/post/f58308ec-93dc-4b39-beba-ae5023f4fd8c/image.png)

위 테이블에서 위 두 주소를 Matching한다면,

1번 주소는 0번 Link로 Matching되고,
2번 주소는 1번 Link로 Matching된다.

## Datagram 네트워크와 VC 네트워크 차이점

>1. **Internet (Datagram)**
- 데이터가 Host들 사이에서 교환될 때 유연한 통신 서비스를 제공한다.
- Host가 똑똑하기 때문에, Host 자체적으로 전송제어, 에러복구가 가능하다.
- Link 종류가 다양하기 때문에 서비스를 단일화하기 어렵다.
2. **ATM (VC)**
- 전화 네트워크에서 진화한 형태
- 유연한 통신 서비스를 제공하지 않는다.
   - 즉, 전송 타이밍이 정해져있고, 의존성이 요구된다.
- Host가 멍청하다.

## 네트워크 계층 평면 구조
- 크게 3개의 계층 평면으로 나뉜다.

### 1. Management plane: 네트워크 인프라를 관리하는 사용자/운영자/도구

### 2. Control plane : 도달 가능성 상태를 교환하기 위한 네트워크 엔티티 간의 시그널 신호 부여
   - Datagram이 송신지에서 수신지까지 전달될 때, 어떤 방법으로 Router를 거쳐갈지 결정한다.

#### Per-router control plane
- 라우팅 알고리즘이 각각의 모든 라우터에서 실행되며, 라우터는 포워딩 기능과 라우팅 기능을 모두 갖고 서로 상호작용한다.

   ![](https://velog.velcdn.com/images/calzone0404/post/b40ce25e-804e-4b6c-8337-6a37ba477fda/image.png)

#### Logically centralized control plane
- 각각의 Router가 Local Control Agents(CAs)를 통해 상호작용한다

   ![](https://velog.velcdn.com/images/calzone0404/post/4767900a-a5d0-412e-a07d-88c139b5ca35/image.png)

### 3. Data plane : 애플리케이션 데이터 패킷의 실제 이동
   - Input Link에서 Output Link로 Datagram을 전달한다.

# 4.3 라우터 내부 구조

## 1. Input Port

![](https://velog.velcdn.com/images/calzone0404/post/75a7bc10-cbf7-4114-8d50-4e6b354d574a/image.png)

- 입력 포트의 맨 왼쪽은, bit데이터가 들어오는 물리 계층 기능을 수행한다.
- **입력 포트의 가장 오른쪽** 박스는 Forwarding Table을 참조하여, 도착한 Packet이 **Switching 과정**을 통해 라우터 Output Port를 결정하게 된다.

### Switching 종류
- Input buffer로부터 들어온 Packet을 적절한 Output buffer로 전달해야 한다.
- Switching rate: Input에서 Output으로 전달할 수 있는 비율

#### 1. Memory를 통한 Switching
패킷 전달 과정
1. Packet이 도착하면, Input은 CPU에 신호를 보내서, Packet을 Memory에 복사한다.
2. CPU는 헤더에서 목적지 주소를 추출한다.
3. Forwarding Table에서 적절한 Output port를 찾는다.
4. Packet을 Output port에 복사한다.

![](https://velog.velcdn.com/images/calzone0404/post/425440bf-d879-4ded-aed5-3c8f201c4f47/image.png)

#### 2. Bus를 통한 Switching

- Input port로부터 전달된 Datagram은 CPU 개입 없이 공유 Bus를 통해 직접 Output port로 전달된다.

> Bus contention: Switching 속도는 Bus의 대역폭에 의해 제한된다.

![](https://velog.velcdn.com/images/calzone0404/post/b4e9d683-0d11-43d0-a593-56b8f441fa0f/image.png)

#### 3. Interconnection Network를 통한 Switching

- Bus contention을 극복하기 위한 해결책
- 각 수직 Bus는 교차점에서 각 수평 Bus와 교차하며, Switch controller에 의해 언제든지 교차점을 On/Off할 수 있다.
- 여러 패킷을 동시에 전달할 수 있게 된다.
- 그러나, 두 개의 서로 다른 Input port에서 나오는 패킷이 동일한 Output port로 전달되는 경우, 한 번에 하나의 Packet만 특정 Bus에서 전송될 수 있으므로 대기해야 한다.

![](https://velog.velcdn.com/images/calzone0404/post/d71f0132-df60-4778-a7b1-e2b18df071db/image.png)

### Input port Queuing
- 모든 패킷을 전송하기에는 Switching 구조가 충분한 속도를 가지지 못한다면, 대기가 발생하게 된다.

예를 들어,

![](https://velog.velcdn.com/images/calzone0404/post/aafaf769-b380-40ce-ad20-c33ba070f1bf/image.png)

> 왼쪽 상단 큐의 앞쪽에서 먼저 패킷을 전송한다고 가정

왼쪽 하단 큐의 가장 앞쪽의 패킷은 출력 포트가 같으므로 대기해야 함. 그런데 그 뒤 두 번째 패킷은 출력 포트가 다르지만, 앞 패킷이 전달되지 않고 있으므로 대기해야 함.

이러한 현상을 **HOL (Head-of-the-Line blocking)** 이라고 한다.

## 2. Output Port

![](https://velog.velcdn.com/images/calzone0404/post/1c0ec69c-748b-40eb-b5d7-0fa5b58daed6/image.png)

출력 포트는 Buffer에 저장된 패킷을 가져와서 Link로 전송하는 역할을 수행한다.

만약, Link로 전송하는 전송률보다 Input으로부터 전달되는 데이터 전송률이 더 크다면, Buffering이 발생한다.

> 따라서, Queue에 들어있는 Datagram들을 어떤 순서로 전송해야 할지 결정하는 규칙이 필요하게 된다.

### Output port Queuing

- Scheduling: Link로 전달할 다음 순서의 Packet을 결정하는 것

#### FIFO Scheduling

> 가장 먼저 Queue에 들어온 Packet부터 Output Link로 전달한다.

![](https://velog.velcdn.com/images/calzone0404/post/fc8819b4-ec91-4a10-afd4-926c4e66246a/image.png)

#### Discard 정책
> 만약 꽉 차 있는 Queue에 Packet이 들어온다면, 어떤 Packet을 버려야 할까?

1. Tail drop: 도착하는 패킷을 버린다.
2. Random drop: 아무거나 버린다.
3. Priority: 우선순위를 기준으로 버린다.

#### Priority Scheduling
- 높은 우선순위를 가진 Packet부터 전달한다.
- 우선순위 Class가 높은 Packet을 전달한다.

![](https://velog.velcdn.com/images/calzone0404/post/181d595c-4e36-46fa-b4f3-d55550ff0789/image.png)

#### Round Robin Scheduling
- 여러 개의 Class가 존재하며, 각 Class를 번갈아가며 Output Link로 전달한다.

![](https://velog.velcdn.com/images/calzone0404/post/3f6063d2-37b1-4a08-810e-cae2ed814376/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/3e58305b-10eb-49ba-aa4a-ac7130be0a4b/image.png)

#### Weighted Fair Queuing (WFQ)
- Round Robin 방식의 일반적 형태이다.
- 각 Class는 각 Cycle마다 Service의 개수에 따른 가중치가 존재한다.

![](https://velog.velcdn.com/images/calzone0404/post/c20b48ef-7731-45e0-9046-d4302aaa68e8/image.png)

# 4.4 Internet Protocol: IP

## Datagram 형태

![](https://velog.velcdn.com/images/calzone0404/post/9828a513-3d28-49ab-ad18-00c5a48f2c25/image.png)

- 네트워크 링크는 MTU(최대 전송 크기)를 갖는다. 링크 종류에 따라 MTU가 달라진다.

## Fragmentation & Reassembly
> 크기가 큰 IP datagram은 송신지의 network 상에서 fragmentation 되어 전달되고, 수신지에서 reassemble 된다. 이 때, IP header bit를 통해 각 순서를 결정하게 된다.

![](https://velog.velcdn.com/images/calzone0404/post/8dd411a7-0bbb-400b-9380-bd6d93ef78fe/image.png)

## IP Address

> IP Address: 32bit의 네트워크 계층 host identifier
> Interface: host와 router 사이에 연결된 물리적 link

### IP Address의 Class

![](https://velog.velcdn.com/images/calzone0404/post/040b2de2-e1a1-44ae-aaf8-a3e43aed2c07/image.png)
- Class A
   - Network 주소: 상위 8bit
   - Host 주소: 하위 24bit

- Class B
   - Network 주소: 상위 16bit
   - Host 주소: 하위 16bit
   
- Class C
   - Network 주소: 상위 24bit
   - Host 주소: 하위 8bit
   
![](https://velog.velcdn.com/images/calzone0404/post/fb23379b-8449-41b2-96ae-e3d4e06b3b3b/image.png)

## Subnet
- IP 주소는 Subnet 부분, Host 부분으로 나뉜다.

> Subnet: 전체 네트워크에서 ***각각의 부분 네트워크를 구분***하기 위해 사용
> Host: 각 Subnet 내에서 Host를 구분하기 위해 사용

> Subnet은 반드시 상위 bit부터 계속 1이 연속되어야 하며, 0이 등장하면 그 이후로는 계속 0으로 채워져야 한다.

> Subnet을 쪼개버리면, 각 Subnet은 서로 같은 네트워크가 아니게 되어 Router를 통해 통신해야 한다.

- Subnet에 존재하는 Host는 Router 없이 서로 물리적으로 연결되어 있다.

![](https://velog.velcdn.com/images/calzone0404/post/4c40a6b0-730e-4b1f-a7c0-ea9424cf2f3c/image.png)

![](https://velog.velcdn.com/images/calzone0404/post/3ff50562-25ac-4893-84a1-c532ab41c282/image.png)

위 그림에서, C class 네트워크로 가정하고,

IP: 192.168.1.123을 이진수로 변환하면
11000000.10101000.00000001.01111011

Subnet: 255.255.255.192를 이진수로 변환하면
11111111.11111111.11111111.11000000

이때, 네트워크는 C class에서 4개의 subnet으로 쪼개져 있으므로 하위 8bit가 Host Network임을 알 수 있다.

그런데, Subnet Mask를 보면 하위 8bit 중, 상위 2bit가 1로 채워져 있는데, 이는 Subnet을 또 4개의 서로 다른 Subnet으로 쪼갰다는 의미이다.

> 따라서 Subnet은 다음과 같이 나누어져 있다.
- 192.168.1.0 ~ 63
  - Subnet: 11111111.11111111.11111111.00[xxxxxx]
- 192.168.1.64 ~ 127
  - Subnet: 11111111.11111111.11111111.01[xxxxxx]
- 192.168.1.128 ~ 191
  - Subnet: 11111111.11111111.11111111.10[xxxxxx]
- 192.168.1.192 ~ 255
  - Subnet: 11111111.11111111.11111111.11[xxxxxx]

다시 위에 했던 IP에 대해,
IP: 192.168.1.123
11000000.10101000.00000001.01111011

위 IP의 Network ID는
11000000.10101000.00000001.01[000000]
즉, 192.168.1.64

첫번째 Host Address는
11000000.10101000.00000001.01[000001]
즉, 192.168.1.65

마지막 Host Address는
11000000.10101000.00000001.01[111110]
즉, 192.168.1.126

Broadcast Address는
11000000.10101000.00000001.01[111111]
즉, 192.168.1.127

![](https://velog.velcdn.com/images/calzone0404/post/5d5607b8-403a-4203-97d4-e5a4319c6845/image.png)

## IP 주소 할당 방식

### CIDR
> CIDR: Classless InterDomain Routing

- a.b.c.d/x에서 x는 subnet address의 상위 $x$ bit를 의미한다.

아래 이미지를 예시로 보면, 상위 23bit가 subnet part이다.

![](https://velog.velcdn.com/images/calzone0404/post/c93148c9-01bc-41ca-b79e-10c01049ffee/image.png)

### DHCP
> Dynamic Host Configuration Protocol

- 호스트가 동적으로 IP Address, Subnet Mask, Router Interface Address, Local DNS Address를 네트워크 서버로부터 할당받을 수 있게 해주는 프로토콜

#### 동작과정

![](https://velog.velcdn.com/images/calzone0404/post/1794f5e8-1d59-4cc2-994c-839966da04ad/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/53a6c59f-6d2c-419c-a8fe-b63f817154f2/image.png)

1. DHCP 서버 탐색
   - IP를 할당받지 못했으므로, 0.0.0.0
   - 자신의 네트워크에서 전체 Device로 포트 67번을 향해 DHCPDISCOVER 패킷 전송
   - DHCPOFFER 패킷을 기다림

2. DHCP 서버 제공
   - DHCP 서버가 DHCPDISCOVER 패킷을 받았다면, DHCPOFFER 패킷을 전송
   - 클라이언트 주소를 모르니까 Broadcast로 전송한다.

3. DHCP 요청
   - 나에게 IP Address, Subnet Mask, Router Interface Address, Local DNS Address를 할당해달라고 서버에게 요청
   - DHCP ACK를 기다린다

4. DHCP ACK
   - DHCP 요청 패킷을 받았다면, IP Address, Subnet Mask, Router Interface Address, Local DNS Address를 담아서 Broadcast로 전송
   - DHCP ACK를 클라이언트가 수신하면, 연결이 종료되고 임대 기간 동안 할당된 IP 주소를 사용할 수 있다.

- Subnet mask는 어떻게 할당될까?
> ISP의 address space 중 하나를 할당해준다. 즉, ISP 네트워크에서, 8개로 쪼개진 subnet 중 하나를 할당해준다.

![](https://velog.velcdn.com/images/calzone0404/post/765677c2-083f-4a38-8cb5-68fa3c51bf4a/image.png)

#### Hierarchical addressing
   1. Routing되는 같은 네트워크 대역에 대한 정보를 효율적으로 수집할 수 있다.
   2. 특정 네트워크 대역에 대해 더욱 자세한 정보를 가지게 된다.

### NAT (Network Address Translation)

![](https://velog.velcdn.com/images/calzone0404/post/301137c5-09f2-457c-b72b-41649ea6e934/image.png)

- Local 네트워크가 10.0.0.0/24 네트워크 대역일 때, 이 지역의 Source, Destination 주소는 10.0.0.0/24 주소 안에서 결정된다.
- Local 네트워크에서 나가는 모든 Datagram은 단일 IP 주소인 138.76.29.7을 가지게 된다.

NAT를 사용하게 되면, 하나의 IP 주소만으로 Local Network의 모든 Host에 대한 Datagram을 처리할 수 있다. 따라서 Local Network는 주소를 외부 네트워크에 알리지 않고 자유롭게 사용 가능하다. 외부 Network는 내부 Network에 대한 주소를 알지 못하므로 보안성이 확보된다.

#### NAT 동작과정

> 외부 네트워크로 나가는 Datagram에 대한 정보
- 모든 외부 네트워크로 나가는 Datagram에 대해서,
  1. (송신자 내부 IP 주소, 송신자 포트 번호)
  2. (변환된 NAT IP 주소, 새로운 포트 번호)


  이 두 정보를 Mapping하기 위해 Table이 존재한다.

> Mapping Table
- 변환 정보를 기억하는 Table이 존재한다.

> 내부 네트워크로 들어가는 Datagram에 대해서
- Mapping Table을 통해 내부 네트워크 정보를 획득하여 내부 네트워크로 들어간다.

![](https://velog.velcdn.com/images/calzone0404/post/09132b9-16ac-4bac-8e91-bb60d47f5ca9/image.png)

#### NAT의 문제점

![](https://velog.velcdn.com/images/calzone0404/post/206d53bc-f25b-4750-accb-650142dbe1bb/image.png)
- 외부에 있는 Client 138.76.29.7이 NAT router 뒤에 존재하는 서버 10.0.0.1에 연결하려면 어떻게 해야 할까?

##### Solution 1: 정적 방법
> 외부로부터 특정 포트 번호에 대한 연결 요청이 들어오면, Server로 Forwarding하는 방법

- (138.76.29.7, port: 2500)에 대한 요청이 외부로부터 들어왔을 때, 2500에 대한 요청을 항상 10.0.0.1이 받도록 Forwarding한다.

##### Solution 2: Universal Plug and Play (UPnP) Internet Gateway Device Protocol (IGD)
> 외부 IP 주소를 기억해두고, 특정 시간 동안 port 번호를 mapping 한다.

##### Solution 3: Relaying
1. Relay를 위해 NAT 내부 Client가 외부 Relay 서버에 연결을 수립한다.
2. 외부 Client가 Relay 서버에 연결한다.
3. 외부 Client와 내부 Client가 연결을 수립하면 Relay Server가 Packet을 전송할 수 있도록 정보를 제공한다.

## ICMP
- 네트워크 계층의 정보를 Host와 Router가 서로 의사소통을 하기 위해 사용한다.
  - 에러 정보 (Unreachable host or network or ...)
  - Echo Request/Reply 정보

    ![](https://velog.velcdn.com/images/calzone0404/post/39f67572-3585-4891-905d-c9f9072c3e5e/image.png)
    - (0, 0) & (8, 0): 서로 살아있는지 확인하는 메시지
    - (3, 0 ~ 7): Datagram이 제대로 전달되지 않은 경우
    - (9, 0) & (10, 0): 라우팅 프로토콜을 위해 사용되는 메시지
    - (11, 0) & (12, 0): TTL 종료 메시지, IP 헤더 오류 메시지

- ICMP 프로토콜은 IP의 상위 계층이다. 따라서, IP Datagram에 캡슐화되어 전달된다.

### Traceroute 과정
#### 1. Source에서 Destination에 UDP Segment를 3개씩 전송
이때, 첫 번째 Segment의 TTL = 1을 갖는다.
첫 번째 Router에 도착하면, TTL = 0이 되어 파기된다.
첫 번째 Router는 (11, 0) TTL expired ICMP를 Source로 전송한다.

두 번째 Segment는 TTL = 2를 갖는다.
두 번째 Router에 도착하면, TTL = 0이 되어 파기된다.
따라서 Router는 (11, 0) TTL expired ICMP를 Source로 전송한다.

...

n 번째 Segment는 TTL = n을 갖는다
Hop(n) Router에서 TTL expired

이 과정을 통해 Source는 ICMP 메시지가 도착할 때마다 RTT를 계산하게 된다.

#### 2. (11, 0)을 받지 않는 경우
UDP segment가 갑자기 host로 전달된 경우에 해당한다. 즉, TTL이 굉장히 커져서 결국 목적지로 도달하게 된 경우에 해당한다. 이때, 목적지는 UDP segment가 어떤 Port로 가는지 모르므로 (3, 3): Destination Port unreachable ICMP 메시지를 전송한다.

#### 3. (3, 3)을 받은 경우
Source가 (3, 3) ICMP 메시지를 받는 순간, Destination에 Segment가 도착했음을 알게 된다.

## IPv6
> IPv4가 고갈되는 것을 미연에 대처하고자 만든 새로운 주소 체계

### IPv6 Datagram Format

![](https://velog.velcdn.com/images/calzone0404/post/9975c850-378d-4794-a94d-5f8ac4f1957b/image.png)

### IPv4를 IPv6로 변환하는 방법: Tunneling

![](https://velog.velcdn.com/images/calzone0404/post/c1b0d1a4-7063-402f-bcbd-dba63330f57c/image.png)

두 IPv6 Router B, E가 IPv6 Datagram을 이용하여 통신한다고 가정하자. 실제로, B와 E는 IPv4 라우터를 통해 연결되어 있다. > 이렇게 IPv6 Node 사이에 연결되어 있는 IPv4 라우터들을 ***Tunnel***이라고 한다.

#### 변환과정
1. A는 터널의 송신 측에 있는 IPv6 노드 F에게 Datagram을 전달하려고 한다.
2. Datagram을 전달받은 B는 IPv6 Datagram을 IPv4 Datagram의 Data 필드에 집어넣는다.
3. IPv4의 Datagram에 목적지 주소를 터널의 수신 측 IPv6 노드의 주소(E)를 적어서 Tunnel에 있는 IPv4 Router로 전달한다.
4. Tunnel의 라우터들은 일반적으로 IPv4 Datagram을 처리한다.
5. 터널 수신 측에 있는 IPv6 라우터 E는 IPv4 Datagram을 받아서 IPv6 Datagram인지 결정한다.
6. IPv6 데이터그램을 확인하고, 목적지 F로 전달한다.

## 일반화된 Forwarding & SDN (소프트웨어 정의 네트워크)
- 각 라우터에는 논리적으로 중앙 집중화된 라우팅 컨트롤러에 의해 계산 및 배포되는 플로우 테이블이 포함되어 있음.

![](https://velog.velcdn.com/images/calzone0404/post/9c3f5b74-017a-4f42-b54e-0ef12346b20a/image.png)

Based on the provided contents of Chapter 4.5 and additional details from the document, here is the updated and expanded section. 

---

# 4.5 Routing Algorithms

## Routing 프로토콜
- 송신지에서 수신지까지 Router를 거쳐 갈 수 있는 Path 중에서, 가장 효율적이고 좋은 Path를 찾는 프로토콜
(최소비용, 최대속도, 혼잡상태가 아닌)

## Routing Algorithm 분류 1

### 1. 중앙 집중형
>네트워크 전체에 대한 완전한 정보를 가지고 출발지와 목적지 사이의 최소 비용 경로를 계산하는 것

Link state algorithm : 알고리즘이 네트워크 전체 상태 정보를 갖는 알고리즘

### 2. 분산형
>각 노드는 자신에게 직접 연결된 링크에 대한 비용 정보만을 가지고 시작함

분산형 라우팅 알고리즘은, 반복된 계산과 이웃 노드와의 정보 교환을 통해 노드는 점차적으로 목적지 또는 목적지 집합까지의 최소 비용 경로를 계산한다.

Distance vector algorithm : 각 노드가 네트워크 내 다른 모드까지의 비용의 추정값을 벡터 형태로 유지함

## Routing Algorithm 분류 2
### 1. Static routing algorithm

사람이 직접 링크 비용을 수정하는 경우와 같이, 종종 사람이 개입하는 상황이 존재하는 경우 때문에 정적 라우팅 알고리즘에서 경로는 아주 느리게 변함
### 2. Dynamic routing algorithm

네트워크 트래픽 부하나, 토폴로지 변화에 따라 라우팅 경로를 바꿀 수 있음.

주기적으로, 또는 비용의 변경에 직접적으로 응답하는 방식으로 작동함
- 네트워크 변화에 빠르게 대응 가능하지만, **Graph Cycle이 존재하거나, 경로 진동(노드가 강연결되어있는 경우)** 같은 문제에 취약함

## Link State Routing Algorithm
>네트워크 토폴로지와 모든 링크 비용이 이미 알려져 있어서 Link state 알고리즘의 입력값으로 사용될 수 있다.

각 노드가 자신과 직접 연결된 링크의 식별자와, 비용 정보를 담은 ***링크 상태 패킷***을 네트워크 상의 다른 모든 노드로 ***Broadcast***하여 알릴 수 있다.


### 1. Dijkstra 알고리즘
- D(v) : 알고리즘의 현재 반복 시점에서 출발지 노드부터 목적지 v까지의 최소 비용 경로의 비용

- p(v) : 출발지에서 v까지의 현재 최소 비용 경로에서 v의 직전 노드

- N’ : 노드의 집합

- $(D(v), p(w))$ = $(weight, 직전 이어진 노드)$

**알고리즘 단계**:
1. 초기화 단계: 시작 노드를 설정하고, 시작 노드에서 각 노드까지의 초기 비용을 설정합니다.
2. 반복 단계: 가장 적은 비용의 노드를 선택하고, 이웃 노드의 경로 비용을 갱신합니다.

> 시간복잡도 : $O(n^2)$

![](https://velog.velcdn.com/images/calzone0404/post/dc832ff3-3db4-447f-bb26-f666769c450d/image.png)

![](https://velog.velcdn.com/images/calzone0404/post/b4f17436-9444-49ee-ae4e-93c5e0b43a15/image.png)

## Distance Vector Routing Algorithm

### 1. Bellman-Ford 알고리즘
노드 x부터 y까지 최소 비용 경로의 비용은 다음과 같이 나타낼 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/668b0b33-010e-48ba-854f-34dfce412c51/image.png)

$min_v$ : x에서 y까지 가는데 드는 최소 비용
$c(x, v)$ : x에서 v로 가는데 드는 최소 비용
$d_v(y)$ : v에서 y까지 가는데 드는 최소 비용

쉽게 생각해서, 노드의 개수가 $n$개일 때, 시작점에서 1번만 진행하는 경우, 2번만 진행하는 경우, $(n - 1)$번만 진행하는 경우에 대해서 계속 가중치를 갱신해주면 된다.

![](https://velog.velcdn.com/images/calzone0404/post/9a0a8840-3a4b-4cfc-887b-bbcedb10be37/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/3f185399-6a2c-4555-b789-89984686e124/image.png)

### Distance Vector Routing Algorithm 특징
- ***Iterative*** : 일정 시간마다, 각 노드는 각자 가지고 있는 Distance Vector 추정치를 각자 인접한 node에 전송함

- ***Asynchronous*** : 각 노드는 자신의 DV가 변경되었을때만 변경 정보를 이웃에게 전송함

- 어떤 노드 x가 이웃으로부터 새로운 DV 정보를 받았다면, Bellman-Ford 알고리즘을 통해 자신의 DV를 갱신함. 
(위 예시를 보면 2번째 열, 3번째 열에서 각자 이웃의 정보를 통해 DV테이블을 갱신하는 것을 볼 수 있음)

### 정리
1. 자신과 이어져있는 링크에 대한 Cost변화가 있거나, 이웃으로부터 DV정보가 날라올때까지 대기
2. DV 추정치를 갱신
3. 자신의 DV가 갱신되었다면, 인접한 이웃 노드에 정보 전송

### Distance Vector Routing Algorithm의 좋은점

아래는 y에서 x로의 링크 비용이 4에서 1로 변한 상황을 나타낸 것이다.

![](https://velog.velcdn.com/images/calzone0404/post/2820e41c-8f66-4f68-87bc-f9b05888003e/image.png)

이 상황에서의 DV 알고리즘은 다음과 같은 일련의 사건을 발생시킨다.

- 시각 t0 : y가 링크 비용의 변화를 감지하고, 자신의 거리 벡터를 갱신한 후 이 변경값을 이웃에게 알린다.

- 시각 t1 : z는 y로부터 갱신 정보를 받고 자신의 테이블을 갱신한다. 
이때, z는 x까지의 새로운 최소 비용을 계산한다. 이웃에게 자신의 새로운 거리 벡터를 전송한다.

- 시각 t2 : y는 z로부터 갱신 정보를 받고 자신의 테이블을 갱신한다. 
이때, y의 최소 비용은 변화가 없으므로 y는 z에게 아무런 메시지를 보내지 않는다. 이에 알고리즘은 정지 상태가 된다.

### Distance Vector Routing Algorithm의 문제점

아래는 y에서 x로의 링크 비용이 4에서 60로 변한 상황을 나타낸 것이다.

![](https://velog.velcdn.com/images/calzone0404/post/2e44220c-8fb9-44da-a6f9-c4d70daa11e0/image.png)

이 상황에서의 DV 알고리즘은 다음과 같은 일련의 사건을 발생시킨다.

- 시각 t0 : y가 링크 비용 변화를 감지하고 노드 x까지 다음의 비용을 갖는 새로운 최소 비용 경로를 계산한다.
![](https://velog.velcdn.com/images/calzone0404/post/e2c85d76-8ec3-4a66-ace6-f824356c38ea/image.png)
이때 우리는 네트워크 전체를 한눈에 볼 수 있기 때문에 z를 경유하는 이 새로운 비용이 잘못되었다는 사실을 알 수 있지만,
노드 y의 입장에서는 아니다.

- 시각 t1 : x로 가기 위해 y는 z로 경로 설정을 하고, z는 y로 경로 설정을 하는 라우팅 루프(routing loop)가 발생한다.
- t1에 x를 목적지로 하는 패킷이 y나 z에 도착하면 포워딩 테이블이 변할 때까지 이 두 노드 사이에서 왔다 갔다 순환할 것이다.
- 노드 y는 x까지의 새로운 최소 비용을 계산했으므로 z에게 새로운 거리 벡터를 알린다.
- 시각 t2 : z는 y로부터 갱신 정보를 받고 새로운 최소 비용을 계산한다.

> **$D.z(x) = min{50+0, 1+6} = 7$**

- x까지의 z의 최소 비용이 증가했으므로, 새로운 거리 벡터를 y에 알린다.
- 시각 t3 : y는 z로부터 새로운 거리 벡터를 수신하고 새로운 최소 비용을 계산한다.

> **$Dy(x) = min{60+0, 1+7} = 8$**

- x까지의 y의 최소 비용이 증가했으므로, 새로운 거리 벡터를 z에 알린다.

이렇게 계속 반복되는 문제를 무한 계수 문제(count-to-infinity)라고 한다.

#### 포이즌 리버스
> 참고 블로그 : https://code-lab1.tistory.com/35
> 위에서 발생한 무한 루프 문제를 해결하기 위해 포이즌 리버스 방법을 사용한다.

즉, 만약 z가 y를 통해 목적지 x로 가는 경로 설정을 했다면, z는 y에게 x까지의 거리가 무한대라고 알린다.
z는 y를 통과해서 x로 가는 동안은 이러한 거짓말을 계속한다.

이에 y는 z에서 x로 가는 **경로가 없다**고 믿으므로, z가 계속해서 y를 통해 x로 가는 경로를 사용하는 동안은 y는 z를 통해 x로 가는 경로를 **시도하지 않을 것**이다.

하지만 **포이즌 리버스는 모든 무한 계수 문제를 해결할 수는 없다.** 
단순히 직접 이웃한 2개의 노드가 아닌, 3개 이상의 노드를 포함한 루프는 포이즌 리버스로는 감지할 수 없다.

## LS와 DV 알고리즘 비교
### 경로 계산 방법
- LS
   - 전체 정보를 필요로 함.
   - 각 노드는 다른 모든 노드와 Broadcast를 통해 통신
   - 오직 자신에게 직접 연결된 링크의 비용만 알림
   
- DV
   - 각 노드는 직접 연결된 이웃과만 통신함
   - 자신으로부터 내트워크 내 자신이 알고있는 모든 노드로의 최소 비용 추정값을 이웃 노드로 전송
   
### 메세지 복잡성
- LS
   - 각 노드는 네트워크 내 각 링크의 비용을 모두 알아야 하므로 이를 알아내는데 드는 시간
   > n : 노드의 개수, E : 간선의 개수, 시간복잡도 : $O(nE)$
   
- DV
   - 시간 복잡도는 많은 요소에 따라 달라지므로 확정할 수 없음
   - 이웃과만 데이터 교환
   
### 견고성 : 만약 라우터가 오작동한다면?
- LS
   - 연결된 링크에 대해 잘못된 비용 정보를 Broadcast 가능
   - Broadcast를 통해 모든 노드에 전달되므로 받은 패킷을 자신의 테이블에 적용
   
 따라서 LS는 어느 정도 견고성을 제공한다
   
- DV
   - 잘못된 최소 비용 경로를 일부 혹은 모든 목적지에 알릴 수 있음
   - 즉, 테이블이 갱신되면 인접 노드에 전달되고, 또 다시 전달되므로 한 노드의 잘못된 계산은 전체로 확산되어 잘못된 포워딩 테이블이 모든 노드에 저장될수도 있게 됨
 
 따라서 DV는 견고성을 제공하지 않는 경우가 존재함
 
## Hierarchical Routing

> 실제 세계는 Destination이 무수히 많기 때문에 모든 정보를 Forwarding Table에 저장하는것은 불가능함
 
> AS : autonomous system (자율 시스템)
> 라우터를 지역에 따라 모아놓은 시스템

자율 시스템은 전 세계적으로 고유한 AS 번호(autonomous system number, ASN)으로 식별된다.
같은 AS 안에 있는 라우터들은 **동일한 라우팅 알고리즘**을 사용하고 상대방에 대한 정보를 갖고 있다.

자율 시스템 **내부에서 동작하는 라우팅 알고리즘을 AS 내부 라우팅 프로토콜**(intra-autonomous system routing protocol)이라고 한다.
물론 서로 다른 AS에 대해서도 통신이 가능해야 하는데, 이를 가능하게 하도록 도와주는 프로토콜을 **inter-AS (inter-autonomous system routing protocol)**이라고 한다

### 상호 연결된 AS
- 라우터의 Forwarding Table에는 반드시 intra, inter-AS routing algorithm이 담겨있어야 한다.

### Intra-AS
- **intra-AS**는 AS 내부에 존재하는 목적지에 대해 경로를 결정한다.
- 내부 속도가 가장 중요하므로, 성능에 중점을 둔다.

### Inter-AS
- **inter-AS와 intra-AS가 함께 동작하는 경우**, 다른 AS에 존재하는 목적지에 대해 경로를 결정한다.
- 주로 여러개의 ISP 업체에서 자신의 고객에 대해서만 연결을 제공하려고 할 때 사용한다.
- 성능보다는, 정책에 중점을 둔다.

![](https://velog.velcdn.com/images/calzone0404/post/0bf427a4-7319-4cae-ac50-3e92f2d71c81/image.png)

만약 외부 AS로부터 AS1에 Datagram이 전송되었다면, AS1은 이 패킷을 다른 AS로 전송해야할지, 내부로 전송해야할지 결정해야 한다.

>이 작업은 inter-AS가 결정한다.

즉, 자신의 Forwarding Table을 보고 이 목적지가 AS2를 통해서 갈 수 있는 곳인지 AS3를 통해서 갈 수 있는 곳인지 판단한다.

또한, AS1 내부에 있는 모든 라우터의 정보를 통해 내부로 들어가는 데이터인지 판단한다.

Here is the updated and expanded section based on the provided details for 4.6 Routing Protocols and the additional information from the document:

---

# 4.6 Routing Protocols

## 1. RIP (Routing Information Protocol)
> 거리 벡터 알고리즘을 사용한다. 이때, 거리는 자신으로부터 노드를 거쳐서 목적지를 몇 번 만에 갈 수 있는지를 뜻하는 **Hop**으로 계산한다. (최대 15 Hop)

![](https://velog.velcdn.com/images/calzone0404/post/d1039257-e8b5-42c4-910e-b11acfd238a9/image.png)

### RIP 갱신 정보
1. Distance vectors
   - 매 30초마다 주기적으로 자신의 라우팅 테이블을 브로드캐스팅하여 전달한다.
   - 라우팅 테이블에는, 최대 AS 내부에 존재하는 25개의 목적지 서브넷에 대한 정보가 들어있음.
   
### 동작과정

![](https://velog.velcdn.com/images/calzone0404/post/4b5aede3-df3a-47d7-9e5d-16f191b501fa/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/c1734119-1c08-4bca-bc8d-439e769b1868/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/ab19d08f-3bc1-4d4e-b908-a7f87d4703b8/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/c2b564e3-f418-439e-ab08-2f2b5bbb781b/image.png)

### 문제점

![](https://velog.velcdn.com/images/calzone0404/post/a3cc0e29-c028-48f5-8163-69d9dee1ad64/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/a7605f5e-1d11-4dcd-bcfd-7f34b5ee746a/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/c66ebb57-1745-4c41-83e7-00451a8569ce/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/c5afb184-a5e9-467e-829b-55e2f7fe855b/image.png)
- 무한 증가를 방지하기 위해 Poison Reverse를 사용한다. (최대 16 hops)

![](https://velog.velcdn.com/images/calzone0404/post/092c026c-5f47-44e6-a69e-74c53dc0b4ff/image.png)

## 2. OSPF (Open Shortest Path First)
> 개방형 최단 경로 우선 라우팅
- Link State 알고리즘을 사용함
    - 주로 경로를 계산할 때 Dijkstra 알고리즘을 사용함
- 라우터는 같은 AS 대역의 모든 라우터에 자신의 OSPF 정보를 broadcast 해서 전송함
   - IP를 통해서 OSPF가 전달된다.

### OSPF 개선사항
- 보안성
 모든 OSPF 메시지는 OSPF 라우터들 간의 정보 교환 시 인증을 수행
 
 인증을 통해 신뢰할 수 있는 라우터만이 AS 내부의 OSPF 프로토콜을 통한 통신이 가능함
 
- 동일한 비용의 경로 허용
 즉 비용이 동일한 여러 개의 경로가 존재한다면, 모든 트래픽을 전달할 때 하나의 경로만 사용하지 않고 여러 경로를 사용할 수 있다.
 
- Unicast, Multicast 지원

- AS 내 영역 간 계층 영역을 형성할 수 있다 (Area 안의 SubArea 생성 가능)

 ![](https://velog.velcdn.com/images/calzone0404/post/9db05284-844f-4fc3-8693-36e04b636e61/image.png)

>각 영역은 자신의 OSPF 링크 상태 라우팅 알고리즘 (고유한 OSPF 번호를 가짐, **주로 Area 번호**)을 수행한다.

한 영역 내의 라우터는 같은 영역 내의 라우터들에게만 링크 상태를 브로드캐스트한다.

각 영역 내에서 하나 혹은 그 이상의 영역 경계 라우터**(Area border router)**가 영역 외부로의 패킷 라우팅을 책임진다.

Backbone 영역의 주요 역할은 **AS 내 서로 다른 영역 간의 트래픽을 라우팅**하는 것이다.

## 3. BGP (Border Gateway Protocol)
> 인터넷 서비스 제공업자 (ISP) 간의 라우팅

만약 패킷이 한국에 있는 Host에서 미국에 있는 Host로 전송되어야 할 때, inter-AS 프로토콜이 필요하다

같은 AS내에 있는 목적지에 대해서는 Router의 Forwarding Table을 통해 AS intra-Routing 에 의해 결정된다.

그러나, AS 외부에 있는 목적지에 대해서는 **BGP**를 사용해야 한다.

- eBGP : 이웃 AS들로부터 어떤 subnet에 접속이 가능한지 정보를 가져온다.
- iBGP : eBGP로부터 가져온 정보를 iBGP가 모든 AS 내부 라우터에게 외부 AS에 존재하는 전달 가능 정보를 전달해준다.

이러한 동작을 하기 위해서, 각 Subnet은 자신의 존재를 모든 Internet에 광고 메시지를 보낸다.

이때, 광고 메시지는 ***{AS-PATH(통과하는 AS들의 정보,) 광고해야 하는 subnet으로 들어가는 interface 주소}***로 구성된다.

![](https://velog.velcdn.com/images/calzone0404/post/907705ff-3d75-4593-a7c3-d8945d75365b/image.png)

위 예시에서, subnet x에 대한 도달 가능성 정보를 AS1, AS2에 알리는 작업을 생각해보자.

AS3 Gateway Router $3a$는 AS3에서 X로 가능한 경로에 대한 정보를 AS2의 Gateway Router $2c$에 광고한다. >> **eBGP**
- 즉, AS3는 AS2에게 자신한테 x로 가는 데이터를 Forwarding한다면, 전달이 가능하다고 알린다.

$2c$는 전달받은 광고를 자신 AS의 모든 라우터에게 정보를 전달한다 >> **iBGP**

이후, AS2의 $2a$는 위 동작과정과 마찬가지로 AS1의 Gateway router $1c$로 전달하게 된다. **이때 전달되는 정보는 {(AS2, AS3), X}가 전달된다.**

### BGP 경로 선택
>BGP 경로가 여러 경로가 존재한다면, 이들 중 하나를 선택해야 한다.

#### Hot potato routing
> 자신이 속한 AS에서 가장 적은 비용의 게이트웨이를 선택하는 Routing. 이때, Inter-AS 비용(외부 AS로 나가는 비용)은 고려하지 않는다.

![](https://velog.velcdn.com/images/calzone0404/post/2a3f5405-b638-48a9-a243-6ab2fe4bee9c/image.png)

### BGP 메시지
- BGP는 TCP를 통해 전송된다.

```
OPEN : TCP 연결을 위해 열린 상태
UPDATE : 새로운 path 광고
KEEPALIVE : 연결을 유지함
NOTIFICATION : 이전 메시지에 대해 에러를 보고 또는 연결을 종료할 때 사용함
```
