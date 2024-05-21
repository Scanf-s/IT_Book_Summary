### 6.1 데이터 링크 계층 소개

#### Terminology
1. **Host**와 **Router**는 `NODE`라고 부른다
2. `link`란, 통신 경로를 따라 인접 노드를 연결하는 통신 채널을 의미함. 예를 들어, Wired Link, Wireless Link, LANs
3. 2계층의 데이터 단위는 `frame`이라고 부른다. 이 frame들을 캡슐화 한 것이 `datagram`이다.

>즉, 데이터 링크 계층은 Link를 통해 물리적으로 인접한 Node로 datagram을 전송하는 역할을 담당한다.**

#### Context

- Datagram은 서로 다른 Link Protocol들을 통해 서로 다른 Link로 전송된다.
- 각 Link Protocol은 서로 다른 서비스를 제공한다.
- L2의 전송 과정은 다음과 같은 예시를 들 수 있다. 서울에서 부산으로 가는 여행을 하려고 할 때,
  
  여행사가 다음과 같은 경로를 여행객에게 제공하였다.
  1. 서울에서 평택으로 갈 때는 고속 버스를 탄다.
  2. 평택에서 대구로 갈 때는 비행기를 탄다.
  3. 대구에서 부산으로 갈 때는 택시를 탄다.

  이때 여행객은 **Datagram**, 거쳐가는 경로는 **Communication Link**, 이동 수단은 **Link Layer Protocol**, 여행사는 **Routing algorithm**

#### 데이터 링크 계층의 Service

1. **Framing, Link Access**: **Datagram**을 header와 trailer를 추가하여 frame으로 캡슐화 한다.
   - Header: 목적지, 출발지 주소, 데이터 내용을 정의
   - Trailer: 비트의 에러를 감지
   - Frame의 header에서 출발지와 목적지를 식별하기 위해 **MAC주소**를 사용함.

2. **인접 노드 간의 신뢰성 있는 데이터 전송**

3. **흐름 제어**: 인접한 송신자와 수신자 노드 사이의 송수신 속도를 조절한다.

4. **에러 감지**: 신호 감쇠와 노이즈로 인해 발생하는 에러를 감지한다.
   - 수신자가 오류를 감지하면 발신자에게 **재전송을 요청**하거나 **프레임을 삭제**해버림.

5. **에러 수정**: 수신자가 에러를 감지하면, bit error를 **재전송** 하거나 **삭제**를 하지 않고, 복구 작업을 수행함.

