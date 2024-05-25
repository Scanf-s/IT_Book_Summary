> 참고 강의 : [링크](https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard)
> 사용 서적 : Operating System Concepts - 10th edition

# 1. 서론

## 1.1 운영체제가 하는 일
![](https://velog.velcdn.com/images/calzone0404/post/4dc62e60-e344-4eda-9f06-066d3763ad43/image.png)

### 운영체제란?
> 컴퓨터 시스템(하드웨어)를 제어하여 사용자가 실행하는 Application이 시스템 자원을 이용할 수 있도록 도와주는 소프트웨어

책에 따르면 운영체제를 완벽하게 정의하는 말은 정해지지 않았다고 한다.

"the one program running at all times on the computer" << **Linux Kernel과 같은 Kernal에 의해 호출되는 프로그램**이라고 생각하면 될 것 같다.

## 1.2 컴퓨터 시스템 구성요소

![](https://velog.velcdn.com/images/calzone0404/post/dd7afe72-be7b-4312-b26e-a5d43036e754/image.png)

- 하나 이상의 CPU가 존재함
- 여러개의 device controller(마우스, 키보드 등..)들이 **System bus**를 통해서 연결되어 있음

### Bootstrap progarm

> Bootstrap 프로그램은 컴퓨터에 전원을 넣었을 때 처음으로 실행해야 할 **ROM**에 저장된 프로그램이다.

- 디스크에 저장된 커널(O/S)을 CPU에서 연산할 수 있도록 불러와주는 행위가 `컴퓨터 부팅`이라고 이해하면 된다.


### Interrupt

![](https://velog.velcdn.com/images/calzone0404/post/16c0087f-c991-4a3b-b3fe-c69e7bcc71a6/image.png)

키보드를 사용자가 눌렀을 때, 키보드 컨트롤러가 CPU로 전달하는 과정 시그널을 통해 설명한 그림이다.

> 하드웨어는 아무때나 인터럽트 트리거가 발생하게 되면, CPU에게 신호를 system bus를 통해 보내준다.

### 폰 노이만 구조
> 일반적인 명령어 실행 사이클 구조

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/b8a06610-1a3f-4b66-920e-a67185714390)


- 메모리로부터 첫 번째 명령어를 가져오고(**fetch**), 이 명령어를 명령어 레지스터(Instruction Register)에 저장해둔다.
- 저장된 명령어가 디코드되고, CPU가 가져가서 해당 명령을 내부 레지스터에 저장한 뒤, 연산을 수행한다.
- 연산이 완료되면(**executed**), 결과를 필요한 경우 메모리나 레지스터에 저장한다.
- 다음 명령어를 가져오기 위해 프로그램 카운터(Program Counter)를 업데이트한다.
- 이 과정은 반복적으로 수행되며, CPU가 프로그램을 실행하게 된다.

### 저장 장치 구조

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/951addfe-3665-48dc-98a1-06975254a3bf)

메모리는 휘발성이기 때문에, OS와 같이 부팅에 필요한 프로그램을 저장할 비휘발성 장치가 필요하다.
Storage system은 용량과 접근 속도에 따라 계층이 나뉜다. 위에 계층에 존재할수록 용량이 작고 비싸지만, 매우 속도가 빠르다

- Register: CPU 내부에 존재하는 매우 빠른 저장장치
- Cache: RAM과 Register 사이에 존재하는 중간다리 저장장치
- Main Memory: 우리가 주로 알고있는 RAM이다.
- Solid state disk : SSD이다. HDD보다는 비싸지만 속도가 빨라서 요새 많이 사용된다.
- HDD : SSD보다 느리지만, 많은 용량을 더 싸게 사용할 수 있다.
- Optical disk : CD라고 생각하면 된다.
- Magnetic tapes : 주로 백업용으로 사용한다.

### I/O 구조
  
![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/fd25688a-62a7-4ccd-a02c-34adcc76bed1)

- 시스템의 신뢰성과 성능을 보장하기 위해 운영체제 코드의 상당 부분이 I/O 관리를 하기 위해 사용된다.
- 다양한 장치의 특성 때문에 I/O 관리가 복잡하다.


#### 일반적인 컴퓨터 시스템 구성요소

일반적인 컴퓨터 시스템은 여러 장치들로 구성되어 있으며, 이들은 공통 버스를 통해 데이터를 교환한다.
앞서 서술한 인터럽트 기반 I/O는 소량의 데이터를 이동시키는 데 적합하지만, 
대량의 데이터를 이동시킬 때는 높은 오버헤드(지연시간)를 발생시킨다.

#### 직접 메모리 접근 방식 (DMA)

> 대량 데이터 전송의 오버헤드를 해결하기 위해 DMA가 사용된다.

버퍼, 포인터, 카운터를 설정한 후, 
장치 컨트롤러가 CPU의 개입 없이 장치와 주 메모리 사이에 데이터를 직접 전송한다.

DMA는 **블록 단위**로 데이터를 전송하며, **블록 당 하나의 인터럽트**만 생성하여 작업을 수행한다.

CPU는 DMA가 작동하는 동안 다른 작업을 수행할 수 있어 인터럽트 기반 방식보다 훨씬 효율적이다.

#### 고급 시스템에서의 스위치 아키텍처

일부 고급 시스템은 버스 대신 스위치 아키텍처를 사용한다.
- 여러 구성 요소가 동시에 다른 구성 요소와 통신할 수 있다.
- 공통 버스를 두고 누가 먼저 bus를 사용할지 눈치보지 않아도 된다.
- 이러한 시스템에서 사용하는 접근 방식이 `DMA`이다.

## 1.3 컴퓨터 시스템 구조

### 용어 정리
- CPU : 명령들을 실행하는 하드웨어
- Processor : 하나 또는 여러개의 CPU들이 가지고 있는 **물리적인 칩**
- Core : 명령을 실행하고 로컬로 데이터를 저장하기 위한 레지스터를 포함하는 구성요소. **CPU의 기본 계산 단위**
- Multicore : 하나의 CPU에 여러개의 Core가 존재하는 구조
- Multiprocessor : 하나의 컴퓨터에 여러개의 CPU Processor가 존재하는 구조

### 다중 처리기 시스템

현재 우리가 사용하는 컴퓨터는 **MultiProcessor System**이다.
즉, 컴퓨터 내부에 단일 코어 CPU가 있는 두개 이상의 프로세서가 존재한다는 뜻이다.

#### 장점 : 처리량 증가
- 프로세서 수가 늘어났기 때문에 더 적은 시간에 더 많은 작업을 수행할 수 있다.

#### 단점 : 오버헤드 발생
- 여러 프로세서가 하나의 작업을 같이 수행할 때,
  모든 프로세서가 올바르게 작동하는데 유지하는 비용, **오버헤드**가 발생한다.

#### Symmetric multiprocessing (SMP)
> 가장 일반적인 다중처리 시스템이다.

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/d05001c4-2034-4c3b-a4bd-bc164cdfaef0)

