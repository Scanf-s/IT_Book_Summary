> 이 게시글은 **Operating Systems: Three Easy Pieces**를 읽고 정리한 글 입니다.

# Introduction to OS

## 프로그램 실행 시 무슨 일이 일어나는가

> 실행중인 프로그램 : 단순히 명령어를 실행할 뿐

### 폰 노이만 모델의 기본적인 설명

- 오늘날의 Processor는 메모리로부터 명령어들을 가져와서 해석하고, 실행하는 작업을 수행한다.
- 하나의 명령을 마치면 다음 명령어로 이동하는 식으로 계속해서 수행하는데, 프로그램이 종료될 때 까지 반복한다.

### Operating system

> 프로그램 실행을 쉽게 해주고, 여러 프로그램을 동시에 실행할 수 있게 해주며, 프로그램들의 메모리 공유 및 장치와 상호작용할 수 있도록 도와주는 Software

### Virtualization

- 위에서 설명한 운영체제가 하는 작업을 수행하기 위해서는 **가상화**라는 기술을 사용해야한다.

- OS는 물리적인 Hardware를 추상화해준다.

- OS는 사용자가 OS에게 명령을 내리거나 몇몇 OS의 기능을 사용하기 위한 API(인터페이스)를 제공한다. 이러한 인터페이스를 **시스템 콜** 이라고 부른다.

- 시스템 콜 함수를 통해서 프로그램 실행, 메모리 및 장치 접근, 관련 작업 수행이 가능해진다.

> 가상화 덕분에 사용자 입장에서 여러개의 프로그램이 실행되는 것처럼 보이게 된다. (CPU Virtualization)
또한, 여러 프로그램이 각자 명령과 데이터를 동시에 접근할 수 있다. (Memory Virtualization)

**이러한 이유로 OS를 자원 관리자(resource manager)라고 부르기도 한다.**

## 2.1 CPU 가상화

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/time.h>
#include <assert.h>
#include "common.h"

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "usage: cpu <string>\n");
        exit(1);
    }
    char *str = argv[1];
    while (1) {
        Spin(1);
        printf("%s\n", str);
    }
    return 0;
}
```

이 C코드는 단순히 Spin() 함수를 호출하는 함수이다. 반복적으로 1초마다 특정 문자(argv로 집어넣은)를 출력한다.

이를 실행하면 다음과 같이 출력된다.
```shell
prompt> gcc -o cpu cpu.c -Wall
prompt> ./cpu "A"
A
A
A
A
^C
prompt>
```

만약 동일한 프로그램을 여러개의 인스턴스에서 실행한다면? 다음과 같이 출력된다.

```shell
prompt> ./cpu A & ; ./cpu B & ; ./cpu C & ; ./cpu D &
[1] 7353
[2] 7354
[3] 7355
[4] 7356
A
B
D
C
A
B
D
C
A
C
B
D
```

우리 컴퓨터에는 하나의 프로세서(CPU)가 있음에도 불구하고, 네개의 프로그램이 동시에 실행되는것처럼 보인다. (즉, 완전히 동시에 실행되는것은 아니다)

### 가상화의 마법

> OS는 시스템이 많은 Virtual CPU를 가지고 있는것처럼 보이게 만든다.

하나의 CPU를 여러개의 CPU가 있는것처럼 보이게 해서 여러 프로그램이 동시에 돌아가도록 만드는것을 **CPU 가상화**라고 한다.

## 2.2 Memory 가상화

```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include "common.h"

