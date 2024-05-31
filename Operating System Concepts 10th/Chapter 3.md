# 3.1 Process Concept

## 3.1.1 Process

> Process: 실행중인 프로그램

좀 더 자세하게 말하면, **Process는 OS의 작업 단위**이다.
프로세스는 작업을 수행하기 위해 특정한 리소스가 필요하다. 예를 들면,
- CPU 사용 시간
- Memory
- Files
- I/O devices

I/O device를 통해 C언어로 이루어진 코드를 작성하고, 이 코드를 gcc를 통해 컴파일하게 되면
`asdf.out`파일이 생성된다. `asdf.out`파일을 실행하려면 먼저 `메모리`에 해당 파일의 내용인 `instructions`을 올려야 하고,
`메모리`에 올려진 `instructions`를 CPU가 `fetch`해서 실행한다.

> 프로세스의 현재 활동의 상태는 `프로그램 카운터 (PC)`값과 프로세서 레지스터의 내용으로 나타낸다.

### 프로세스의 메모리 배치

![](https://velog.velcdn.com/images/calzone0404/post/8b7c987f-9af6-4cbf-86b2-7d277ce1c790/image.png)

- Text Section : 실행 코드
- Data Section : 전역 변수
- Heap Section : 프로그램 실행 중 **동적으로 할당되는 메모리**
- Stack Section : 함수 호출 시 임시 데이터 저장장소 (함수 매개변수, return address), 지역 변수

그림에서 보이는것처럼 스택과 힙 영역은 동적으로 줄어들거나 커질 수 있다.
함수가 호출될 때 마다 함수 매개변수, 지역 변수, 복귀 주소를 포함하는 **활성화 레코드**가 스택에 Push된다.

### C 프로그램의 메모리 배치

![](https://velog.velcdn.com/images/calzone0404/post/005aec4b-cd36-471b-be2f-40a545c530ab/image.png)

1. 초기화된 전역변수는 initialized data section에 배치된다.
2. 초기화가 되지 않은 전역변수는 자동으로 0을 할당받고, uninitialized data section(**BSS**)에 배치된다.
3. main()함수에 전달된 argc, argv 매개변수가 저장되는 별도의 섹션이 stack 영역에 저장된다.

실제로 위 코드의 메모리 할당을 보면 다음과 같다.

![](https://velog.velcdn.com/images/calzone0404/post/4c312c73-1ff0-4731-adaf-9081d9232252/image.png)

> dec과 hex값은 text, data, bss 세 영역의 총 합을 10진수, 16진수로 표현한 값이다.


## 3.1.2 Process State

> 프로세스는 실행되면서 상태가 변한다.

![](https://velog.velcdn.com/images/calzone0404/post/f7bc9bd8-18b3-4262-aab4-f8c4ed3a735e/image.png)

- New : 프로세스가 생성중인 상태
- Ready : 프로세스가 CPU에 할당되기를 기다리는 상태
- Running : 명령어들이 실행되는 상태
- Waiting : 프로세스가 어떤 이벤트가 일어나기를 기다리는 상태 (입출력 완료 or 신호 수신)
- Terminated : 프로세스의 실행이 종료된 상태

## 3.1.3 Process Control Block

> 각 프로세스는 운영체제에서 Process Control Block (**PCB**)에 의해 표현된다.

![](https://velog.velcdn.com/images/calzone0404/post/43b4a927-d180-4d20-a0a6-de48f31d22a4/image.png)

**PCB는 특정 프로세스와 연관된 여러 정보를 가지고 있다.**
- Process State : 3.1.2절에 나온 내용
- Program Counter : 다음에 실행할 명령어의 주소를 가리키는 포인터
- CPU registers
- CPU-scheduling information
- Memory-management information
- Accounting information : CPU 사용 시간, 사용 경과 시간, 시간 제한 등..에 대한 정보
- I/O status information

## 3.1.4 Thread

지금까지 논의한 프로세스 모델은 **프로세스가 단일 실행 `thread`를 실행하는 프로그램**이었다.
예를 들어, 만약 한 스레드가 Excel 프로그램만을 실행 중이면, 실행되는 명령어의 단일 제어 thread가 존재한다는 것이다.

> **단일 제어 thread**란, 프로세스가 한 번에 딱 하나의 작업만 실행하도록 허용하는 것이다.

따라서, 단일 thread로만 작업을 수행한다면, Excel 프로그램만 실행중이고, 사용자는 문자를 입력하거나 Excel 함수를 사용하는 작업을 할 수 없다는 뜻이 된다.

> 따라서 현대의 운영체제는 **Multi-Thread**를 사용하여 한 번에 하나 이사으이 일을 수행할 수 있도록 허용한다.

# 3.2 Process Scheduling

> **Multi programming**의 목적은 CPU의 이용을 최대화해서 동시에 여러개의 프로세스를 실행하는 것이다.

이 목적을 수행하기 위해서 `Process Scheduler`는 CPU core에서 실행 가능한 여러개의 프로세스 중, 하나를 골라서 Fetch한다.
이렇게 고르는 작업을 특정 시간마다 빈번하게 수행하는데, 이를 `time sharing`이라고 한다.

> **time sharing**의 목적은, CPU Core가 처리하는 프로세스를 빈번하게 교체해서, **사용자로 하여금 동시에 작업하고 있다고 느끼게 만드는 것**이다.

## 3.2.1 Scheduling Queue

- 앞서 Process의 state에 대해서 봤다싶이, 프로세스가 생성되면 `Ready`상태로 들어가게 된다. 좀 더 자세히 이야기하자면,
**Ready Queue**에 들어가서 준비 상태가 되는 것이다.

- 만약, 특정한 I/O 이벤트나 신호를 기다리는 프로세스라면, *Wait Queue*에 들어가게 된다.

> 이러한 Queue들은 PCB의 LinkedList로 구현된다.

![](https://velog.velcdn.com/images/calzone0404/post/8292aa88-1a7d-4955-941a-2c1a3dd82973/image.png)

---

프로세스 스케쥴링의 일반적인 표현은 아래 그림과 같은 **큐잉 다이어그램**으로 표현된다.

![](https://velog.velcdn.com/images/calzone0404/post/348e5e72-2d77-4449-a4c6-79d44de2320a/image.png)

- 새 프로세스가 Ready queue에 놓인다.
- 해당 프로세스가 CPU에 선택되거나, dispatch될때까지 기다린다.
- 만약 해당 프로세스가 CPU에 선택되어서 CPU Core가 할당되고, 실행 상태가 되면, 여러 이벤트가 발생할 수 있다.
(I/O 요청으로 인한 waiting queue, 할당 시간 종료로 인한 해제, Child process 생성, 인터럽트 대기 등..)

## 3.2.3 Context Switch

> Context는 CPU 레지스터의 값, Process state, 메모리 관리 정보를 포함하는 정보이다.
Process의 context가 바로 PCB에 저장되는 정보이다.

>**Context Switch** : 현재 프로세스가 실행중인 상태를 저장하고, 새로운 프로세스로 교체하는 것.

만약 새로운 Process로 교체하라는 Interrupt가 발생한다면,

![](https://velog.velcdn.com/images/calzone0404/post/2983a714-f06e-4213-b60e-0ac0bf2dc4ee/image.png)

1. CPU Core를 다른 Process에게 할당한다.
2. 방금까지 실행했던 Process의 현재 상태를 저장해놓는다. (PCB정보를 저장한다)
3. 다른 Process의 PCB정보를 읽어와서 상태를 복구한다. (마지막에 실행했던 지점으로 복구된다.)


# 3.3 Operation on Processes

운영체제는 다음과 같은 기능을 제공한다.

**1. 새로운 프로세스 생성**
**2. 프로세스 종료**

## 3.3.1 Process Creation

> 어떤 프로세스는 자신의 여러개의 자식 프로세스를 가질 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/5cd5f74b-4217-40f8-87db-3334fe311048/image.png)

- 주로 C의 systemcall함수 `fork()`를 통해 자식 프로세스를 생성한다.

주로 새로 생성한 프로세스를 `Parent process`라고 부른다.
Parent process로부터 생성된 프로세스를 `Child process`라고 부른다.

이러한 프로세스들은 두가지 경우로 실행된다.

1. 부모와 자식 프로세스가 동시에 계속 실행되는 상태
2. 자식 프로세스가 끝날때까지 부모는 대기하는 상태

또한, 메모리 할당에 대해서도 두가지 경우가 존재한다.

1. 자식 프로세스가 부모 프로세스의 정보를 복제해서 메모리를 할당받는 경우
2. 자식 프로세스가 새로운 프로그램을 실행해서 메모리를 할당받는 경우


## 3.3.2 Process Termination

> 마지막 코드(statement)까지 실행을 끝낸 경우, 프로세스는 종료된다.

- 주로 C의 systemcall 함수 `exit()`을 통해 OS에게 해당 프로세스를 삭제하라고 호출한다.
- OS는 해당 프로세스에 할당한 모든 자원을 해제해버린다.

### Zombie and Orphan

- Zombie Process : 자식 프로세스가 종료됐지만 부모 프로세스가 자식 프로세스의 종료 상태를 호출하지 않은 경우(`wait()`을 부모가 호출하지 않은 경우)
- Orphan Process : 부모 프로세스가 자식 프로세스보다 먼저 종료되는 경우 (자식 프로세스는 계속 실행되는 상태인 경우)

## 예제를 통한 이해

### UNIX기반 운영체제의 C systemcall 함수

> **fork()** : 새로운 프로세스를 생성하는 함수.
이 함수를 호출하게 되면, 자식 프로세스는 부모 프로세스가 할당한 메모리 내용을 복사해서 새롭운 메모리로 할당된다.

- fork()의 반환하는 곳이 자식 프로세스인 경우 `0`이고, 0이 아닌 pid값이 반환된다면 child process의 실제 pid값이 부모 프로세스로 반환된다.

### 예제 1.
```c
#include <stdio.h> // 표준 입출력 함수를 사용하기 위한 헤더 파일
#include <unistd.h> // fork() 함수 등을 사용하기 위한 헤더 파일

int main() { // 프로그램의 진입점, 부모 프로세스가 실행됨
    pid_t pid; // 프로세스 ID를 저장할 변수 선언
    pid = fork(); // fork() 호출을 통해 자식 프로세스를 생성. 반환 값은 pid 변수에 저장됨

    // fork()는 부모 프로세스와 자식 프로세스 모두에서 반환됩니다.
    // 부모 프로세스에서는 자식의 PID를 반환하고, 자식 프로세스에서는 0을 반환합니다.

    printf("Hello process! %d\n", pid); // fork()의 반환 값인 pid를 출력
    // 부모 프로세스와 자식 프로세스가 각각 이 줄을 실행하게 됨
    // 부모 프로세스에서는 자식의 PID가 출력되고, 자식 프로세스에서는 0이 출력됨
}
```

### 예제 2.
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    pid = fork();

    if (pid > 0) // 자식 프로세스가 정상적으로 생성되었다면, 부모 프로세스는 자식 프로세스가 끝날 때까지 대기
        wait(NULL);

    // 자식 프로세스는 pid가 0이고, 부모 프로세스는 자식 프로세스의 실제 pid를 출력
    printf("Hello, Process! %d\n", pid);

    // 부모 프로세스는 wait 후에 이 줄을 실행하게 되므로 자식 프로세스의 실제 pid를 출력
    // 자식 프로세스는 pid가 0을 출력하고 종료
}
```

### 예제 3.
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int value = 5; // 전역 변수 선언

int main()
{
    pid_t pid;
    pid = fork();
    
    if (pid == 0) { // 자식 프로세스
        value += 15; // 자식 프로세스는 value 값을 15 증가시킴
        return 0; // 자식 프로세스 종료
    }
    else if (pid > 0) { // 부모 프로세스
        wait(NULL); // 자식 프로세스가 끝날 때까지 대기
        printf("Parent: value = %d\n", value); // 부모 프로세스에서 value 값을 출력
    }
}
```

### 예제 4.
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

/*
* How many processes are created?
*/
int main() // 일단 부모 프로세스가 생성됨 (1개)
{
    fork(); // 첫 번째 fork() 호출: 부모 프로세스가 자식 프로세스를 생성함 (2개)
    fork(); // 두 번째 fork() 호출: 각 프로세스(부모와 자식)가 다시 자식 프로세스를 생성함 (4개)
    fork(); // 세 번째 fork() 호출: 각 프로세스가 다시 자식 프로세스를 생성함 (8개)
    // 따라서, 총 8개의 프로세스가 생성된다.
    return 0;
}

```

### 예제 5.
```c
#include <stdio.h>
#include <unistd.h>
/*
* How many processes are created?
*/
int main()
{
	int i;
	for (i = 0; i < 4; i++)
		fork(); // 예제 4에서와 마찬가지로, 4회 fork()하면 16개 생성됨
	return 0;
}
```

### 예제 6.

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
    pid_t pid;
    pid = fork();
    if (pid == 0) { // 자식 프로세스인 경우
        execlp("/bin/ls", "ls", NULL); // "/bin/ls" 파일의 ls 명령을 실행. 성공하면 프로세스 이미지가 교체되어 아래 코드가 실행되지 않음.
        // execlp가 성공적으로 호출되면 현재 프로세스 이미지가 "/bin/ls"로 대체되므로 다음 라인은 실행되지 않음
        printf("LINE J\n"); // execlp 호출이 실패한 경우에만 실행됨
    }
    else if (pid > 0) { // 부모 프로세스인 경우
        wait(NULL); // 자식 프로세스가 끝날 때까지 대기
        printf("Child Complete\n"); // 자식 프로세스가 완료된 후 출력
    }
    return 0;
}
```

![](https://velog.velcdn.com/images/calzone0404/post/fc53ac87-37bc-4a64-a822-81dbbf30d856/image.png)

### 예제 7.
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
    pid_t pid, pid1;
    pid = fork(); // 새로운 프로세스를 생성
    
    if (pid == 0) { // 자식 프로세스
        pid1 = getpid(); // 자식 프로세스의 PID를 가져옴
        printf("child: pid = %d\n", pid); // A: pid는 fork()의 반환 값으로, 자식 프로세스에서는 0
        printf("child: pid1 = %d\n", pid1); // B: 자식 프로세스의 실제 PID 출력
    }
    else if (pid > 0) { // 부모 프로세스
        pid1 = getpid(); // 부모 프로세스의 PID를 가져옴
        printf("parent: pid = %d\n", pid); // C: pid는 자식 프로세스의 PID
        printf("parent: pid1 = %d\n", pid1); // D: 부모 프로세스의 실제 PID 출력
        wait(NULL); // 자식 프로세스가 종료될 때까지 대기
    }
    return 0;
}

```

> wait()이 아래에 있어서 부모가 먼저 실행되고, 자식이 실행된다.

![](https://velog.velcdn.com/images/calzone0404/post/970e04b6-e50a-4f19-aad5-1238a6e2e603/image.png)

### 예제 8.
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

#define SIZE 5
int nums[SIZE] = {0, 1, 2, 3, 4}; // 전역 배열 선언 및 초기화

int main()
{
    pid_t pid;
    int i;
    pid = fork();

    if (pid == 0) { // 자식 프로세스
        for (i = 0; i < SIZE; i++) {
            nums[i] *= i; // 자식 프로세스에서 배열의 각 요소를 해당 인덱스 값으로 곱함
            printf("CHILD: %d \n", nums[i]); // 변경된 값을 출력
        }
    }
    else if (pid > 0) { // 부모 프로세스
        wait(NULL); // 자식 프로세스가 종료될 때까지 대기
        for (i = 0; i < SIZE; i++) {
            printf("PARENT: %d \n", nums[i]); // 부모 프로세스는 초기 값 출력 (0, 1, 2, 3, 4)
        }
    }

    return 0;
}
```

![](https://velog.velcdn.com/images/calzone0404/post/62876831-ef78-4701-9b1f-df236c5351f0/image.png)

#### 자식 프로세스에서 변경한 배열의 내용이 부모에게 반영되지 않는 이유

1. int nums[SIZE] = {0, 1, 2, 3, 4}로 전역 배열 nums가 초기화됨. 부모 프로세스와 자식 프로세스는 이 배열의 복사본을 각각 가지고 있음

2. fork()를 호출해서 새로운 프로세스를 생성함.

3. 자식 프로세스는 nums 배열의 각 요소를 해당 인덱스로 곱하고 그 결과를 출력함.
이때 자식 프로세스는 자신만의 메모리 공간에서 배열의 복사본에다만 수정작업을 함. 따라서 부모 프로세스의 배열에는 영향을 미치지 않는것임

4. 부모 프로세스는 wait(NULL)을 호출하여 자식 프로세스가 종료될 때까지 대기하고, 자식이 종료되면 부모 프로세스는 자신의 nums 배열을 그대로 유지하고, 초기 값을 출력함.


# 3.4 Interprocess Communication

## Processes executing concurrently

동시에 실행되는 프로세스의 종류로는 서로 **독립적(independent)**으로 실행되거나, **함께 상호작용(cooperating)** 하면서 실행되는 경우가 있다.

> **Independent processes** : 다른 프로세스와 데이터를 공유하지 않는 경우에 해당
**Cooperating processes** : 다른 프로세스에 의해 영향을 받거나, 다른 프로세스에 영향을 주는 프로세스들.
즉, cooperating processes간 **데이터의 공유가 일어남**

> `IPC` : Inter-Process Communication
주로 Cooperating process들이 서로 데이터를 주고받기 위해 사용하는 방식이다.

IPC의 종류로는 2가지가 존재한다.

![](https://velog.velcdn.com/images/calzone0404/post/de198230-f13d-4440-ad11-3277832baefb/image.png)

### 1. Shared memory 방식
공유 메모리 방식은 서로 공통된 메모리 (공유 메모리)를 이용해서 전달할 또는 전달받을 메세지를 교환하는 방법이다.

### 2. Message passing 방식
메세지 전달 방식은 **Message queue**를 이용해서 전달할 또는 전달받을 메세지를 교환하는 방법이다.

#### Google Chrome에 대해서

우리가 아주 많이 사용하는 프로그램으로, **Chrome browser**가 있다.

크롬은 여러 프로세스로 구성되어, 각 탭을 **독립적**으로 실행해서 하나의 프로세스가 웹 어플리케이션으로 인해 **Crash**되어도 다른 프로세스에는 영향을 주지 않도록 설계되어있다. (필자는 아래 10개의 크롬 탭을 사용하고 있다)

![](https://velog.velcdn.com/images/calzone0404/post/a4e35512-b489-479d-8b85-847883bf4af5/image.png)

하지만 실제 세부정보를 확인해보면 더 많은 프로세스가 작동하는것을 볼 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/d4a84d2f-16cc-4c56-ac63-db67c2bf6b42/image.png)

그 이유는 크롬 브라우저 프로세스 이외에도 추가적인 프로세스가 존재하기 때문이다.

크롬의 프로세스 유형으로는
- Browser process : UI관리 및 I/O작업을 담당하는 프로세스이며, 크롬 실행 시 하나만 생성된다.

- Renderer process : 웹 페이지 rendering을 담당하며, 각 탭마다 **새로 생성된다**

- Plugin process : 크롬을 사용하면, 확장 프로그램을 설치하여 사용하는 경우가 존재하는데, 사용자가 사용중인 각 플러그인에 대해 별도로 생성된다.

---

> 결론적으로, 크롬은 프로세스 독립성을 가지고 있기 때문에 하나의 웹사이트를 보여주는 탭에서 크래시가 발생하더라도 다른 탭에는 영향을 주지 않으며, 
>
>각 Renderer process는 **Sandbox**라는 독립된 환경에서 실행되기 때문에 보안적으로 강화된 형태이다

# 3.5 IPC in Shared-Memory Systems

## Producer-Consumer Problem

생산자-소비자 문제는 Cooperating process들을 표현하기 위해 예시로 든 것이다.

> Producer는 Consumer가 소비할 정보를 생산하고, Consumer는 Producer가 생산한 정보를 소비한다.

예를 들어, Compiler(Producer)가 Assembly code를 생산하면, Assembler(Consumer)가 해당 코드를 사용한다.

또 다른 예로, Web Serber(Producer)가 HTML file를 제공하면, Browser(Consumer)가 해당 파일을 사용한다.

## Shared Memory System

앞서 서술한것과 같이, 공유 메모리 시스템은 Producer와 Consumer가 **concurrently(동시에)**동작하면서 `Shared memory`를 사용하여 통신한다.

`Buffer` : 이용 가능한 item

Producer는 shared memory buffer를 채워넣음으로써 자신이 생산한 자원을 전달하고, Consumer는 shared memory buffer를 확인하여 자신이 사용할 데이터를 가져와서 소비한다.

이때, 만약 buffer가 가득 찬 경우, Producer는 더이상 추가 데이터를 넣을 수 없으므로 Wait상태가 되며, 
반대로 buffer가 비어 있는 경우 Consumer는 가져올 데이터가 존재하지 않기 때문에 Wait 상태가 된다.

## Shared Memory System Example

```c
 #define BUFFER SIZE 10
 typedef struct {
 	. . .
 } item;
 item buffer[BUFFER_SIZE];
 // 공유 메모리 역할을 하는 구조체 `item`을 선언하고, 
 // 구조체의 크기를 정해서 구조체 배열을 생성
 // Circular Queue 형태로 생각하면 된다.
 
 int in = 0; // Producer가 push를 수행할 index
 int out = 0; // Consumer가 pop을 수행할 index
```

위와 같이 공유 메모리가 구성되어 있다면, Producer는 다음과 같이 사용할 수 있다.

```c
item next_produced;

while(true){
	// next_produced에 생성한 데이터를 공유 메모리에 넣는다.
    while((in + 1) % BUFFER_SIZE == out){
    	//만약 Modulo 연산을 통한 in 위치가 out 위치와 동일한 경우 (Circular Queue 형태이므로 다음 넣을 index를 modulo 연산으로 구한다)
        do nothing! // 자리가 생길때까지 대기한다
    }
    
    // 자리가 남아있으면
    buffer[in] = next_produced;
    in = (in + 1) % BUFFER_SIZE;
}
```

Consumer가 사용하는 방법은 다음과 같다.
```c
item next_consumed;

while(true){
	while(in == out){ // 만약 Queue에 아무것도 없다면
    	do nothing!
    }
    
    next_consumed = buffer[out];
    // 데이터를 가져오고, index를 Modulo 연산을 통해 증가시킨다.
    out = (out + 1) % BUFFER_SIZE;
}
```

## Shared Memory 방식의 단점

Shared Memory 사용 방식은 프로세스들이 메모리의 한 영역을 공유해야 한다. 

- 프로그래머가 **직접 사용할 공간을 명시**해줘서 메모리를 할당해야 한다.

- 프로세스들이 해당 메모리 공간을 공유해서 사용할 수 있도록 직접 코드를 작성해야한다.

> 즉, 프로그래머가 해야할 일이 너무 많으며, 복잡해진다.

# 3.6 IPC in Message-Passing Systems

> Message Passing 방식은 Shared Memory 방식에 비해, O/S에서 Systemcall 함수를 지원해주기 때문에, 단지 프로그래머는 이 API 함수를 사용해주기만 하면 된다!

주로 다음과 같은 형태이다
- send(message) : 메세지 전달
- receive(message) : 메세지 수신

## Message passing 예시

### Producer
```c
message next_produced;

while (true){
	send(next_produced); 
    // 그냥 Systemcall을 사용하여 메세지만 전달해주면
    // 나머지는 OS에서 알아서 처리한다.
}
```

### Consumer
```c
message next_consumed;

while(true){
	receive(next_consumed); 
    // 위와 마찬가지로 그냥 받는 함수만 사용해주면 알아서 받아준다.
}
```
## Communication Links

> 두 개의 프로세스 P와 Q가 서로 통신을 한다는 것은, **각자 서로 메시지를 send하거나, receive 한다는 것이다.**

이러한 통신은 Communication Link를 통해 수행되며, Communication Link의 구현 종류는 여러 가지가 있다.

### 1. Direct or Indirect Communication

#### Direct Communication

이 방법은 각각의 프로세스(P와 Q)가 서로 통신하고자 할 때, 서로 통신할 **상대방 프로세스**를 명시하여 정하는 방식이다.

즉, 다음과 같이 설정한다.
```c
send(P, message) // 프로세스 P에게 메시지를 보낸다.
receive(Q, message) // Q로부터 메시지를 전달받는다.
```

직접 통신 방식의 특징은 다음과 같다:

- Link가 자동으로 설정된다.
- 하나의 Link는 오직 두 개의 프로세스를 연결하는 데만 사용된다.

#### Indirect Communication

이 방법은 **MAILBOX** 또는 **PORT** (둘다 같은 말)를 사용하여 메시지를 전달하고, 전달받는 방식이다.

PORT는 우리가 잘 알고 있는 PORT NUMBER로 생각할 수 있다.

프로세스들이 전달할 데이터가 Port에 전달되고, 
전달받을 데이터는 Port로부터 받아오게 된다.

```c
send(PORT_NUMBER, message) // 해당 포트로 메시지를 전송한다.
receive(PORT_NUMBER, message) // 해당 포트에서 메시지를 받는다.
```

간접 통신 방식의 특징은 다음과 같다:

- Port를 통해 간접적으로 메세지를 전달 및 수신한다.
- 하나의 Port에 여러 프로세스가 연결될 수 있다.
- 프로세스는 Port를 통해 메세지를 주고받기 때문에 상대방 프로세스를 명시할 필요가 없다.

### 2. Synchronous or Asynchronous Communication

#### Synchronous Communication

동기식 통신은 송신자가 메시지를 보낸 후, 수신자가 해당 메시지를 받을 때까지 기다리는 방식이다. **Blocking send/receive**라고도 불리며, 이 방식은 프로세스 간의 시간 동기화를 보장한다. 

```c
send(P, message) // 메시지를 보내고, 수신자가 받을 때까지 기다린다.
receive(Q, message) // 메시지를 받고, 송신자가 기다리는 동안 확인한다.
```

동기식 통신의 특징은 다음과 같다:

- 송신자는 수신자가 메시지를 받을 때까지 대기한다.
- 메시지 전달의 **정확성과 순서**를 보장한다.

#### Asynchronous Communication

비동기식 통신은 송신자가 메시지를 보낸 후, 수신자가 메시지를 받을 때까지 기다리지 않고 바로 다음 작업을 수행하는 방식이다. **Non-blocking send/receive**라고도 불린다.

```c
send(P, message) // 메시지를 보내고, 기다리지 않고 다음 작업을 수행한다.
receive(Q, message) // 메시지를 비동기적으로 수신한다.
```

비동기식 통신의 특징은 다음과 같다:

- 송신자가 메시지를 보낸 후 대기하지 않는다.
- 메시지 전달의 순서를 보장하지 않을 수 있다.

### 3. Persistent or Transient Communication

#### Persistent Communication (지속적 통신)

지속적 통신은 메시지가 수신될 때까지 시스템에서 유지되는 방식이다. 메시지는 수신자가 **준비되지 않은 상태에서도 손실되지 않고 보존**된다.

#### Transient Communication (일시적 통신)

일시적 통신은 송신자가 메시지를 보낼 때, 수신자가 **준비되지 않으면 메시지가 손실**되는 방식이다. 메시지는 수신자가 즉시 받을 준비가 된 상태에서만 전달된다.

### 4. Automatic or Explicit Buffering

#### Automatic Buffering

자동 버퍼링은 운영체제나 통신 시스템이 메시지를 임시로 저장할 **버퍼를 자동으로 관리**하는 방식이다. 프로세스는 버퍼의 크기와 메시지의 저장 및 전달이 자동으로 관리되기 때문에, 버퍼링의 세부 사항을 신경 쓰지 않아도 된다.

```c
send(Q, message) // 운영체제가 버퍼를 자동으로 관리하며 메시지를 전송한다.
receive(P, message) // 메시지를 자동 버퍼에서 받아온다.
```

자동 버퍼링의 특징은 다음과 같다:

- 버퍼링 관리가 자동으로 이루어지므로 개발자가 버퍼의 크기나 메시지 저장 위치를 명시할 필요가 없다.
- 버퍼 오버플로우를 방지하기 위해 운영체제가 적절한 크기의 버퍼를 할당한다.
- 시스템에 의해 자동으로 처리되므로 구현이 간단하다.

#### Explicit Buffering

명시적 버퍼링은 **프로세스가 메시지를 임시로 저장할 버퍼를 직접 관리**하는 방식이다. **개발자는 버퍼의 크기와 메시지의 저장 및 전달 방법을 명시적으로 지정**해야 한다.

```c
char buffer[BUFFER_SIZE]; // 버퍼 크기를 명시적으로 설정
send_to_buffer(buffer, message); // 버퍼에 메시지를 저장
receive_from_buffer(buffer, message); // 버퍼에서 메시지를 가져옴
```

명시적 버퍼링의 특징은 다음과 같다:

- 개발자가 버퍼의 크기와 메시지의 저장 위치를 명시적으로 관리한다.
- 버퍼 오버플로우를 방지하기 위해 개발자가 직접 버퍼 크기를 관리해야 한다.
- 더 복잡한 구현이 요구되지만, 특정 요구사항에 맞춘 최적화가 가능하다.

# 3.7 Examples of IPC Systems

- Shared Memory를 사용하는 IPC 시스템 : **POSIX(Portable Operating System Interface for UNIX)**
- Message Passing을 사용하는 IPC 시스템 : **Pipes**

## POSIX shared memory

POSIX는 프로세스들이 메모리 영역을 공유해서 서로 통신하게 된다. 주로, 다음 과정을 통해 구현된다.

### 1. 공유 메모리 객체 생성
C 라이브러리에서 지원하는 `shm_open` 시스템콜 함수를 사용해서 공유 메모리 객체를 생성하고, file descriptor를 반환받는다.

File descripter : 공유 메모리에 대한 추상적인 참조를 나타내는 포인터라고 생각하면 된다.

```c
int shm_fd = shm_open(name, O_CREAT | ORDWR, 0666);
```

- `name` : 공유 메모리 객체의 이름을 나타내는 문자열. 즉, 공유 메모리 객체를 식별할 때 사용된다.
- `O_CREAT | ORDWR` : 이렇게 생긴 매개변수는 `oflag`라고 하는 플래그인데, 공유 메모리 객체를 어떻게 열지 결정하는 것으로 생각하면 된다. **OR**연산자로 결합되어 있으므로, 다음과 같이 읽을 수 있다.

  - O_CREAT : 공유 메모리 객체가 존재하지 않으면 새로 생성한다
  - O_RDWR : 읽기 및 쓰기 모드로 공유 메모리 객체를 연다.
  따라서, 공유 메모리 객체가 없으면 만들고, 있다면 해당 객체를 연다는 의미로 읽으면 된다.
  
- 0666 : Linux에서 사용하는 파일 접근 권한이다. rwx/rwx/rwx에서, rw/rw/rw만 사용한다는 의미이다.

### 2. 크기 설정

`ftruncate` 함수를 사용해서 공유 메모리 객체의 크기를 결정한다.
```c
ftruncate(shm_fd, 4096); // shm_fd의 크기를 4096 bytes로 설정한다
```

### 3. 메모리 매핑 파일 설정

`mmap` 함수를 사용해서 공유 메모리 객체를 프로세스의 주소 공간에 매핑하게 된다.

```c
char *ptr = (char *)mmap(0, SIZE, PROT_READ | PROT_WRITE, MAP_SHARED, shm_fd, 0);
```

- 0: 매핑할 주소를 시스템이 자동으로 선택하도록 함
- SIZE: 매핑할 크기
- PROT_READ | PROT_WRITE: 읽기 및 쓰기 권한 설정
- MAP_SHARED: 다른 프로세스와 메모리를 공유
- shm_fd: 매핑할 파일 설명 데이터 (file description)
- 0: 파일 오프셋

### 4. 전체 코드

#### Shared memory producer
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <fcntl.h>
#include <sys/mman.h>
#include <unistd.h>


int main(){
    const int SIZE = 4096;
    const char *name = "OS";
    const char *message_0 = "Hello, ";
    const char *message_1 = "Shared Memory!\n";

    int shm_fd; // 공유 메모리 객체를 위한 파일 설명자
    char *ptr; // 공유 메모리 영역을 가리키는 포인터

    // shm_open() : 공유 메모리 객체를 생성하거나 열기 위해 사용
    shm_fd = shm_open(name, O_CREAT | O_RDWR, 0666);
    /*
     * name: 공유 메모리 객체의 이름
     * O_CREAT | O_RDWR: 객체를 생성하고 읽기/쓰기 모드로 열기 위한 플래그
     * 0666: 파일 권한 설정 (읽기 및 쓰기 권한 부여)
     */

    // ftruncate() : 공유 메모리 객체의 크기를 SIZE로 설정 (4096 바이트)
    ftruncate(shm_fd, SIZE);

    // mmap : 공유 메모리 객체를 프로세스의 주소 공간에 매핑
    ptr = (char *)mmap(0, SIZE, PROT_READ | PROT_WRITE, MAP_SHARED, shm_fd, 0);
    /*
     * 0: 매핑할 주소를 시스템이 자동으로 선택하도록 함
     * SIZE: 매핑할 크기
     * PROT_READ | PROT_WRITE: 읽기 및 쓰기 권한 설정
     * MAP_SHARED: 다른 프로세스와 메모리를 공유
     * shm_fd: 매핑할 파일 설명 데이터 (file description)
     * 0: 파일 오프셋
     */

    // sprintf를 이용해서 공유 메모리 영역에 message_0와 message_1을 순차적으로 씀
    sprintf(ptr, "%s", message_0);
    ptr += strlen(message_0); // 첫 번째 메시지의 길이만큼 포인터를 이동
    sprintf(ptr, "%s", message_1);
    ptr += strlen(message_1); // 두 번째 메시지의 길이만큼 포인터를 이동

    return 0;
}
```

#### Shared memory consumer
```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <sys/mman.h>

int main(){
    const int SIZE = 4096;
    const char *name = "OS";

    int shm_fd;
    char *ptr;

    // 공유 메모리 객체를 읽기 전용 모드로 염
    shm_fd = shm_open(name, O_RDONLY, 0666);
    // O_RDONLY: 읽기 전용 모드로 열기 위한 플래그

    ptr = (char *)mmap(0, SIZE, PROT_READ, MAP_SHARED, shm_fd, 0);

    printf("%s", (char*)ptr);
    // 공유 메모리에서 읽은 문자열을 표준 출력(콘솔)에 출력

    // 공유 메모리 객체를 시스템에서 삭제
    shm_unlink(name);
    
    return 0;
}
```

#### 실행 결과

![](https://velog.velcdn.com/images/calzone0404/post/b0c4afd9-9e4c-4d5f-b33b-979581286539/image.png)

---

## Pipes

### Ordinary pipes

일반 파이프는 두 프로세스가 Producer-Consumer 방식으로 통신할 수 있게 한다.
즉, 생산자는 파이프의 한쪽 끝에서 데이터를 Write 하고, 소비자는 다른쪽에서 Read한다.

이 방법은 외부 프로세스와 통신하는 것이 아니라, 일반적으로 자신의 Child process들과 통신하기 위해 사용한다.

![](https://velog.velcdn.com/images/calzone0404/post/c32a7232-b028-4a75-bf17-685de9d8070f/image.png)

위 그림처럼 한번 통신할 때, 한 방향으로만 통신이 가능한 **Unidirectional** 특징을 가지고 있다.
만약, 두개 이상의 communication을 하고싶다면, 두개 이상의 pipe를 생성해야한다.

### 파이프 구현 방법

Ordinary pipe를 구현하기 위해서는, Unix 시스템에서 지원하는 Systemcall 함수를 사용하면 된다.

```c
int fd[2];
pipe(fd);
```

- fd[0] : the read end of the pipe
- fd[1] : the write end of the pipe
- pipe() : 파이프를 생성하고, 인자로 read/write end 파일 디스크립터 배열을 받는다.

### Example
```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

#define BUFFER_SIZE 25
#define READ_END 0
#define WRITE_END 1

int main() {
    char write_msg[BUFFER_SIZE] = "Greetings"; // 부모 프로세스가 보낼 메세지
    char read_msg[BUFFER_SIZE]; // 자식 프로세스가 읽을 메세지
    int fd[2]; // Pipe file descriptor
    pid_t pid; // 프로세스 id

    pipe(fd);
    pid = fork(); // 자식 프로세스 생성
    // fork 하는 순간 parent와 child가 동시에 실행된다.

    if (pid > 0) { // 부모 프로세스에서는
        close(fd[READ_END]); // 읽지 않으므로 닫아버림
        write(fd[WRITE_END], write_msg, strlen(write_msg) + 1);
        // 파이프에 메세지를 작성하고, 닫아버림
        close(fd[WRITE_END]);
    } else if (pid == 0) { // 자식 프로세스에서는
        close(fd[WRITE_END]); // 쓰지 않으므로 닫아버림
        read(fd[READ_END], read_msg, BUFFER_SIZE);
        // 파이프에 전달된 메세지를 읽고, 닫아버림
        printf("read %s\n", read_msg);
        close(fd[READ_END]);
    }

    return 0;
}
```

### 결과
![](https://velog.velcdn.com/images/calzone0404/post/3b010a2e-7376-4f2f-8d67-260e707f3ccc/image.png)

### 실제 파이프를 사용하는 예시

파이프는 UNIX 명령줄 환경에서 한 명령의 출력을 다른 명령의 입력으로 사용하는 상황에서 자주 사용된다.

예를 들어, UNIX의 `ls` 명령은 디렉토리 목록을 생성하는 명령이다. 
만약, ls 명령의 출력이 매우 길다면, 스크롤을 여러번 해야 모든 내용을 볼 수 있다. 

`less` 명령은 출력을 한 번에 한 화면씩만 표시하여 사용자가 특정 키를 사용하여 파일 내에서 앞뒤로 이동할 수 있도록하는 명령이다.

이때, `ls`와 `less` 명령(개별 프로세스로 실행되는) 사이에 파이프를 설정하면 
**`ls`의 출력이 `less`의 입력으로 전달**되어 사용자가 큰 디렉토리 목록을 한 번에 한 화면씩 표시할 수 있게 된다.

```bash
ls | less
```

# 3.8 Communication in Client-Server Systems

앞서 배운 내용은, 로컬 컴퓨터에서 일어나는 프로세스들간의 통신 방식에 대해 서술한 것이다.

여기에서 배울 내용은 이제 네트워크를 통해 일어나는 프로세스들간의 통신 방식에 대해서이다.

서버와 클라이언트 프로세스간의 통신 방식은 두가지 존재한다.

1. Socket 통신 방식
2. RPC 통신 방식

## Socket communication

> Socket은 서버와 클라이언트가 IP주소와 Port번호가 합성된 것을 통해 서로를 식별한다.

### Server implementation with Java

```java
import java.net.*;
import java.io.*;

public class DateServer {
    public static void main(String[] args) {
        try {
            ServerSocket sock = new ServerSocket(6013);
            // 6013번 포트를 열고, 서버를 생성한다.
            // Client가 연결될때까지 while문을 돌게된다.
            while (true) {
            	//client가 연결되면 연결을 수락하고,
                Socket client = sock.accept();
                PrintWriter pout = new PrintWriter(client.getOutputStream(), true);
                // client로 보낼 outputstream에 현재 날짜를 담아서 전송한다.
                pout.println(new java.util.Date().toString());
                // 소켓을 닫고, 다시 연결을 대기한다.
                client.close();
            }
        } catch (IOException ioe) {
            System.err.println(ioe);
        }
    }
}
```

### Client implementation with Client

```java
import java.net.*;
import java.io.*;

public class DateClient {
    public static void main(String[] args) {
        try {
            // 서버에 6013번 포트로 연결한다.
            Socket sock = new Socket("127.0.0.1", 6013);
            // 나에게 들어오는 Stream에 대한 인스턴스 생성
            InputStream in = sock.getInputStream();
            // BufferedReader에 날라온 데이터를 담아준다.
            BufferedReader bin = new BufferedReader(new InputStreamReader(in));
            
            // 서버에서 날라온 데이터(현재 날짜)를 읽는다
            String line;
            while ((line = bin.readLine()) != null) {
                System.out.println(line);
            }
            
            // 소켓 연결을 종료해준다.
            sock.close();
        } catch (IOException ioe) {
            System.err.println(ioe);
        }
    }
}
```

## Remote Procedure Call (RPC)

> RPC는 네트워크 연결을 통해 원격 시스템 간의 함수 호출 메커니즘을 추상화하기 위해 설계된 원격 서비스이다.

따라서 클라이언트가 로컬에서 함수를 호출하는 것처럼 원격 호스트에서 함수를 호출할 수 있게 된다.

> **Stub** :  클라이언트와 서버 간의 통신 세부 사항을 숨기기 위한 클라이언트 측과 서버 측의 구성요소

- Client측의 `stub`은 Server를 찾아서 매개변수를 `marshalling`한다.

> Marshalling : 매개변수를 전송 가능한 형식으로 변환하는 것

- Server측의 `stub`은 Client로부터 메세지를 수신하고, Marshallized된 매개변수를 unmarshalling한 후, 서버에서 함수를 호출해서 실행한다.

### RPC 동작 방식
- Client측에서 Local 함수를 호출하는것처럼, Server에 존재하는 함수(Procedure)를 호출한다.

- Client측의 `stub`은 호출된 함수와 매개변수를 packing해서 서버측으로 전달한다.

- Server측의 `stub`은 패킹된 요청을 수신해서 unpacking후, 메세지를 확인한다. 이후, 메세지에 들어있는 함수를 찾아서 호출한다.

- 함수가 실행되고, 결과가 Server측의 `stub`으로 반환되는데, Server는 이 결과를 Client측으로 다시 전달해준다.

- Client stub은 결과를 수신해서 Client application에게 해당 결과를 전달해준다.
