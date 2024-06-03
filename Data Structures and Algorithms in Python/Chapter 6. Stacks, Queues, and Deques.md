# 6.1 Stacks

> Stack : Last In, First Out 원칙에 따라 요소들을 삽입하거나 삭제하는 자료구조

예를 들면, Web browser의 최근 방문 기록 기능이 Stack으로 구현된다.
사이트를 방문할 때 마다 방문기록 stack에 URL을 넣고, 
사용자가 뒤로가기 버튼을 눌렀을 때, Stack의 가장 위에 놓인 URL로 redirect하게 된다.

또한, 텍스트 편집기나 문서 편집기의 되돌리기(Undo)기능도 Stack으로 구현된다.

## 6.1.1 The Stack ADT

일반적으로 Stack ADT는 다음 메서드들을 지원해야한다.

1. **S.push(e)** : Stack `s`에 element `e`를 삽입한다.
2. **S.pop()** : Stack `s`의 top에 있는 element를 반환하고, **삭제한다**. 만약 Stack이 empty라면, 에러가 발생한다.
3. **S.top()** : Stack `s`의 top에 있는 element를 반환한다. 만약 Stack이 empty라면, 에러가 발생한다.
4. **S.is_empty()** : Stack이 비어있는지 여부를 검사해준다. 비어있다면 True를 반환하고, 비어있지 않다면 False를 반환한다.
5. **len(S)** : Stack에 들어있는 요소의 개수를 반환한다. Python에서는 Stack 구현 클래스에 `__len__()` 메서드를 추가해주면 된다.

## 6.1.2 Simple Array-Based Stack Implementation

Python에서 라이브러리나 내장함수로 Stack 기능을 지원해주지는 않고, 직접 List로 구현해주어야 한다.
List class는 기본적으로 배열의 맨 뒤에 요소를 삽입해주는 `append`함수와 
가장 뒤에 존재하는 element를 반환하고 삭제해주는 `pop`함수를 지원한다.
(**pop**함수 안에 index를 집어넣으면 해당 index를 반환하고 삭제해주기도 한다)

### The Adapter Pattern

어댑터 디자인 패턴은 Java의 Override라고 생각하면 된다.
즉, 기존 클래스를 수정해서 새로운 클래스나 인터페이스의 메소드와 일치하도록 하는 경우 적용한다.