### 다중 코어 시스템

> 여러 CPU Processor간의 통신보다, 하나의 CPU에서 여러개의 Core가 통신하는 것이 더 빠르므로 더 효율적인 구조이다.
> 또한, 전력도 더 적게 소모한다.

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/792eec1c-4afd-400f-aa36-f7889200c9e8)

## 1.4 운영체제 작동

### Multiprogramming

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/59cf5312-4c74-470d-8269-e74cec9efbdc)

일단 단일 프로그램은 CPU나 I/O 장치를 항상 100% 전부 사용할수가 없다.
그리고 사용자는 보통 동시에 여러 프로그램을 실행하기 원한다.

따라서 멀티프로그래밍을 통해 CPU 사용률을 높이고, 사용자가 여러개의 프로그램을 실행할 수 있다.

#### 작동 원리

위에 그림처럼 멀티프로그래밍 시스템에서는 여러 프로세스를 동시에 메모리에 올린다.
운영 체제는 이들 프로세스 중 하나를 선택해 실행하고, 특정 프로세스가 I/O 작업 등으로 인해 대기 상태가 되면, 운영 체제는 다른 프로세스로 전환하여 실행한다.
이 과정이 반복되면서 CPU는 항상 실행할 프로세스를 가지고 있어, 자원을 효율적으로 사용할 수 있게 된다.