6. **전이중 및 반이중 전송**
   - **Half Duplex**: 수신자와 송신자 사이의 통신은 한번에 하나씩 진행되지만, **쌍방향**으로 진행한다. 즉, 수신자와 송신자는 각자 정해진 시간 안에서 정보를 송수신할 수 있다.
   - **Full Duplex**: 수신자와 송신자 사이의 통신이 **동시에** 일어난다.
  
   ![Duplex](https://velog.velcdn.com/images/calzone0404/post/7092ccc0-3b60-4f7d-a4d1-bd0dd3ca6717/image.png)

#### 데이터 링크 계층이 구현된 위치
- 데이터 링크 계층은 모든 호스트에 구현되어 있다.
- 데이터 링크 계층은 **NIC**라고 불리는 어댑터에 구현되어 있다. 
  > 우리 컴퓨터에 꽂혀있는 랜카드라고 생각하면 된다.
- 호스트의 CPU, Main Memory와 함께 사용되며, Hardware, Software, Firmware의 결합체이다.

##### Adaptor끼리 통신하는 방법

###### 1. 송신측
- 송신측에서는 Datagram을 Frame으로 캡슐화 한다.
- 캡슐화할 때, Error checking bits, Rdt, Flow Control 정보도 함께 추가된다.

###### 2. 수신측
- 수신측에서는 에러가 발생했는지 Error checking bits, Rdt, Flow Control을 통해 확인한다.
- 에러가 발생하지 않았다면 상위 계층인 네트워크 계층으로 Frame의 캡슐을 풀어서 Datagram을 전달해준다.

---

# 6.2 Error Detection & Correction

## Error Detection이란?

- **EDC** : Error Detection and Correction bits
- **D** : Data protected by error checking bits. 이 데이터는 header field가 존재한다.

>하지만, Error detection만으로는 100% 신뢰성 있는 전송을 보장할 수 없다.

프로토콜이 간혹 몇몇 에러를 놓칠 가능성이 존재하기 때문이다.

- EDC field가 클수록 더 나은 Detection과 Correction이 가능하다.
![EDC](https://velog.velcdn.com/images/calzone0404/post/0c40d802-b081-455f-a622-ab3ceddd625d/image.png)
송신측에서 D를 EDC와 함께 전송하면, 수신측에서는 에러 검사를 통해 올바른 D가 전송되었는지 확인하게 된다.

- 이때, 에러를 검사하는 방법은 총 3가지가 존재한다.

### 1. Parity Checking

![single](https://velog.velcdn.com/images/calzone0404/post/60d4bf86-074a-41b4-9f55-80c71aac66d7/image.png)
- Single Bit Parity는 1bit로 구성되며, 이 방법은 Detection만 수행한다.

![two](https://velog.velcdn.com/images/calzone0404/post/6362a65a-db22-45cd-a6f7-5a8d87aaf8ef/image.png)
- Two Dimensional bit parity 방법은 Detection & Correction이 가능하다.

***
**Odd parity** : 1의 개수가 홀수개
**Even parity** : 1의 개수가 짝수개

예를 들어, Parity 정책을 Odd parity를 적용한다면,
```
D = 111 1111
```
D의 1의 개수가 홀수개 이므로, Parity bit로 0을 추가한다. 즉, 전송되는 형태는 다음과 같다

```
111 1111 0
```

수신측에서 Odd Parity 정책임을 알고있고, 아래의 데이터를 전달받았다면

```
D = 101 1111 0
```

수신측은 Data에 오류가 존재한다는 것을 알 수 있다.

Even Parity정책을 적용했다면, 다음과 같다
![](https://velog.velcdn.com/images/calzone0404/post/75b5828b-1b25-49dc-8f71-ce0dbd585bf8/image.png)

***

![](https://velog.velcdn.com/images/calzone0404/post/99c38ec5-4153-4279-8424-3fd35e95148f/image.png)

위 그림은 Even Parity 정책을 적용했음을 알 수 있다.
***

>그러나 이 방법은, 1bit의 오류에 대해서는 잘 검출할 수 있지만, 그 이상의 bit오류에 대해서는 좋지 않은 방법이다.


### 2. Cyclic Redundancy Check
**CRC** : 순환 중복 검사

1. 2진수로 이루어진 Data bit를 **D**라고 표기한다
2. $$(r+1)$$ bit 길이의 패턴을 선택한 것을 **G**라고 표기한다 _(G는 주로 소수로 결정한다)_
3. $$r$$ bit 길이의 CRC bits를 R이라고 표기한다

$$<D, R>$$ 은 Databit와 CRC bit를 연결한 bit이다.

![dr](https://velog.velcdn.com/images/calzone0404/post/a7e8cd26-dbc9-4b9c-b4df-218017ab8fb9/image.png)

- 송신자는 $$<D, R>$$ XOR \ 2 연산을 통하여 연산한 후, **나머지를 $$<D, R>$$ bit에 더해준다.**

- 수신자가 **G**를 알고 있다고 했을 때, 수신된 $$<D, R>$$을 $$<D, R>$$ XOR \ 2 연산을 수행한다.

- 이때, $$<D, R>$$이 나누어 떨어진다면, Error가 존재하지 않는다는 것이고, 나머지가 존재한다면 Error가 발생했다는 것을 알게 된다.

따라서, 수신자는 $$(r+1)$$ bit보다 작은 개수의 모든 Error bit를 검출 가능하다.

![example](https://velog.velcdn.com/images/calzone0404/post/a95b3590-fcfe-471c-a708-6f1bcc75c04c/image.png)

---

# 6.3 다중 연결과 프로토콜

데이터 링크 계층의 Link는 두 가지로 나뉜다.

1. Point to Point
2. Broadcast (하나의 Link를 여러 Node가 공유하는 형태)
![broadcast](https://velog.velcdn.com/images/calzone0404/post/c82ddf0d-3933-4662-8c0f-92ddf2a6120e/image.png)

## 다중 연결 제어 프로토콜

- 하나의 Link를 여러 Node가 공유할 때, 두 개 이상의 Node가 전송을 시도하게 된다면 충돌이 발생한다.

> 따라서, Multiple Access Protocol을 통해 어떻게 충돌을 제어할 것인지 결정하게 된다.
- 즉, Node가 언제 전송할 수 있을지 결정한다.

다중 연결 제어 프로토콜로는 크게 세 종류가 있다.

### 1. Channel Partitioning
- Channel을 작은 **"조각"**으로 나누어버린다. (시간 분할 방식, 주파수 분할 방식, 코드 분할 방식)

#### TDMA 방식
- 고정된 시간대에만 Channel 접근이 가능하다.
- 각 Station은 고정된 길이(패킷 전송 시간)만큼 크기가 고정되어 있다.
* 이 방식은 전송을 사용하지 않을 때 낭비가 발생하게 되는 문제가 있다.
![tdma](https://velog.velcdn.com/images/calzone0404/post/2d5aeb46-4745-46f0-b544-a99f059ef25d/image.png)

#### FDMA 방식
- Channel이 주파수 대역에 따라 분할된다.
- 각 Station은 고정된 주파수 대역에 할당된다.
* TDMA와 마찬가지로 사용하지 않으면, 해당하는 대역이 낭비된다.
***
### 2. Random Access

어떤 Node가 전송하려고 할 때, 전체 채널 데이터 전송률 R만큼 통째로 사용하여 전송한다.

- Node끼리 우선순위가 존재하지 않으므로 그냥 전송을 해버리게 된다.

만약, 2개 이상의 Node가 전송을 시도하게 된다면 당연히도 **충돌**이 발생한다.

Random Access가 충돌을 해결하는 방법으로는 크게 3가지가 있다.

#### 1. ALOHA 방법
- 단순하며, Node 간의 시간 동기화가 필요하지 않다.
- **Frame**이 처음 도착하면, 바로 전송을 해버린다.
- 충돌이 발생하면, **Frame**을 그냥 Drop해버린다.

이 방식은 충돌 확률이 매우 높다.
![aloha](https://velog.velcdn.com/images/calzone0404/post/8630cbbe-3400-43a4-9fd0-ff75dbe873c0/image.png)

#### 2. Slotted ALOHA 방법

- 동일한 Frame size, 동일한 시간으로 Slot을 분할한다.
- Slot이 시작하는 타이밍일 때만 전송이 가능하다.
- 만약 하나의 Slot에 두 개 이상의 Node가 전송을 시도한다면, 충돌이 발생한다.

> 충돌이 발생하지 않았다면, Node는 새로운 **Frame**을 다음 Slot에도 전송할 수 있다.

> 충돌이 발생했다면, Node는 **Frame**을 전송이 성공할 때까지 다음 Slot에 계속해서 **재전송**을 시도한다.

**장점**
- 성공하면 계속해서 Channel의 전부를 사용 가능하다.
- 간단한 방식이다.

**단점**
- 충돌이 발생하면, 낭비되는 Slot이 발생한다.
- 모든 Node의 Slot Clock을 동기화해주어야 한다.
![saloha](https://velog.velcdn.com/images/calzone0404/post/f2f02b96-e68d-414d-89dc-06c0d0d1a885/image.png)

#### 3. CSMA (Carrier Sense Multiple Access) 방법

- 전송하기 전에, 어떤 Node가 전송 중인지 확인하는 방법이다.

 만약 Channel이 사용 중이 아닌 상태라면, Frame 전체를 전송한다.
 Channel이 사용 중이라면, 사용이 끝날 때까지 대기한다.
 
- 이 과정을 비유하자면, **다른 사람이 말할 때, 말을 끊지 않고 들어주는 것에 해당한다.**

이 방법도 여전히 충돌 가능성이 존재한다.
그 이유는 Propagation Delay 때문인데, 아래 그림을 보자.
![csmacol](https://velog.velcdn.com/images/calzone0404/post/387ff587-efc0-4fd6-ba8f-5e5fae14484a/image.png)

B가 전송 중인 상태에서, 내가 전송 중이라는 신호를 다른 Node들에게 뿌리게 된다.

그런데, 이 신호가 전파되기 전에, D 입장에서는 채널을 아무도 사용하고 있지 않으므로 전송을 시작하게 된다.

충돌이 발생하더라도 CSMA 방법은 전송을 중지하지 않고, 계속해서 Frame을 전송하므로 충돌이 발생한 후 전송된 모든 Frame이 Drop된다. 즉, **낭비**가 발생한다.

이 방법을 개선한 것이 바로 **CSMA/CD** 방식이다.

#### CSMA/CD (Collision Detection)

- 짧은 시간 안에 충돌이 감지된다면, **전송을 중지**해버려서 **Channel 낭비를 줄인다**.

- 이 방법은 유선에서는 좋은 방법이지만, 무선에서는 좋지 않은 방법이다. (무선은 다른 방법을 사용하게 된다)

- 이 방법을 비유하자면, 서로 말이 겹쳤을 때, 말을 멈추고 서로 양보하는 행동이라고 할 수 있겠다.

#### CSMA/CD 알고리즘 동작 과정

1. 어댑터는 네트워크 계층으로부터 데이터그램을 받아서 링크 계층 프레임을 만든 후에 그 프레임을 어댑터 버퍼에 저장한다.

2. 어댑터는 채널이 유휴(idle) 상태임을 감지하면 프레임 전송을 시작한다.
   - 만일 어댑터가 채널이 바쁜(busy) 상태임을 감지하면, 어떤 신호 에너지도 감지되지 않을 때까지 더 기다렸다가 프레임을 전송하기 시작한다.

3. 전송하는 동안 어댑터는 브로드캐스트 채널을 사용하는 다른 어댑터로부터의 신호 에너지가 있는지 감시한다.

4. 프레임 전체를 전송하는 동안 다른 어댑터로부터의 신호 에너지가 감지되지 않으면, 프레임 전송을 완료한다.
   - 감지되면 자신의 프레임 전송을 취소한다.
5. 전송 취소 후 임의의 **랜덤 시간**만큼 기다린 후 2단계로 돌아간다.
   - 만일 랜덤 시간이 아니라 고정 시간이라면 동시에 프레임을 전송했을 때 똑같은 시간을 기다린 후 전송을 하므로 계속해서 충돌하게 된다.

#### 랜덤 시간 결정 알고리즘 Backoff

- 충돌을 n번 경험한 프레임을 전송할 때 노드는 {0,1,2,…,2^n - 1} 중에서 랜덤하게 K 값을 선택한 후 $K * Bit 길이$ 시간만큼 기다린다.

이더넷의 경우 $K * 512$ 비트 시간$(이더넷으로 512 비트를 전송하는데 걸리는 시간 * K)$가 되며 n의 최댓값을 10으로 제한한다.

즉, 충돌을 많이 경험할수록 K의 범위가 지수적으로 커지게 된다.

새 프레임을 준비할 때는 최근 발생한 충돌을 고려하지 않고 CSMA/CD를 수행하여 충돌을 경험한 노드보다 먼저 전송될 수도 있다.

### 3. Taking Turns (Round Robin)

#### Polling 방법
- Master Node는 Slave Node를 Round Robin 방식으로 선택(Polling)한다.
- Master는 먼저 선택한 Node에게 해당 Node가 최대로 보낼 수 있는 프레임 수에 대한 메시지를 전송하고, 해당 Node가 프레임을 전부 보낸 다음,
다음 노드도 똑같이 수행하여 순환적으로 각 노드를 폴링하는 방식으로 이 과정을 계속한다.

##### 장점
1. Idle Slot을 제거할 수 있다.

##### 단점
1. Polling 지연 시간이 존재한다.
2. Master가 고장 나면, 전체 채널이 동작하지 못한다.

#### Token Passing 방법
- 토큰(token)이라고 알려진 작은 특수 목적 프레임이 정해진 순서대로 노드 간에 전달된다.
  - 여기서 토큰은, 통신할 수 있는 권리에 대한 티켓이라고 생각하면 된다.

예를 들어, 노드 1은 항상 노드 2에 노드 2는 노드 3에 노드 N은 노드 1에 토큰을 전송한다.

- 노드가 토큰을 수신하면, **전송할 프레임이

 있을 때만 토큰을 붙잡고**, **그렇지 않으면 토큰을 전달**한다.

프레임을 최대 개수까지 전송한 뒤 토큰을 다음 노드로 전달한다.

##### 장점
1. 분산 방식이기 때문에 효율적이다.

##### 단점
1. 노드 하나가 실패하면, 전체 채널이 동작하지 않는다.
2. 어떤 노드가 토큰을 넘겨주지 않는다면, 토큰이 다시 돌 수 있도록 하는 알고리즘이 필요하다.

---

# 6.4 데이터 링크 계층 주소체계
## MAC 주소와 ARP
> 네트워크 계층에서는 32bit의 IP 주소를 사용하지만, 데이터 링크 계층에서는 **MAC 주소**를 사용한다.

- **MAC 주소**
  - 어떤 NIC에서 물리적으로 연결된 다른 NIC로부터 Data Frame을 전달받기 위해 지정하는 주소
  - 48bit의 16진수로 표현한다.

![mac](https://velog.velcdn.com/images/calzone0404/post/69e202ae-55a5-4d1e-883f-a7501f43a472/image.png)

MAC 주소는 주민번호로 비유할 수 있고, IP 주소는 우편번호로 비유할 수 있다.
- MAC 주소는 평면적인 주소 체계이다. 즉, 위치가 바뀌더라도 NIC가 동일하다면 MAC 주소는 동일하다.

- 반면 IP 주소는 IP 서브넷에 의해 주소가 결정되므로 이동 불가능하다.

## ARP : Address Resolution Protocol
> IP 주소는 알고 있는데, NIC의 MAC 주소는 어떻게 결정해야 할까?

### ARP Table
- 각 Node들의 IP를 **MAC 주소로 Mapping**하는 테이블

- **IP 주소, MAC 주소, TTL**로 구성된다.

- TTL : Time To Live
  - 주소 Mapping이 잊혀지는 시간 (주로 20분으로 설정된다)

- 각 호스트와 라우터는 자신의 메모리에 **ARP 테이블(ARP table)**을 갖고 있다.

- 테이블에 서브넷상의 **모든 호스트와 라우터에 대한 엔트리를 갖고 있지 않아도 된다**.
  - 즉, 데이터그램을 전송하려 할 때, ARP 테이블에 목적지 노드에 대한 엔트리가 없을 수 있다.

### ARP 동작 과정

>A가 B에게 Datagram을 보낸다고 해보자. 이때, A는 B의 MAC 주소를 모른다.

1. A는 MAC 주소 문제를 해결하기 위해 ARP 프로토콜을 사용하여 ARP 패킷이라는 특수 패킷을 어댑터에 보낸다.
   - ARP 패킷은 송수신 IP 주소와 MAC 주소를 포함하는 필드를 가지며 질의 패킷과 응답 패킷 모두 같은 형식을 갖는다.
   - 질의 패킷의 목적은 해결하려는 IP 주소에 대응되는 MAC 주소를 결정하기 위해 서브넷의 다른 모든 호스트와 라우터들에게 질의하는 것이다. **(B를 아시나요?)**

2. 어댑터는 ARP 패킷을 링크 계층 프레임에 캡슐화하고, 목적지 주소를 **MAC 브로드캐스트 주소(FF-FF-FF-FF-FF-FF)**로 하여 패킷을 전송한다.

3. 이 질의는 서브넷 상의 다른 모든 어댑터에 의해 수신되며, 브로드캐스트 주소 때문에 각 어댑터는 프레임에 들어있는 ARP 패킷을 자신의 ARP 모듈로 전달한다.

4. ARP 모듈은 자신의 IP 주소가 ARP 패킷에 들어 있는 목적지 IP 주소와 일치하는지 검사한다. **(내가 A가 찾고 있는 사람인지 판단)**

5. 일치하는 노드는 요구된 매핑 정보가 포함된 응답 패킷을 돌려보낸다. **(내가 찾고 있는 사람이 맞다면 응답한다)**
   - 질의 패킷은 브로드캐스트 프레임으로 전송되는 반면 응답 패킷은 표준 프레임으로 전송된다. **즉, A에게 바로 응답한다.**

6. 질의한 호스트 A는 자신의 ARP 테이블을 갱신한다.

즉, 노드의 ARP 테이블(ARP table)은 플러그 앤 플레이(plug-and-play)다. 즉, 관리자가 구성하지 않아도 자동으로 구축된다.

ARP는 링크 계층 주소도 포함하고, 네트워크 계층 주소도 포함하기 때문에 네트워크 계층과 링크 계층의 경계에 있는 프로토콜이다.

---

# 6.5 Ethernet

## Topology

### Bus Topology

![bus topology](https://velog.velcdn.com/images/calzone0404/post/a06c1297-742a-49da-b454-3718d8930991/image.png)

현재는 쓰이지 않는 형태이다.
하나의 공통된 중앙선으로 데이터를 전송하기 때문에, 충돌이 발생한다.

### Star Topology

![star topology](https://velog.velcdn.com/images/calzone0404/post/47fc2c8b-8d01-428f-a311-ed701d3b93aa/image.png)

현재 주로 쓰이는 형태이다.
스위치를 통해 정해진 경로로 데이터를 전송하므로, 충돌이 발생하지 않는다.

## Ethernet Frame 구조

![Ethernet frame](https://velog.velcdn.com/images/calzone0404/post/10b804ef-566d-4e9d-bd3c-0ff848683c49/image.png)

1. **Preamble**
   - 첫 7byte: 10101010, 마지막 byte: 10101011
   - 송신 어댑터와 목적지 어댑터 간의 송수신 속도를 맞추기 위해 사용

2. **Dest/Source Addr.**
   - 출발지 및 목적지 MAC 주소 (총 6byte)

3. **Type**
   - 상위 계층 프로토콜을 사용할 때, 이를 표시하는 Flag

4. **CRC**
   - 순환 중복 검사 필드. 에러가 발생하면 프레임을 버려버린다.

## Ethernet: 비신뢰적, 비연결형 서비스

### Connectionless
- NIC 간의 통신에 Handshaking 과정이 없음

### Unreliable
- NIC 간의 통신에 ACK, NACK 신호를 보내거나 받지 않음. 즉, 신뢰성 있는 연결을 보장하지 않음

## Ethernet 표준
- 매우 다양한 표준이 존재한다.

![Ethernet standards](https://velog.velcdn.com/images/calzone0404/post/c683d676-0de9-4fcf-89d0-4b7a651630b8/image.png)
   - 100BASE-T 프로토콜에서 **Manchester encoding**을 통해 데이터를 전송한다.

---

# 6.6 스위치 종류

## 1. Hubs
- 어떤 링크로 전달되는 데이터가, 다른 모든 링크로 전달됨
- CSMA/CD가 없기 때문에 충돌이 매우 자주 발생함
- 따라서 Host의 NIC에서 충돌에 대해 따로 처리해주어야 함

## 2. Ethernet Switch
- Ethernet Frame을 저장 및 포워딩함
- CSMA/CD가 적용됨
- 들어오는 Frame에 대해 MAC 주소를 보고 적절한 경로로 전달해줌
- 투명성: host는 switch의 현재 상태가 어떤지 모름
- 스위치는 **스스로 학습**하기 때문에 따로 설정할 필요 없음

### 스위치는 다중 동시 연결을 허용한다.
- 각 호스트들은 각자 지정된 Switch 경로를 가지고 있다.
- 스위치는 호스트로부터 전달되는 Frame에 대해 Buffer에 저장한다.
- Switching 과정을 통해 같은 Switch에 연결되어 있는 Host끼리 통신 가능하다. (Hub와 달리 충돌 없이 전달된다)

### Switch Table

![Switch table](https://velog.velcdn.com/images/calzone0404/post/dba8228a-d9b6-4401-be51-0538e0114660/image.png)
- 각 Switch는 Switch Table을 가지고 있다.
> **{ Host의 MAC 주소, Host와 직접 연결된 Interface Number }**

### 자가 학습 과정
1. Switch는 각 interface를 통해 어떤 host로 갈 수 있는지에 대해 알기 위해, Broadcast Frame을 뿌린다.
2. 답장받는 frame에 대해서, 다음 정보를 저장한다
   - 답장 송신자 MAC 주소
   - 답장이 들어온 Interface Number
   - 현재 시간
   - TTL (저장 시간)

### Frame 전달 과정
```cpp
if (수신된 Frame에 적힌 MAC 주소가 자신의 Table에 존재하지 않는다면) {
    자신이 보낸 Frame을 제외한 모든 Interface에 이 MAC 주소를 가진 Host가 누구인지 Broadcast
} else { // 테이블에 MAC 주소가 존재하는 경우
    if (자신의 인터페이스에 대한 패킷인 경우) {
        패킷을 DROP (Filtering)
    } else {
        테이블을 확인해서 해당하는 interface로 Frame을 전달한다. (Forwarding)
    }
}
```

### Interconnecting switches

![Interconnecting switches](https://velog.velcdn.com/images/calzone0404/post/89810d3e-1ed1-4496-8d4c-419fbfc24344/image.png)

- 여러 개의 스위치가 연결되어 있어도, 스위치가 1개만 있을 때와 마찬가지로 Switch Table이 구성된다.
- 자가 학습을 통해 Switch Table이 구성된다.

## Switch와 Router의 공통점과 차이점
- **공통점**
  - 둘 다 Forwarding Table을 참조해서 데이터를 전달한다.
  - Store-and-forward 구조이다.

- **차이점**
  - Switch: 데이터 링크 계층 장치
    - Broadcast를 통해 학습, MAC 주소를 이용
  - Router: 네트워크 계층 장치
    - Routing 알고리즘을 통해 학습, IP 주소를 이용