int main(int argc, char *argv[]) {
    int *p = malloc(sizeof(int)); // a1
    assert(p != NULL);
    printf("(%d) address of p: %08x\n", getpid(), (unsigned) p); // a2
    *p = 0; // a3
    while (1) {
        Spin(1);
        *p = *p + 1;
        printf("(%d) p: %d\n", getpid(), *p); // a4
    }
    return 0;
}
```

이 C코드는,
- a1) p 배열(포인터)에 integer 하나만 들어가는 공간을 동적으로 할당하고
- a2) p 포인터가 할당된 메모리 주소와 pid를 출력하고,
- a3) p 포인터의 할당된 메모리 주소 Head에 integer 0 하나를 삽입하고
- a4) 1초마다 반복하면서 메모리 주소 Head에 값을 1씩 증가시키고 이를 출력한다.

**만약 이 프로그램을 동시에 여러개 실행한다면??? -> 메모리 충돌이 일어날수도 있지 않을까??**

답은 NO

```shell
prompt> ./mem &; ./mem &
[1] 24113
[2] 24114
(24113) memory address of p: 00200000
(24114) memory address of p: 00200000
(24113) p: 1
(24114) p: 1
(24114) p: 2
(24113) p: 2
(24113) p: 3
(24114) p: 3
(24113) p: 4
(24114) p: 4
...
```
이와 같이 정상적으로 실행된다. 그런데, 두개의 프로그램에서 할당한 메모리 주소가 같고, 각각 독립적으로 값을 업데이트하는것을 볼 수 있다. (??? 왜 그런거지)

### 메모리 가상화의 마법

> 정상적으로 동작하는 이유는 OS의 Memory virtualization 때문이다. 각 Process는 자신만의 개인 가상 주소 공간을 사용하고, 운영 체제는 이를 물리적 메모리와 매핑시킨다.

따라서 한 프로그램에서 사용하는 메모리는 사실 **자신만의 가상 주소 공간**이며, OS는 이를 물리적 메모리(실제 메모리)로 매핑하게 된다.

실행중인 프로세스 입장에서는 본인이 메모리를 점유하고 있다! 라고 생각하게 되지만, 사실은 이러한 뒷배경이 있는것이다.

## 2.3 동시성 (Concurrency)

> 동시성 : 하나의 프로그램에서 여러개의 작업을 동시에 수행할 때 발생하는 다양한 문제들을 의미함

앞에 가상화에 대한 설명에서도 OS는 여러개의 작업을 동시에 처리한다. 다시 말해, 하나의 프로세스를 실행하고 또 다른 프로세스를 실행하는 식으로 작동한다.

```c
#include <stdio.h>
#include <stdlib.h>
#include "common.h"

volatile int counter = 0;
int loops;