### Multitasking (= Multiprocessing = Concurrency)

> 멀티태스킹은 멀티프로그래밍을 논리적으로 확장시킨것이다.

멀티태스킹 시스템에서 CPU는 여러개의 프로세스를 빠르게 전환하면서 실행하게 된다.
이러한 전환이 빈번하게 수행되기 때문에 사용자가 동시에 사용하는 프로그램이 끊임없이 작업하는것처럼 보이게 되는것이다.

> 이렇게 여러개의 프로세스가 동시에 실행하는 상태일 때 어떤 프로세스를 다음에 실행할 지 결정하는 것이 **CPU 스케쥴링** 이다.

### Dual-Mode and Multimode Operation

운영체제는 사용자가 사용하는 프로그램이 컴퓨터 시스템의 하드웨어나 소프트웨어 자원을 공유하기 때문에, 잘못된 또는 악의적인 사용을 방지해야 한다.
이를 위해 운영체제 코드 실행과 사용자가 실행하는 코드를 구분해야 한다.

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/80490bff-9de1-44f4-bc01-8d79470e25d7)

#### Dual Mode
위에 그림처럼 이중모드를 사용하기 위해, `user mode`와 `kernel mode`라는 두가지 모드가 필요하다.

`mode bit`가 0인지 1인지에 따라 0이면 커널모드, 1이면 사용자 모드로 판단하여 현재 어떤 코드가 실행중인지 구분이 가능하다.
따라서 시스템이 사용자의 프로그램을 실행할 때는 사용자 모드에서 실행되고, 운영체제의 코드를 실행할때에는 커널 모드로 전환한다.

#### Multimode Operation

> mode bit가 2개 이상의 경우로 확장된 것으로, 예를 들면 Intel Processor에는 4개의 `보호 링`을 통해 현재 실행되는 코드가 시스템인지 사용자인지 구별한다.

> 가상화를 지원하는 CPU라면 가상머신 관리자(VMM - Virtual Machine Manager)가 시스템을 제어할때를 나타내는 별도의 모드(보호 링)이 존재한다.

## 1.7 가상화

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/c6167630-7523-4f1f-9893-4d703811e352)

> 가상화란, 단일 컴퓨터에서 하드웨어를 여러가지 실행 환경으로 추상화하여 개별적 환경이 자신만의 컴퓨터에서 실행될 수 있도록 만드는 기술이다.

우리가 주로 사용하는 VMware나 VirtualBox라고 생각하면 된다.

### 활용
- 단일 물리적 서버에서 여러 운영 체제를 실행하여 개발, 테스트 및 디버깅을 할 수 있다.
- 물리적 메모리보다 큰 프로그램을 실행할 수 있는 가상 메모리 기능을 제공한다.

네, 설명에 몇 가지 오류와 모호한 부분이 있습니다. 수정된 설명은 다음과 같습니다:

### 에뮬레이션과 가상화의 차이점

> **에뮬레이션**: 원래의 CPU와 다른 CPU에서 동작하는 소프트웨어를 실행하기 위해, 원래의 CPU 명령어를 현재 CPU에서 이해할 수 있는 명령어로 변환하여 실행하는 방식.
> **가상화**: 동일한 CPU에서 여러 운영체제가 실행될 수 있도록 하여 각 운영체제가 독립적으로 실행될 수 있게 하는 기술. 하이퍼바이저를 사용하여 물리적 리소스를 여러 가상 시스템이 공유하도록 함.

## 1.10 Computing 환경 종류

- Traditional Computing
- Mobile Computing
- Client-Server Computing
- P2P Computing
- Cloud Computing
- Real-Time Embedded Systems
