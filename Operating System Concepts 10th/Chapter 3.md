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

> IPC : Inter-Process Communication

IPC란, Co-operating process들에서 사용하는 메커니즘으로, Process들이 서로 데이터를 주고받는 것

# 3.5 IPC in Shared-Memory Systems

# 3.6 IPC in Message-Passing Systems

# 3.7 Examples of IPC Systems
## POSIX shared memory

### Shared memory producer
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

### Shared memory consumer
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

### Run shm sources

![](https://velog.velcdn.com/images/calzone0404/post/1471f14f-c3a1-4721-96d5-7f4042cce982/image.png)

# 3.8 Communication in Client-Server Systems
