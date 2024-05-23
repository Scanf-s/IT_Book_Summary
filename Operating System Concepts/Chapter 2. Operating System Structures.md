# 2.1 Operating System Services

> OS의 역할은 프로그램들이 실행될 수 있는 환경을 제공하는 것.

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/9cfbe50f-04ba-47df-a514-51c785298a49)

OS는 다음과 같은 기능을 제공한다.
- User Interface
- **Program execution (Process)**
- I/O operation
- **File-system manipulation**
- Communications
- Error detection
- Resource allocation
- Logging
- Protection and security

# 2.2 User and Operating-System Interface

> 사용자가 OS와 소통하기 위한 근본적인 세가지 방법에 대해 소개한다.

## 1. CLI : Command Line Interface

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/5929912b-f08d-41af-8fe9-550252bb706e)

- Shell이라고 생각하면 된다.
- Shell의 종류로는 sh, **bash**, csh, tcsh, **zsh**등이 있다.
- 즉 명령어 기반으로 OS와 소통하는 방법이다.

## 2. GUI : Graphic User Interface
- Windows, MacOS, Linux GNOME, etc..

## 3. Touch-Screen Interface

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/0c76854c-b00c-40f7-85c6-db4ecfa31acf)

- Android, IOS, etc...


# 2.3 System Calls
> OS에 의해 사용 가능한 서비스들의 interface를 제공해주는 기능이다. (사용할 수 있도록 만드는)

아래 예시는 시스템 콜을 호출하는 함수이다.
![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/8737449b-540a-4d6a-9a21-48ce66efddf8)
- `read()`라는 함수를 사용해서 `filedescription`장치의 `buffer`의 데이터를 `count`만큼 읽으라는 명령

> OS에서 이러한 System Call을 호출하는 것을 API(Application Programming Interface)라고 부른다.

우리같은 프로그래머가 시스템 콜을 사용하는 API를 통해 프로그래밍 하는 이유는, 
운영체제나 하드웨어에 맞는 동작까지 구현하기에는 너무 복잡하기 때문이다 아래 그림을 확인해보자.

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/4e3db537-d96d-4d57-850c-407c5b345398)

`User mode`에서 프로그래머가 `open()` 시스템 콜을 호출하면,
`system call interface`가 이걸 받아서 `kernel mode`로 넘기고 해당 함수를 처리해준다.

만약 개발자가 단순히 `API`를 통해 개발하는 것이 아니라, `kernel mode`에 들어가서,
이를 처리하는 과정까지 구현한다면 개발이 매우 복잡해진다.

따라서, 현재의 대부분 프로그래밍 언어는 시스템 콜을 호출하는 API를 지원하고 있다.
![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/7c0f9438-d144-4b8f-8e7b-b0be34b42cda)
![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/73d25419-a31e-418d-8c2f-908630a2cde3)