void *worker(void *arg) {
    int i;
    for (i = 0; i < loops; i++) {
        counter++;
    }
    return NULL;
}

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "usage: threads <value>\n");
        exit(1);
    }
    loops = atoi(argv[1]);
    pthread_t p1, p2;
    printf("Initial value : %d\n", counter);

    Pthread_create(&p1, NULL, worker, NULL);
    Pthread_create(&p2, NULL, worker, NULL);
    Pthread_join(p1, NULL);
    Pthread_join(p2, NULL);
    printf("Final value : %d\n", counter);
    return 0;
}
```

이 C 코드는 다중 쓰레드 프로그램이다.
- `Pthread_create` : `worker` 작업을 수행하는 스레드 생성
- `Pthred_join` : 작업을 실제로 수행

loops를 만약에 1000을 입력했다면, 다음과 같은 출력 결과가 나온다.

```shell
prompt> gcc -o thread thread.c -Wall -pthread
prompt> ./thread 1000
Initial value : 0
Final value : 2000
```

그렇다면 두개의 프로그램을 동시에 실행한다면 어떨까?

```shell
prompt> ./thread 100000
Initial value : 0
Final value : 143012 // huh??
prompt> ./thread 100000
Initial value : 0
Final value : 137298 // what the??
```
우리가 예상한 결과와는 매우 다른 결과가 나왔다. 이러한 예상치 못한 결과가 나온 이유는 두 쓰레드에서 공우한 `counter`값을 사용하는 과정에서 atomicity를 보장받지 못해 발생한 경우이다.

## 2.4 영속성 (Persistence)

> 우리가 사용하는 컴퓨터에서 RAM은 전원이 꺼질때 안에 있는 모든 데이터가 사라진다. 따라서 데이터를 지속적으로 저장하기 위한 장치가 필요하게 되었고, 이를 지원하는 장치가 HDD, SDD같은 하드웨어 이다.

- OS에서 이러한 하드웨어를 관리하는 Software를 **file system**이라고 부른다.
- **CPU, Memory 와는 달리 저장장치를 가상화 하지 않는다**
- File system은 `open()`, `write()`, `close()`같은 시스템 콜 API를 제공한다.

## 2.5 설계 목표

> 운영체제는 물리적인 자원을 **가상화**하여 사용하기 쉽게 만들고, 동시성 문제를 해결하고, 데이터 영속성을 지원한다.

### 설계 목표

1. Abstraction : 시스템을 사용하기 쉽게 만들기 위해 `proc`같은 xv6의 PCB (CPU 추상화)를 제공한다.
2. 성능 최적화
3. 보호 및 격리 : 여러 프로그램이 동시에 실행될 때 각 프로그램이 서로에게 영향을 미쳐서는 안된다. (Isolation)
4. 신뢰성 : OS는 항상 안정적이어야 한다.
5. 에너지 효율 : 환경을 고려하여 에너지를 절약해야 한다.
6. 보안 강화 : 악의적인 행위로부터 시스템을 보호해야 한다.
7. 소형 장치 지원 : portable device도 지원해야 한다.

## 2.6 History

1. **UNIX의 탄생과 발전**
   - UNIX는 Ken Thomson과 Dennis Ritchie에 의해 개발되었으며, 초기 시스템인 Multics에서 영감을 받았습니다.
   - 초기 UNIX는 Assembly로 작성되었고, B 언어를 거쳐 C 언어로 재작성되어 발전했습니다.
   - UNIX 버전은 1에서 시작하여 6, 7 등으로 발전했으며, Version 6 UNIX는 MIT에서 교육용 OS인 xv6로 재탄생했습니다.

2. **UNIX 버전의 진화**
   - **Version 6 UNIX**에서 **System III**으로 발전(1981년), 그 후 System IV, System V로 계속 진화했습니다.
   - **System V**는 UNIX의 상업적 표준으로 자리 잡으며 다양한 버전(SVR4.1, SVR4.2 등)으로 발전했습니다.

3. **BSD vs. System IV**
   - UNIX가 상업적 버전(System IV)으로 발전하는 동안, Berkeley Software Distribution(BSD)도 독자적인 발전을 이루었습니다.

4. **Linux vs. FreeBSD**
   - UNIX의 철학을 계승한 다양한 운영체제들이 등장했으며, Linux와 FreeBSD(386BSD, NetBSD, OpenBSD) 등이 대표적인 예입니다.
   - Linux는 커뮤니티 중심의 개방형 프로젝트로 널리 사용되었고, FreeBSD 계열은 고유의 안정성과 성능을 중시하는 방향으로 발전했습니다.

# Extra memo

## 추상화 vs 가상화
### 1. Abstraction (추상화)
   - 운영체제(OS)는 자원을 관리하는 소프트웨어로, 하드웨어 자원을 소프트웨어 구조로 추상화합니다.
   - **OS = Resource Manager**: 자원을 효율적으로 관리하는 관리자 역할을 수행.
   - **컴퓨터 시스템의 HW를 SW로 추상화**:
     - **CPU**: 프로세스 구조(Process Structure)로 추상화.
     - **Memory**: 메모리 관리 구조(Memory Management Structure)로 추상화.
     - **Disk**: 파일(File), 파일 시스템의 Inode 등으로 추상화.

### 2. Virtualization (가상화)
   - 가상화는 물리적인 자원 하나를 여러 개처럼 보이게 만드는 환상을 제공합니다.
   - 각 프로세스가 독립적으로 실행되도록 하여, 서로의 간섭 없이 실행이 가능하게 만듭니다.
   - **Interleave Execution**: CPU가 여러 프로세스의 명령어를 번갈아 실행하여, 마치 병렬적으로 동시에 실행되는 것처럼 보이게 합니다.
   - **CPU Utilization Maximization**: 이러한 가상화는 CPU 사용률을 최대화하여 자원을 효율적으로 활용합니다.
