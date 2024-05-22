> 참고 강의 : [링크](https://www.inflearn.com/course/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EA%B3%B5%EB%A3%A1%EC%B1%85-%EC%A0%84%EA%B3%B5%EA%B0%95%EC%9D%98/dashboard)
> 사용 서적 : Operating System Concepts - 10th edition


## 1.1 운영체제가 하는 일
![](https://velog.velcdn.com/images/calzone0404/post/4dc62e60-e344-4eda-9f06-066d3763ad43/image.png)

### 운영체제란?
> 컴퓨터 시스템(하드웨어)를 제어하여 사용자가 실행하는 Application이 시스템 자원을 이용할 수 있도록 도와주는 소프트웨어

책에 따르면 운영체제를 완벽하게 정의하는 말은 정해지지 않았다고 한다.

"the one program running at all times on the computer" << **Linux Kernel과 같은 Kernal에 의해 호출되는 프로그램**이라고 생각하면 될 것 같다.

## 1.2 컴퓨터 시스템 구조
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

![](https://velog.velcdn.com/images/calzone0404/post/e062e0e1-0518-451d-a4b8-ae86e1cd0d70/image.png)


- 메모리로부터 첫 번째 명령어를 가져오고(**fetch**), 이 명령어를 명령어 레지스터(Instruction Register)에 저장해둔다.
- 저장된 명령어가 디코드되고, CPU가 가져가서 해당 명령을 내부 레지스터에 저장한 뒤, 연산을 수행한다.
- 연산이 완료되면(**executed**), 결과를 필요한 경우 메모리나 레지스터에 저장한다.
- 다음 명령어를 가져오기 위해 프로그램 카운터(Program Counter)를 업데이트한다.
- 이 과정은 반복적으로 수행되며, CPU가 프로그램을 실행하게 된다.
