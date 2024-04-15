# 1.1 리스트 ADT

## 1.1.1 리스트의 개념

- Node : 리스트의 목록을 이루는 개별 요소
- Head : 리스트의 첫 번째 노드
- Tail : 리스트의 마지막 노드
- 노드의 개수 : 리스트의 길이

리스트 ADT로는 append, insert, remove, getat 등...이 있다고 할 수 있겠다.

## 1.1.2 리스트와 배열 비교

배열의 단점 : 
- C언어에서 배열은 생성하는 시점에 반드시 배열의 크기를 지정해줘야함.
- 생성한 이후, 크기를 다시 변경할 수 없음

# 1.2 링크드 리스트
> 주석을 자세히 달아놨으니 복습하러 왔을때 읽어보도록 하자.

## LinkedList.h
```c
#ifndef LINKEDLIST_H // 만약 헤더파일에 LinkedList.h가 선언되지 않았다면
#define LINKEDLIST_H

#include <stdio.h>
#include <stdlib.h>

typedef int ElementType;

typedef struct tagNode{
    ElementType Data;
    struct tagNode* NextNode;
} Node;

//함수 원형 선언
Node* SLL_CreateNode(ElementType NewData);
void SLL_DestroyNode(Node* Node);
void SLL_DestroyAllNodes(Node** Head);
void SLL_AppendNode(Node** Head, Node* NewNode);
void SLL_InsertAfter(Node* Current, Node* NewNode);
void SLL_InsertBefore(Node** Head, Node* Current, Node* NewNode);
void SLL_InsertNewHead(Node** Head, Node* NewHead);
void SLL_RemoveNode(Node** Head, Node* Remove);
Node* SLL_GetNodeAt(Node* Head, int Location);
int SLL_GetNodeCount(Node* Head);

#endif

```