Stack ADT는 Python의 List를 사용해서 어댑터 패턴을 적용할 수 있다.
![](https://velog.velcdn.com/images/calzone0404/post/8fcfea32-4769-42ff-a5a1-8198e292c533/image.png)

### Implementing a Stack Using a Python List

아까 정한 Stack ADT에서, 스택이 비어있을 때 Pop 또는 Top 함수를 사용하는 경우, 에러가 발생하게 된다. 하지만, 단지 에러가 발생한다고 그냥 두면 안되고, 어떤 에러가 발생하는지 명확히 정해야 한다.

해당 책에서는 새로운 Exception class를 정의하였다.

```python
class Empty(Exception):
	"""
    빈 stack에 접근하려는 시도가 발생하면 해당 Exception을 발생시킨다.
    """
	pass
```

이를 바탕으로 Stack을 구현한 코드는 다음과 같다.

```python
class ArrayStack:
    """LIFO Stack implementation using a Python list as underlying storage."""
    
    def __init__(self):
        """Create an empty stack."""
        self._data = []  # nonpublic list instance
    
    def __len__(self):
        """Return the number of elements in the stack."""
        return len(self._data)
    
    def is_empty(self):
        """Return True if the stack is empty."""
        return len(self._data) == 0
    
    def push(self, e):
        """Add element e to the top of the stack."""
        self._data.append(e)  # new item stored at end of list
    
    def top(self):
        """Return (but do not remove) the element at the top of the stack.
        
        Raise Empty exception if the stack is empty.
        """
        if self.is_empty():
            raise Empty("Stack is empty")
        return self._data[-1]  # the last item in the list
    
    def pop(self):
        """Remove and return the element from the top of the stack (i.e., LIFO).
        
        Raise Empty exception if the stack is empty.
        """
        if self.is_empty():
            raise Empty("Stack is empty")
        return self._data.pop()  # remove last item from list

class Empty(Exception):
    """Error attempting to access an element from an empty container."""
    pass
```

### Analyzing the Array-Based Stack Implementation

![](https://velog.velcdn.com/images/calzone0404/post/912fa233-b3bb-4118-82d2-558c82507c49/image.png)

스택에서 수행하는 함수는 대부분 상수시간 O(1)이 소요된다.

하지만, Push나 Pop의 경우에는 최악의 경우 O(n)이 소요된다.
그 이유는, Stack을 생성할 때 할당한 메모리 크기가 꽉 찬 상태에서, 새로운 element를 삽입하게 된다면, 배열 resize가 필요하기 때문이다.

마찬가지로 pop의 경우에도 stack 공간의 낭비를 줄이기 위해 resize를 진행하게 된다.

## 6.1.3 Reversing Data Using a Stack

다음 코드는 파일을 열어서, 스택에 한줄 씩 text string을`PUSH`하고, 
다시 Stack에서 `POP`하여 파일에 쓰는 작업을 수행하는 함수다.

Stack의 특성으로 인해, 파일에 쓰인 내용이 정반대로 쓰이게 된다.

```python
def reversefile(filename):
    """Overwrite given file with its contents line-by-line reversed."""
    S = ArrayStack()
    original = open(filename)
    for line in original:
        S.push(line.rstrip('\n'))  # we will re-insert newlines when writing
    original.close()
    
    # now we overwrite with contents in LIFO order
    output = open(filename, 'w')  # reopening file overwrites original
    while not S.is_empty():
        output.write(S.pop() + '\n')  # re-insert newline characters
    output.close()
```

## 6.1.4 Matching Parentheses and HTML Tags

Stack을 가장 효율적으로 활요할 수 있는 경우가 바로 `괄호쌍`의 유효성을 검증하는 것이다. Stack을 이용한 코딩테스트 문제를 풀어보았다면 알겠지만, 괄호쌍 문제를 풀 때 거의 항상 Stack을 사용한다.

> Parentheses : ( )
> Braces: { }
> Brackets: \[ \]

```python
def is_matched(expr):
    """Return True if all delimiters are properly matched; False otherwise."""
    lefty = '({['  # opening delimiters
    righty = ')}]'  # respective closing delimiters
    S = ArrayStack()
    for c in expr:
        if c in lefty:
            S.push(c)  # push left delimiter on stack
        elif c in righty:
            if S.is_empty():
                return False  # nothing to match with
            if righty.index(c) != lefty.index(S.pop()):
                return False  # mismatched
    return S.is_empty()  # were all symbols matched?

```

위 코드는 괄호들이 올바르게 구성되어 있는지에 대해서 검증해준다.

예시로는 다음과 같다.
![](https://velog.velcdn.com/images/calzone0404/post/d3bd6af7-0f87-4549-ab91-02be3cc6a7b4/image.png)

### Matching Tags in HTML
HTML 태그의 쌍이 올바르게 형성되어 있는지에 대해서도 똑같다. HTML 태그가 괄호쌍으로 생각한다면, 당연히 Stack을 사용한다는것을 떠올릴것이다.

```python
def is_matched_html(raw):
    """Return True if all HTML tags are properly matched; False otherwise."""
    S = ArrayStack()
    j = raw.find('<')  # find first '<' character (if any)
    while j != -1:
        k = raw.find('>', j + 1)  # find next '>' character
        if k == -1:
            return False  # invalid tag
        tag = raw[j + 1:k]  # strip away <>
        if not tag.startswith('/'):  # this is an opening tag
            S.push(tag)
        else:  # this is a closing tag
            if S.is_empty():
                return False  # nothing to match with
            if tag[1:] != S.pop():
                return False  # mismatched delimiter
        j = raw.find('<', k + 1)  # find next '<' character (if any)
    return S.is_empty()  # were all opening tags matched?
```

# 6.2 Queues
## 6.2.1 The Queue ADT
## 6.2.2 Array-Based Queue Implementation

# 6.3 Double-Ended Queues
## 6.3.1 The Deque ADT
## 6.3.2 Implementing a Deque with a CircularArray
## 6.3.3 Deques in the Python Collections Module
