# 0.1 자료구조

> 자료구조 : 데이터 보관 방법 및 데이터에 관한 연산의 총체

단순 자료구조 : int, float, double, ...
복합 자료구조 : 배열, Linked List, Stack, Tree, ...

> ADT : 자료구조의 동작 방식을 표현하는 데이터 형식
즉, 자료구조가 갖추어야 할 일련의 연산

예를 들면, 데이터 추가, 삽입, 삭제 연산등이 해당한다.


# 0.2 알고리즘

> 어떤 문제를 풀기 위한 단계적 절차

알고리즘 설계란, **어떤 문제를 풀기 위한 단계적 절차**를 설계하는것.

# 0.3 C 언어로 메모리를 다루는 방법

## 0.3.1 포인터 복습

```c
#include <stdio.h>
int main(){
    int *ptr;
    int a = 3;

    ptr = &a; //ptr은 a의 주소를 가리킨다.
    printf("%p\n", ptr); //ptr이 가리키고있는 주소 출력
    printf("%d", *ptr); //ptr이 가리키고 있는 값 출력

    //arr는 integer형 이므로 4byte씩 메모리 연산이 가능
    int arr[6] = {0, 1, 2, 3, 4, 5};
    int* ptr2 = arr;
    printf("%s\n",  ptr2 == arr ? "True" : "False");
    printf("%d\n", *ptr2); // arr[0]
    printf("%d\n", *(ptr2 + 1)); // arr[1]
    printf("%d\n", *(ptr2 + 2)); // arr[2]
}
```

## 0.3.2 구조체 복습

아래 코드의 구조체는 총 8바이트가 필요하다. (int가 2개 정의되어있기 때문)
```c
#include <stdio.h>
struct Point{
    int x;
    int y;
};

int main(){
    struct Point myPoint = {3, 4};
    struct Point *ptr = &myPoint;
    printf("%d %d\n", myPoint.x, myPoint.y); // 멤버 접근 연산자는 .을 사용한다.
    printf("%d %d\n", ptr->x, ptr->y); // 포인터 멤버 접근 연산자는 ->를 사용한다
}
```

그런데, 위와 같이 구조체를 선언하면, 나중에 사용할 때 귀찮아지므로 **typedef**를 통해 alias를 해준다.

```c
#include <stdio.h>
typedef struct tagPoint{
    int x;
    int y;
} Point;

int main(){
    Point myPoint = {3, 4};
    Point *ptr = &myPoint;
    printf("%d %d\n", myPoint.x, myPoint.y); // 멤버 접근 연산자는 .을 사용한다.
    printf("%d %d\n", ptr->x, ptr->y); // 포인터 멤버 접근 연산자는 ->를 사용한다
}
```

## 0.3.3 메모리 레이아웃 복습

출처 : https://www.geeksforgeeks.org/memory-layout-of-c-program/

![](https://velog.velcdn.com/images/calzone0404/post/a1bc7e71-b123-45ff-b9b3-b037f0265a2a/image.png)


- Text : CPU가 실행할 코드가 적재되는 영역
- Un/initialized data : 초기화 된/안된 global 변수 또는 static 변수가 저장되는곳

## 0.3.4 스택에서 데이터를 다루는 방법

> 스택 : 자동 메모리 할당 지역

스택 메모리 영역은, 스택 ADT와 마찬가지로 할당된 순서의 역순으로 메모리에서 변수가 제거된다.
예를 들어서,
```c
{
	int a;
    int b;
    int c;
}
```
위 코드블럭에서 stack 메모리에는 a -> b -> c 순으로 탑재되며, 메모리 해제 시 c -> b -> a 순으로 해제된다.

## 0.3.5 힙에서 데이터를 다루는 방법

> 힙 : 자유 메모리 할당 지역

힙은 프로그래머가 직접 메모리를 할당/해제하여 자유롭게 사용할 수 있는 영역이다.


```c
typedef struct tagPoint{
	int x;
    int y;
} Point;

Pioint *ptr = (Point*)malloc(sizeof(Point));
// Point 크기만큼 메모리를 힙에 할당하고, 
// ptr을 통해 해당 선언한 메모리 위치를 가리킬 수 있다.
```