## LinkedList.c
```c
#include "LinkedList.h"
/**
 * Singly LinkedList Node 생성 함수
 * @param NewData
 * @return
 */
Node* SLL_CreateNode(ElementType NewData){
    Node* NewNode = (Node*)malloc(sizeof(Node)); // malloc을 사용하여 heap에 생성 (free memory)

    /*
     * sizeof(Node)와 sizeof(Node*)의 차이점
     * sizeof(Node)는 Node 구조체 자체의 크기.
     * 따라서, ELementtype 크기 + Node*형 포인터 크기
     * 보통 64bit 체계 시스템에서는 포인터의 크기는 8바이트 (32bit에서는 4바이트)
     * sizeof(Node*)는 포인터 자체의 크기이므로 8바이트만 할당됨
     * 따라서 SLL 생성할때에는 sizeof(Node)를 해주어야함
     */
    NewNode->Data = NewData; // 데이터 저장
    NewNode->NextNode = NULL; // 다음 노드는 아직 없으니까

    return NewNode; // Heap 영역 주소 반환
    // 만약 NewNode를 힙영역에 할당했다면 이 함수가끝나고, 주소를 반환했을때에는
    // 이미 해제된 메모리를 반환하게 되어 잘못된 연산을 수행하게됨.
}

/**
 * SLL 뒤에 새로운 노드를 추가하는 함수
 * @param Head
 * @param newNode
 */
void SLL_AppendNode(Node** Head, Node* newNode){
    /**
     * 왜 Node** Head인가???
     * 더블포인터변수 A -> A가 가리키는 B -> B가 가리키는 값
     * 위와 같은 형태가 더블포인터 구조이다.
     * 만약 함수가 (Node* Head, Node* newNode)로 선언되어있고,
     * Node* List = NULL인 빈 리스트를 선언하고, 이 리스트를 그냥 넘겨버리면
     * Node* Head는 NULL을 가리키게 된다.
     * 따라서 if문으로 들어가면 Head가 newNode를 가리키게 되고,
     * if문이 종료되면 Node* Head는 newNode를 가리키게 된다.
     * 그런데, 이 함수에서 선언된 Node* Head는 newNode를 가리킨 상태인데,
     * Head는 스택영역에 할당된 변수이므로 함수가 종료되면 바로 해제되어버린다.
     * 따라서 다시 메인함수로 빠져나왔을 때 List는 변하지 않은 상태이므로 여전히 NULL 상태이다.
     *
     * 위 이유 때문에 Node**를 통해 더블 포인터 구조로 자기 자신 List의 주소를 그대로 넘겨서
     * 정상적으로 Head를 설정할 수 있도록 하는 것이다.
     */
    // 헤드가 없다면 새로운 노드가 Head임
    if((*Head) == NULL){
        *Head = newNode;
    }
    else{
        // Tail을 찾아서 그 뒤에 넣어준다.
        Node* Tail = *(Head);
        while(Tail->NextNode != NULL){
            Tail = Tail->NextNode;
        }
        Tail->NextNode = newNode;
    }
}

/**
 * 원하는 위치의 노드를 반환하는 함수
 * @param Head
 * @param Location
 * @return
 */
Node* SLL_GetNodeAt(Node* Head, int Location){
    Node* Current = Head;
    while(Current != NULL && (--Location) >= 0){
        Current = Current->NextNode;
    }
    return Current;
}

/**
 * 원하는 노드를 삭제하는 함수
 * @param Head
 * @param Remove
 */
void SLL_RemoveNode(Node** Head, Node* Remove){
    if (*Head == Remove){
        *Head = Remove->NextNode;
    }
    else{
        Node* Current = *Head;
        // Current Node를 Remove Node 이전까지 이동시킨다.
        while(Current != NULL && Current->NextNode != Remove){
            Current = Current->NextNode;
        }
        // 만약 While문에서 Remove노드가 존재하지 않았다면 삭제 불가
        // Remove노드가 존재하는 경우만 if문 실행
        // Current노드는 Remove 이전 노드를 가리키고 있으므로
        // Current노드가 Remove 노드의 다음 노드를 가리키도록 설정
        if (Current != NULL) {
            Current->NextNode = Remove->NextNode;
            SLL_DestroyNode(Remove);
        }
    }
}

/**
 * 원하는 노드를 현재 노드 이후에 삽입하는 함수
 * @param Current
 * @param NewNode
 */
void SLL_InsertAfter(Node* Current, Node* NewNode){
    NewNode->NextNode = Current->NextNode; // 삽입하려는 NewNode가 Current의 다음 Node를 가리키게 하고,
    Current->NextNode = NewNode; // Current의 다음 노드가 NewNode가 되도록 설정
}

/**
 * 원하는 노드를 현재 노드 이전에 삽입하는 함수
 * @param Current
 * @param NewNode
 */
void SLL_InsertBefore(Node** Head, Node* Current, Node* NewNode){
    if (*Head == NULL){
        *Head = NewNode;
    }
    else if (Current == *Head){
        SLL_InsertNewHead(Head, NewNode);
    }
    else {
        Node* Temp = *Head;
        while (Temp != NULL && Temp->NextNode != Current) {
            Temp = Temp->NextNode;
        }
        Temp->NextNode = NewNode;
        NewNode->NextNode = Current;
    }
}

/**
 * 새로운 헤드 생성 함수 (AppendFront 역할을 하는 함수)
 * 리스트 자체의 주소값을 가져와야 하므로 Node** 사용
 * @param Head
 * @param NewHead
 */
void SLL_InsertNewHead(Node** Head, Node* NewHead){
    // 리스트 자체가 Null이라면,
    // 헤드를 그냥 NewHead로 설정
    if (*Head == NULL){
        *Head = NewHead;
    }
    else{
        NewHead->NextNode = (*Head); // 새로운 헤드가 기존 헤드가 가리키고 있는 노드를 가리키게 함
        (*Head) = NewHead; // 기존 헤드노드를 새로운 노드로 교체
    }
}

/**
 * 노드 개수 세는 함수
 * @param Head
 * @return
 */
int SLL_GetNodeCount(Node* Head){
    int Count = 0;
    Node* Current = Head;
    while(Current != NULL){
        Current = Current->NextNode;
        Count++;
    }
    return Count;
}

/**
 * Heap 영역에 할당한 Linked List Node 해제 함수
 * @param Node
 */
void SLL_DestroyNode(Node* Node){
    free(Node);
}

/**
 * Heap 영역에 할당한 "모든" Linked List Node 해제 함수
 * @param Node
 */
void SLL_DestroyAllNodes(Node** Head){
    if (Head != NULL && *Head != NULL) {
        Node* Current = *Head;
        while (Current != NULL) {
            Node* next = Current->NextNode; // 다음 노드를 임시로 저장
            free(Current); // 현재 노드 메모리 해제
            Current = next; // 다음 노드로 이동
        }
        *Head = NULL; // 모든 노드가 해제된 후, 헤드 포인터를 NULL로 설정
    }
}

```

## Test_LinkedList.c
```c
#include "LinkedList.h"

int main(){
    int Count = 0;
    Node* List = NULL; //깡통 SLL 생성
    Node* Current = NULL; // 원하는 위치를 가리키는 포인터 노드
    Node* NewNode = NULL; // 데이터 노드

    //노드 5개 추가
    for (int i = 0; i < 5; i++){
        NewNode = SLL_CreateNode(i);
        SLL_AppendNode(&List, NewNode);
    }

    NewNode = SLL_CreateNode(-1);
    SLL_InsertNewHead(&List, NewNode);

    NewNode = SLL_CreateNode(-2);
    SLL_InsertNewHead(&List, NewNode);

    //리스트 출력
    Count = SLL_GetNodeCount(List);
    for(int i = 0; i < Count; i++){
        Current = SLL_GetNodeAt(List, i);
        printf("List[%d] : %d\n", i, Current->Data);
    }

    //리스트 세번째 노드 뒤에 데이터 삽입
    printf("\nInserting 3000 After [2]\n\n");

    Current = SLL_GetNodeAt(List, 2); // 2번째 노드를 가리킴
    NewNode = SLL_CreateNode(3000);

    SLL_InsertAfter(Current, NewNode);

    //리스트 출력
    Count = SLL_GetNodeCount(List);
    for(int i = 0; i < Count; i++){
        Current = SLL_GetNodeAt(List, i);
        printf("List[%d] : %d\n", i, Current->Data);
    }

    //리스트 세번째 노드 앞에 데이터 삽입
    printf("\nInserting 2000 After [2]\n\n");

    Current = SLL_GetNodeAt(List, 2); // 2번째 노드를 가리킴
    NewNode = SLL_CreateNode(2000);

    SLL_InsertBefore(&List, Current, NewNode);

    //리스트 출력
    Count = SLL_GetNodeCount(List);
    for(int i = 0; i < Count; i++){
        Current = SLL_GetNodeAt(List, i);
        printf("List[%d] : %d\n", i, Current->Data);
    }

    /*모든 노드를 메모리에서 제거
    printf("\nDestroying List\n");
    for(int i = 0; i < Count; i++){
        Current = SLL_GetNodeAt(List, 0);
        if (Current != NULL){
            SLL_RemoveNode(&List, Current);
            SLL_DestroyNode(Current);
        }
    }*/

    //모든 노드를 메모리에서 제거
    printf("\nDestroying All Nodes in List\n");
    SLL_DestroyAllNodes(&List);

    return 0;
}
```

## 실행결과
![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/d0e96f48-2061-4f1b-ae7d-71c2709a80a5)

## SLL의 장단점
### 장점
1. 새로운 노드의 추가, 삽입, 삭제가 쉽고 빠르다.
2. 현재 노드의 다음 노드를 얻어오는 연산에 대해서는 비용이 발생하지 않는다. (자신이 다음 노드를 가리키고 있으니까)

### 단점
1. 다음 노드를 가리키는 포인터때문에 추가적인 메모리 할당이 필요하다.
> 32bit는 포인터 변수를 4바이트, 64bit 컴퓨터는 8바이트를 고정적으로 할당한다.

2. 특정위치의 노드에 접근하려면 $$O(n)$$ 시간이 필요하다.
