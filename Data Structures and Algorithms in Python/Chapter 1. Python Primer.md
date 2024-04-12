> 파이썬을 소개하는 챕터입니다. 파이썬을 배우고싶다면, 이 책이 아닌 다른 책을 보시는것을 추천드립니다 << 라고 책에서 권하고 있다.

# 1.1 Python Overview
자료구조와 알고리즘을 구현하려면, 우리가 컴퓨터에게 구체적인 명령을 내릴 수 있는 능력이 필요하다.
구체적인 명령을 내릴 수 있는 가장 완벽한 방법은 **고급 컴퓨터 언어** (C, CPP, Java, Python)을 사용하는 것이다.
<hr>
Python은 1990년대 Guido van Rossum이 개발하였으며, 현재까지 두드러지는 언어로 산업과 교육에서 이용되고 있다.
두번째 주요한 버전인 Python2는 2000년도에 출시되었으며, 3번째 주요한 버전 Python3는 2008에 출시되었다.
Python2와 Python3은 서로 비호환성이 상당하기 때문에, 이 책은 대부분 Python3.1 이후 버전으로 기술되었다. Python3의 최신 버전은 www.python.org에서 무료로 다운받을 수 있으며, 개발자 문서와 튜토리얼을 제공한다.
<hr>
이 챕터에서는 Python 언어에 대한 개요를 소개할것이며, 다음 챕터에서는 Python을 이용하여 객체지향 원리에 대해 설명할 것이다. 필자는 이 책을 읽는 독자들이 이전에 파이썬이 아닐지라도, 프로그래밍 경험이 있다고 가정할것이다. 이 책은 Python 언어를 완벽하게 소개하는 책이 아니다 (더 잘 설명해놓은 레퍼런스가 존재하기 때문). 하지만, 책에서 사용하는 Python 문법에 대한 내용은 충분히 기술되어있다.
<hr>

## 1.1.1 The Python Interpreter

파이썬은 정식 **Interpreter**언어이다. **Python Interpreter**로 알려진 소프트웨어에 의해 명령들이 한 조각씩 실행된다. 인터프리터가 명령을 전달받으면, 해당 명령을 평가하고, 그 명령에 대한 결과를 기록한다. 인터프리터가 쌍방향으로 사용됨과 동시에, 프로그래머는 일련의 명령들을 사전에 정의하고, 이러한 명령들을 **Source code 또는 Script**라고 불리는 일반 텍스트파일에 저장한다. Python에서는 이러한 파일을 통합하여 **.py**라는 접미사를 붙여서 저장한다.
<hr>
대부분의 OS에서, 파이썬 인터프리터는 CLI에서 **python**을 입력하여 실행할 수 있다. 기본적으로, 인터프리터가 깨끗한 CLI 작업창에서 interactive 모드로 실행된다.
예를 들어 **demo.py** 스크립트 파일에 미리 정의된 명령들은

	python demo.py
를 통해 인터프리터를 호출하여 실행하거나,

	python -i demo.py
명령어에 추가적인 인자 -i를 주어서 스크립트를 실행하고, itneractive mode로 들어갈 수 있다.
<hr>

Python 기본 설치 시 같이 들어있는 **IDLE**처럼, 대부분의 **통합 개발 환경툴 (IDE)**은 Python을 위해 풍부한 소프트웨어 개발 플랫폼을 제공하고 있다.
IDLE은 Python code를 편집할 수 있도록 도와주는 내장된 툴로, 기본적인 디버그 기능과, 우리가 알고있는 Jupiter Notebook 처럼 개발자가 원하는 Python의 변수값을 측정하는 step by step 기능도 제공하고 있다.
<hr>

## 1.1.2 Preview of a Python Program
```python
print("welcome to the gpa calculator")
print("please enter all your letter grades, one per line")
print("Enter a blnak line to designate the end")
# map from letter grade to point value
points = {'A+': 4.0, 'A': 4.0, 'A-': 3.67,
          'B+': 3.33, 'B': 3.0, 'B-': 2.67,
          'C+': 2.33, 'C': 2.0, 'C-': 1.67,
          'D+': 1.33, 'D': 1.0, 'D-': 0.1,
          'F': 0.0}
num_courses = 0
total_points = 0
done = False
while not done:
    grade = input()
    if grade == '':
        done = True
    elif grade not in points:
        print("invalid grade")
    else:
        num_courses += 1
        total_points += points[grade]

if num_courses > 0: # avoid div by zero
    print("Your GPA is {0:.3}".format(total_points/num_courses))
```
파이썬의 문법은 **whitespace(줄바꿈, 공백, 탭)**에 아주아주 많이 의존한다.
각각의 statement들은 newline 문자로 결정되지만, 명령어가 끝이 나는 '\'문자를 사용하거나, Hashmap (위에서 사용한 Dictionary)를 정의할 때, '{'문자와 같이 아직 닫히지 않는 경우 다른 줄로 확장할 수 있다.
<hr>

공백은 Python의 몸체를 제어하는 구조를 결정하는 아주 중요한 요소이다. 특히 제어 구조를 지정하기 위해 코드 블록을 들여쓰기한다던가, 중첩된 제어 구조를 기술하기 위해 들여쓰기 정도를 늘릴 수 있다. 위 코드를 보면 while 루프 블럭은 중첩된 제어 구조를 표현하기 위해 들여쓰기블록이 여러번 사용되었음을 알 수 있다.
<hr>

주석은 코드를 읽는 인간을 위해 제공하며, 파이썬 인터프리터는 이를 무시한다. Python의 주석에서 가장 중요한 부분은 '#' 문자를 사용한다는 것이다.

<hr>


# 1.2 Objects in Python

파이썬은 객체지향 언어이며, Class들은 모든 데이터 타입의 기초를 정의한다. 이번에는 Python의 기본적인 객체 모델과, int, float, str과 같은 built-in class들을 살펴보도록 하자.
<hr>

## 1.2.1 Identifiers, Objects
Python command의 가장 중요한 것 중 하나가, 변수 선언 및 할당이다.
```python
temperature = 36.5
```
위 command는 temperature을 식별자로 선언한 문장이다. 그리고, 오른쪽 부분에 부동실수값 36.5를 지정하여 해당 변수 temperature에 할당하였다.

### Identifiers
파이썬에있는 식별자들은 대소문자를 별개로 인식한다. 즉, temperature와 Temperature는 다른 식별자이다. 식별자들은 모든 문자, 숫자, 언더바 등...의 조합으로 선언할 수 있다. 물론 식별자 이름에도 규칙이 존재한다. 자세한 명명규칙은 아래를 참고하자
>https://hongong.hanbit.co.kr/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%B3%80%EC%88%98%EB%AA%85-%EA%B7%9C%EC%B9%99%EA%B3%BC-%EC%8B%9D%EB%B3%84%EC%9E%90-%EA%B5%AC%EB%B6%84-%EB%B0%A9%EB%B2%95/

<hr>
CPP나 Java같은 다른 언어에 익숙한 독자들이라면, 파이썬 식별자 명명 규칙에 대해서는 이미 익숙할것이다. 각각의 식별자는 은연중에 객체가 가리키고 있는 메모리 주소와 연관되어 있다. 또, 파이썬 식별자는 **None**이라고 하는 특별한 객체에도 할당될 수 있다. 아마, CPP나 Java에 익숙한 독자라면 **Null** 이라고 생각하면 될 것 같다.
<hr>
Java와 CPP과는 다르게, 파이썬은 동적으로 변수의 타입을 결정한다. 즉, 변수 선언 시 데이터 타입을 명시적으로 선언하지 않아도 된다는 것이다. 식별자는 모든 타입의 객체에 할당될 수 있으며, 후에 다시 똑같은 타입의 또다른 객체의 값을 재할당할수도 있다. **사용자가 선언한 식별자는 명시적인 타입이 선언되어있지 않지만, 후에 식별자가 가리키는 객체의 타입에 따라 자동으로 타입이 결정된다.**
<hr>

또한, 프로그래머는 현재 존재하는 객체에 대해 새로운 이름을 붙여줄 수 있다. **(Alias)**

```python
temperature = 36.5
asdf = temperature # 새로운 변수를 생성하여 36.5에 접근 가능
```
위 코드처럼, alias 사용 시 asdf와 temperature 모두 36.5라는 값에 접근이 가능하다. 만약 asdf가 해당 값에 영향을 미치는 동작을 수행하는 경우 (36.5를 40으로 변경), **asdf와 temperature는 같은 객체를 가리키고 있었기 떄문에,** 다른 별칭(temperature)을 사용할 때 asdf가 변경한 내용이 그대로 표시된다.

```python
temperature = temperature + 5
```
위와 같은 코드를 실행했을 때, 등호 오른쪽에 있는 statement가 실행되어 36.5 + 5 = 41.5라는 값이 temperature에 재할당된다. 즉, temperature는 41.5라는 새로운 부동소수점 객체를 가리키게 되며, 기존 asdf는 36.5라는 객체를 여전히 가리키고 있게 된다.
<hr>

## 1.2.2 Creating and Using Objects

### Instantiation

변수 초기화에 대한 내용이다. (엄청 기초적인 내용이라 Pass)

### Calling Methods

함수 호출 방법에 대한 내용이다. (엄청 기초적인 내용이라 Pass)

## 1.2.3 Python's Built-in Classes
![](https://velog.velcdn.com/images/calzone0404/post/c3ed18b5-d42d-41fc-bd54-126554a5ff7d/image.png)

위 사진은 파이썬에서 가장 자주 사용되는 Built-in Class들을 정리해놓은 표이다. 특히 각 Class들이 변환 가능한지, 변환 불가능한지 유심히 볼 필요가 있다(알고리즘 문제 풀 때, Immutable 때문에 오류가 자주 발생할 것이다).
<hr>

### The bool Class
논리적 연산 시, 해당 결과의 참 또는 거짓에 대해 표현하는 클래스

<hr>

### The int Class
정수를 표현하는 클래스. 다양한 정수 타입을 제공하는 Java와 Cpp과는 다르게 파이썬은 자동으로 표현하는 integer에 따라 정도를 조절한다. 또, 2진수, 8진수, 16진수는 접두사 0과 해당하는 진수의 character를 붙임을 통해 표현할 수 있다. (0b1011 : 2진수, 0o52 : 8진수, 0x7f : 16진수)

<hr>

정수 생성자 **int()**는 기본값 0을 반환한다. 하지만 이 생성자는 현재 존재하는 값을 integer로 변환해주는 기능도 수행한다. 예를 들어, 3.14라는 값을 정수로 변환하려면 
```python
result = int(3.14)
```
위와 같이 사용하면 result에 정수값 3이 들어가게 된다.
또, 숫자 문자열에 대해서도 동작하는데, 다음과 같이 사용하면 된다.

```python
result = int("123123")
```
위와 같이 사용하면 result에 문자열 123123을 integer로 변환한 값인 123123이 할당된다.

그러나, 숫자 문자열 대신 다른 문자열을 집어넣을 경우 ValueError Exception이 발생하므로, 반드시 10진수로 표현된 문자열을 집어넣도록 하자.

<hr>

### The float Class
float class는 부동소수점을 표현한다. Java와 Cpp에서의 double과 거의 비슷하게 동작한다고 생각하면 된다.

<hr>

### The list Class

list 인스턴스는 연속된 객체를 저장해준다. list는 **referentail structure (참조 구조)**이다. 즉, 기술적으로 보았을 때, 각각의 객체의 주솟값을 list에 저장한다. list에는 임의의 아무 객체 (None을 포함하여)를 삽입할 수 있다. 각각의 index는 0 ~ n-1까지로 표현된다.

```python
a = list('hello')
# a = ['h', 'e', 'l', 'l', 'o']
```

<hr>

### The tuple Class

tuple class는 list와는 달리 immutable한 list이다. 따라서 한번 초기화되면, 다른 동작에 의해 수정될 수 없다. array, list는 대괄호를 쓰는것과는 달리, tuple은 소괄호로 표현한다.

<hr>

### The str Class

str class는 문자열을 표현하는 immutable 객체이다. 이때, C에서 표현한 것 처럼 각각의 문자에는 reference 주소가 존재하기 때문에 0 ~ len(str)-1까지의 index가 존재하며, 각각의 문자를 array 또는 list처럼 대괄호로 참조가 가능하다.

<hr>

### The set and frozenset Classes

Set은 수학에서의 set을 생각하면 된다. 즉, 원소의 집합이다. 우리가 어떠한 원소를 **중복 없이 어떠한 자료구조에 집어넣고 싶을때**는 주로 Set을 사용한다. 또, **특정 원소가 해당 자료구조에 있는지 없는지 빠르게 판단**하기 위해서도 사용한다.

Set은 알고리즘 기반에 의해 두가지 중요한 제약사항이 존재한다.
- Set은 원소의 순서를 제공하지 않는다.
- Immutable type의 인스턴스만 추가될 수 있다. 즉, list, array와 같은 mutable 객체는 추가될 수 없다.

Frozenset은 set의 Immutable 형태이다.
<hr>

### The dict Class

파이썬의 dict class는 dictionary를 뜻하며, 우리가 알고있는 HashMap 또는 Map Collection이다. 즉, {key: value} 쌍으로 저장된다.

dict class 생성자는 현재 존재하는 pair에 대해 새로운 dictionary를 생성해준다.
```python
pairs = [("Kim", "20"), ("Lee", "21")]
mydict = dict(pairs)
```
<hr>


# 1.3 Expressions, Operators, and Precedence

### Logical Operator

- not : unary negation
- and : conditional and
- or : conditional or

프로그래밍 언어는 and와 or에 대한 조건을 판단할 때, **Short-circuit**으로 판단한다. 
예를 들어, 
- A and B에 대한 참거짓을 판단할 때, **A가 False라면 뒤에 존재하는 B에 대해서는 참거짓을 판단하지 않고 바로 False를 반환한다.**
- A or B를 판단할 때, **A가 True라면 뒤에 존재하는 B에 대해서는 참거짓을 판단하지 않고 바로 True를 반환한다.**

<hr>

### Equality Operators

- is : same identity
- is not : different identity
- == : equivalent
- != : not equivalent


#### 여기서 잠깐!!!!!!
>a **is** b는 a와 b가 똑같은 object를 가리키고 있는지 판단하는 연산자이며,
a == b는 a와 b가 같은 Value를 가리키고 있는지 판단하는 연산자이다.

<hr>

### Comparison Operators

기초적인 내용이므로 Pass

<hr>

### Arithmetic Operators

기초적인 내용이므로 Pass

<hr>

### Bitwise Operators
- ~ : bitwise complement (보수)
- & : bitwise and
- | : bitwise or
- ^ : bitwise xor
- << : shift bits left, filling with zeros
- \>> : shift bits right, fillig in with sign bit

<hr>

### Sequence Operators
주로 str, tuple, list에서 사용하는 연산자이다.
- s[j] : j번째에 존재하는 element
- s[start:stop] : 범위 slice -> [start, stop)

- s[start:stop:step] : start부터 step만큼 등차수열으로 해당 위치에 존재하는 element를 가져옴
- s + t : 두 sequences를 합치는 연산자
- k * s : s를 k번 더하는 연산자
- val in s : s에 val이 존재하는지 판단하는 연산자
- val not in s : s에 val이 존재하지 않는지 판단하는 연산자

<hr>

# 1.4 Control Flow

이번 챕터에서는 파이썬 구조를 제어하는 가장 중요한 기능인 조건문과 루프에 대해 다룰 예정이다. 둘다 공통적으로 코드블록을 정의하여 사용하게 된다.
제어문은 Colon (:)을 사용하여 제어문의 시작을 알리게 되며, 들여쓰기를 통해 body를 구성한다.

<hr>

## 1.4.1 Conditionals

if문을 사용하는 방법은 코딩도장 또는 점프 투 파이썬을 참고하자

<hr>

## 1.4.2 Loops

for, while문을 사용하는 방법은 코딩도장 또는 점프 투 파이썬을 참고하자

<hr>

# 1.5 Functions

이번 챕터에서는 파이썬에서 함수를 정의하고 사용하는 방법에 대해 다룰 예정이다.

> function : 특정 작업을 수행하는 코드조각
> method : Class, enum, struct 내부에서 선언한 함수

```python
# data list에 존재하는 target의 개수를 세는 함수
def count(data, target):
    n = 0
    for item in data:
        if item == target:
            n += 1
    return n
```

Java와 Cpp과는 달리, 파이썬은 동적으로 인수의 타입과 리턴타입이 결정된다 (물론 데코레이터를 통해 명시할수는 있다). 이 때문에, 함수를 잘못 사용하는 코드가 존재하는 경우에는 런타임에만 잡아낼수 있다.

<hr>

함수가 호출될 때 마다, 파이썬은 지정된 **activation record**를 생성하여 현재 호출에 대한 관련 정보를 모두 저장한다.

> Activation record (추후에 소개하는 **namespace**라고 불리움)는 **현재 호출의 지역 범위 안에있는 모든 식별자들을 저장하는 공간**이다.

namespace 안에는 함수의 파라미터와, 함수 내부에 정의된 지역 변수에 대한 정보가 포함되어 있다. 

<hr>


### Return Statement
return은 함수가 이 지점에서 동작을 중지하고, 계산된 값을 호출자에게 반환한다는 statement이다.

<hr>


## 1.5.1 Information Passing

성공적인 프로그래머가 되려면, 당신은 함수가 어떤 정보를 전달하고 받아야하는지 명료하게 이해하고 있어야 한다. 

함수 선언 시, 받게되는 매개변수를 **formal parameters**라고 지칭하며,
함수를 호출할 때 전달하는 매개변수를 *actual parameters**라고 지칭한다.

```python
def count(data, target):
	pass
    
prizes = count(grades, 'A')
```
예를 들어, 위와 같은 코드가 있다고 해보자
함수의 body 코드가 실행되기 바로 직전에, actual parameter들 grades, 'A'가 암묵적으로 formal parameter들 data, target에 할당된다.

```python
data = grades
target = 'A'
```
이렇게 할당되었을 때, data의 별칭은 grades가 되고, target의 별칭읜 string literal 'A'가 된다. (포인터로 가리킨다는 뜻)

함수에서 return하는 과정도 함수에게 parameter을 전달하는 동작과 비슷하다. 그러므로 함수 호출 후 전달받은 값에 대해서 prizes에 새로운 값이 할당된다.

<hr>


### Defalut Parameter Values

파이썬은 하나 이상의 가능한 호출을 지원하는 수단이 존재한다. 이러한 함수를 **Polymorphic(다형적)**이라고 한다.
즉, 함수의 formal parameter의 기본값을 지정하여, actual paramenter을 전달받지 않아도, 기본값을 통해 정상적으로 함수 작동이 가능하다.

<hr>


## 1.5.2 Python's Built-In Functions

![](https://velog.velcdn.com/images/calzone0404/post/85aa0c63-4939-4169-b1a0-b6b211c180f8/image.png)

<hr>

# 1.6 Simple Input and Output

이 섹션에서는 파이썬에서 콘솔 및 파일 입출력을 어떻게 수행하는지에 대해 소개하겠다.

<hr>

## 1.6.1 Console Input and Output

> print() : Built-In function
콘솔에 표준 출력을 하기 위해 사용한다.

>input() : Built-In function
콘솔에서 사용자의 입력을 받기 위해 사용한다.

<hr>

## 1.6.2 Files
파일도 마찬가지로, 파이썬에서 지원하는 Built-In function을 통해 접근 및 수정이 가능하다.

```python
fp = open('mytext.txt', 'r')
# 첫번째 인수로 텍스트파일 경로를 넣어주고
# 두번째 인수로 파일을 열때 모드를 설정한다
```
- r : 파일 읽기 모드
- w : 파일 쓰기 모드 (파일을 초기화해버린다)
- a : 파일 수정 모드 (기존 내용에 이어서 쓸 수 있다)
- wb : binary file을 작업하고 싶은 경우

위와 같이 open을 통해 파일을 열면, 파일에 접근할 수 있는 포인터가 fp에 담기게 된다.

![](https://velog.velcdn.com/images/calzone0404/post/15f50831-6b02-404d-9900-bef57c194cca/image.png)

<hr>

### Reading from a file

```python
fp.read(k) 
# 파일 포인터가 가리키고 있는 부분부터
# k bytes를 읽어서 return한다.
```

<hr>

### Writing to a File

```python
fp = open('mytext.txt', 'w')
fp.write('salkdjflkasdkf') # 파일에 해당 내용이 쓰인다
```
write는 항상 기존 내용을 지워버리고, 새로 써버린다는것을 명심하자. (append mode로 파일을 열자)


<hr>

# 1.7 Exception Handling

Exception은 프로그램 실행중에, 기대하지 않았던 이벤트가 발생한 상황이다. Exception은 논리적인 오류가 있거나, 기대하지 않은 상황일 때 주로 발생한다. 파이썬에서는 exception이 발생하였을 때, **rasied**라고 표현한다.
또한, 파이썬 인터프리터는 할당되지 않은 메모리에 접근하였을때에도 Exception을 발생시킨다.

파이썬은 Java와 마찬가지로 이러한 Exception을 다루는 방법이 존재한다.

### Common Exception Types
![](https://velog.velcdn.com/images/calzone0404/post/e6833025-25a8-4277-a99f-d69f88f3f77d/image.png)

## 1.7.1 Rasing an Exception

**raise** statement를 통해 강제적으로 예외를 발생시킬수 있다.

```python
def sqrt(x):
    if not isinstance(x, (int, float)):
        raise TypeError('x must be numeric')
    elif x < 0:
        raise ValueError('x must be positive')
```

> isinstance를 통해 첫번째 인수가 두번째 인수의 자식 Class instance인지 확인이 가능하다.

함수를 구현할 때, 얼마나 많은 에러를 체크해야 하는지는 논쟁의 여지가 있다. 타입과 값을 각 파라미터마타 체크하는것은 추가적인 실행 시간이 필요하여, 좀 극단적으로 말하지면 파이썬의 본질에 반하는 것처럼 보일 수 있다.

여기 Built-In Sum 함수를 보자. 이 Sum 함수는 풍부한 Error 체크를 하고 있다.
```python
import collections


def sum(values):
    if not isinstance(values, collections.Iterable): raise TypeError('values must be an iterable')
    total = 0
    for v in values:
        if not isinstance(v, int, float): raise TypeError('values must be numeric')
        total += v
    return total
```

> collections.Iterable에는 모든 파이썬의 iterable container들이 담겨있으며, for loop 수행 시, iterable함을 보장하기 위해 제공된다.

그런데, 다음 함수를 보자
```python
def sum(values):
    total = 0
    for v in values:
        total += v
    return total
```
흥미롭게도, 위와 같이 간단하게 구현해도 Built-In Sum함수와 완전히 똑같이 작동한다. Error Check가 없어도, TypeError가 에러 메세지와 함께 자동으로 발생한다.

따라서 이 책에서는 최대한 간단한 구현을 지향하고 있다. 우리는 최소한의 error check만을 이용하여 함수를 구현할 것이다.

<hr>

## 1.7.2 Catching an Exception

파이썬은 try-except을 이용하여 예외 발생을 catch할 수 있다.

```python
try:
	fp = open('myfile.txt')
except IOError as e:
	print('Unable to open the file: ', e)
```
파일을 찾을 수 없는경우, IOError가 발생하는데, except문에서 이 에러를 잡아서 print로 에러 메세지를 출력해준다.

만약, while이나 for loop 에서 except가 발생해도 계속 진행시키고 싶다면, pass를 이용하면 된다.

```python
except(ValueError, EOFError):
	pass
```
>pass : 아무것도 하지 말라는 의미 (continue라고 생각하면 된다)

만약 다음 코드와 같이 다중으로 except문이 존재한다고 해보자.

```python
age = -1
while age <= 0:
    try:
        age = int(input('Enter your age: '))
        if age <= 0:
            print('Invalid age')
    except ValueError:
        print('That is an invalid age specification')
    except EOFError:
        print('There was an unexpected error reading input')
        raise # EOFError을 다시 발생시킨다.
```

**만약, 상위에 있는 except문에서 에러를 잡은 경우, 하위에 있는 except문은 모두 건너뛴다. **

# 1.8 Iterators and Generators

### Iterators
```python
for element in iterable:
```

> iterator : 일련의 값들을 통해 반복문을 관리하는 object. 예를 들어 변수 i를 iterator로 선언했을 때, 각각의 호출은 __next__(i)를 통해 일어나며, 순차적으로 현재 존재하는 일련의 요소들을 접근해 나간다. 만약 __next__(i) 호출 시, StopIteraton Exception이 발생한다면 반복을 종료한다. (더 이상 요소가 존재하지 않음을 나타내는 exception)

> iterable : iter(obj)를 통해 iterator가 작동할 수 있는 요소의 집합

예를 들어 iterable한 list로 data = [1, 2, 4, 8]이 있다고 해보자. data는 iterable하기 때문에, __next__(data)호출이 가능하다. 만약 코드를 좀 줄이고 싶다면, i = iter(data)를 통해 i에게 iterable 요소의 주소값을 전달하고, __next__(i)를 통해 호출하면 똑같이 작동한다.

또한, 파이썬은 모든 값을 한 번에 저장하는 데이터 집합을 생성하지 않고도 값의 암시적 iterable 배열을 생성하는 함수 및 클래스를 지원한다.
예를들어, range()라는 함수인데, range(10000)을 호출하면 0부터 9999까지의 숫자 list를 반환하는 것이 아니라, iterable 범위 객체를 반환한다.
이 객체를 통해 한 번에 하나씩, 필요한 만큼만 값을 생성하게 된다.

이러한 방식을 **lazy evaluation(지연 평가 방식)** 이라고 하는데, 만 개의 정수를 저장하기 위해 메모리를 따로 할당하지 않고도 범위에 대한 루프를 iterator을 통해 실행할 수 있다. 그리고 루프가 중단되는 경우에도, 아직 파이썬 인터프리터가 사용되지 않은 값을 계산하는 시간이 필요가 없으므로 프로그램 실행 시간이 단축된다.

lazy evaluation에 대한 예시로, Dictionary에서 지원하는 함수 key(), values(), items()가 있으며, 각각 모든 키, 값, (key, value)쌍을 반환한다.

이들 함수가 실행될 때, range()처럼 명시적으로 값을 할당하는 리스트가 생성되는것이 아니라 iterable object가 생성되어 iterator로 접근할 때 마다 해당 값을 반환해주는 방식으로 효율적으로 실행된다.

<hr>

### Generators
파이썬에서 iterator을 가장 편리하게 구현할 수 있는 테크닉이 바로 Generator을 사용하는 것이다. Generator는 함수와 매우 비슷한 문법 구조를 가지고 있으며, **값을 반환하는 대신, 일련의 element를 가리키는 yield statement를 사용한다**

예를들어, 양의 정수의 모든 약수를 구하는 알고리즘을 구현한다고 해보자. yield를 사용하지 않는다면, 다음과 같이 구현하게 될것이다.
```python
def factors(n):
    result = []
    for k in range(1, n + 1):
        if n % k == 0:
            result.append(k)
    return result
```

이와 반대로, generator을 통해 구현하게 된다면 다음과 같다.
```python
def factors(n):
    for k in range(1, n+1):
        if n % k == 0:
            yield k 
```
값을 반환하는 return을 사용한 것 대신에, yield를 통해 k를 가리키는 요소를 생성하였다. 이렇게 구현하면, 파이썬에게 generator을 정의하는 것이라고 알릴 수 있다.

>Python에서 yield와 return을 같이 사용할수 없다!

generator에서 만약, 프로그래머가 100의 약수를 구하기 위해 다음과 같이 for loop을 돌렸다면,
```python
for factor in factors(n):
	print(factor)
```
generator 인스턴스가 생성되며, 각 loop 시도마다 파이썬은 yield statement를 통해 다음 값이 존재하지 않을때까지 값을 factor로 넘긴다. 

이 시점에서 Procedure는 일시적으로 중단되었다가, 다시 Generator에게 다른 값이 요청되면 다시 시작된다. 마지막으로 자연스럽게 yeild가 가리킬 요소가 더이상 존재하지 않는다면, StopIteration 예외가 발생하여 for loop이 종료된다.

<hr>

> 약수를 좀 더 효율적으로 구하는 방법 : 루트(n)까지 k를 탐색한다.

```python
def factors(n):
    k = 1  # 1은 항상 약수이니까
    while k * k <= n:
        if n % k == 0:
            yield k  # 약수 k를 생성하고
            if n // k != k: # 중복 제거
                yield n // k  # 그 k로 n을 나누면 또 약수니까 이것도 생성해버린다
        k += 1
    if k * k == n:  # n이 제곱수라면
        yield k


for factor in factors(100):
    print(factor)
```

> 피보나치를 Generator로 구현해보자

```python
def fibonacci(n):
    a = 0
    b = 1
    cnt = 0
    while cnt < n:
        yield a
        next_fib = a + b
        a = b
        b = next_fib
        cnt += 1


for n in fibonacci(10):
    print(n)

```

<hr>

# 1.9 Additional Python Conveniences

파이썬은 깔끔하고, 정확한 코드 작성을 위해 여러가지 편의 기능을 제공한다. 이러한 문법들은 이전에 우리가 배운 문법들과 기능이 똑같으며, 같은 결과를 제공한다.

<hr>

## 1.9.1 Conditional Expressions

조건 표현식은 다음과 같이 작성한다

```python
expr1 if condition else expr2
```
만약 condition을 만족한다면 expr1을 수행하고 그렇지 않다면 expr2를 수행한다는 의미이다. (C, Java의 3항 연산자 기능)

<hr>

## 1.9.2 Comprehension Syntax

```python
[expression for value in iterable if condition]
```
위 표현식은 아래 for loop과 동일하다
```python
result = []
for value in iterable:
	if condition:
    	result.append(expression)
```

>1. 제곱수를 저장하는 list를 생성 시, 다음과 같이 작성한다.

```python
squares = [k*k for k in range(1, n + 1)]
```

> 2. 약수를 저장하는 list를 생성 시, 다음과 같이 작성한다.

```python
factors = [k for i in range(1, n + 1) if n % k == 0]
```

> 3. n의 약수들의 총합은 다음과 같이 작성한다.

```python
sum = (k for k in range(1, n + 1) if n % k == 0)
```
![](https://velog.velcdn.com/images/calzone0404/post/d3ec20d0-3244-42b8-9de7-e8d4ca8c142b/image.png)

<hr>

## 1.9.3 Packing and Unpacking of Sequences

파이썬은 튜플이나 다른 sequence 타입들을 포함해서 두가지 편의기능을 제공한다.

<hr>

### Packing

만약 일련의 comma-seperated 표현식이 주어졌을 때, 파이썬은 단일 tuple로 간주하여 묶어버린다 (Packing)

```python
data = 2, 4, 6, 8
# data = (2, 4, 6, 8)
```

<hr>

또한, return 시 여러개의 값을 comma로 분리된 상태로 전달하면 다음과 같이 자동으로 packing해준다.
```python
return x, y
# 전달받는 값은 (x, y)가 된다
```

<hr>

### Unpacking

packing이 수행했던것과 정반대로, 묶여있는 형태의 sequence type을 각각의 변수에 unpacking 할 수 있다.

```python
a, b, c, d = range(7, 11)
# a = 7, b = 8, c = 9, d = 10
```

이러한 기능을 사용하는 예로 divmod() built-in 함수가 있는데, divmod는 return을 다음과 같이 수행한다.
```python
return (a // b, a % b)

q, r = divmod(a, b)
# q에 몫이 저장되고, r에 나머지가 저장된다
```

마찬가지로 for loop에도 활용이 가능하다.
```python
for x, y in [(1, 2), (3, 4), (5, 6)]
```

<hr>

# 1.10 Scopes and Namespaces

파이썬에서 x + y라는 구문을 계산할 때, 변수명 x와 y는 반드시 사용하기 이전에 변수명(Name)을 선언한 상태여야만 한다. 이 때, 만약 이러한 변수명이 없다면 NameError exception이 발생한다.
식별자가의 값을 결정하는 과정은 **name resolution**이라고 한다.

식별자에 값이 할당되었을 때, 그 변수의 정의는 특정 scope 안에서만 수행된다.
> 즉, main 함수 내부에 선언된 변수는 main 내부에서만 사용할  수 있으며, func라는 함수의 내부에 선언된 변수는 func의 내부에서만 사용할 수 있다(**지역변수**)

>가장 상위 레벨의 할당이 일어나는 scope를 **global scope(전역변수)**이라고 한다.

파이썬에서 각각의 구별된 scope는 **namespace**라고 불리우는 추상화를 사용한다. namespace는 주어진 scope에서 정의된 모든 식별자들을 관리해준다.
아래 사진은 두개의 namespace에 대한 사진이다. 하나는 main()함수에서 함수로 전달하는 인자들이 관리되는 namespace라고 생각하고, 다른 하나는 함수 내부에서 선언된 변수들이 관리되는 namespace라고 생각하자
![](https://velog.velcdn.com/images/calzone0404/post/88c0e61b-3502-4107-8dec-3fe82b5f4032/image.png)


파이썬은 namespace를 dictionary로 구현하여 변수명을 key, 그에 할당되는 값을 value로 하여 저장한다. 파이썬은 주어진 namespace를 보여주는 몇가지 기능을 제공하고 있다.
>dir() : 주어진 namespace의 식별자들의 이름을 반환한다.
>
>vars() : 주어진 namespace의 식별자들을 full dictionary 형태로 반환한다.

기본적으로 dir()과 vars() 실행시, 지역변수로 할당된 식별자를 namespace를 보여준다.

### First-Class Objects

**일급 객체**라는 용어는 주로 식별자에 할당할 수 있거나, 함수에 매개변수로 전달하거나, 함수에서 반환될 수 있는 유형의 인스턴스를 의미한다. 즉, 우리가 주로 사용해왔던 iinteger, string 등.. 다루고 있는 **모든 데이터타입이 일급 객체이다**

파이썬에서는 함수명과 클래스 이름도 일급객체로 취급한다.
```python
speak = print
speak("AYAAYAY")
```
위 코드를 보면, 함수명을 식별자에 할당하여, 그 식별자를 통해 print()를 수행했다.

이러한 경우에, 우리는 새로운 함수를 만들지 않았다. 단지 우리는 speak을 존재하는 print함수의 이름로 바꿔서 명명한것 뿐이다. 따라서 우리는 파이썬의 함수를 하나의 파라미터화하여 전달할 수 있음을 알 수 있다.

# 1.11 Modules and the Import Statement

파이썬 버전에 따라 다르겠지만, 현재 파이썬의 built-in 함수는 대략 130개 ~ 150개 정도이다. 그런데, 이러한 built-in 함수 이외에도 표준 Python 배포에는 **module**이라고 하는 추가 라이브러리에 구성되어 있는 수만개의 함수 및 클래스가 정의되어있다.

예를 들어, 수학 모듈에 대해 생각해보자. math.py에는 abs, min, max와 같은 몇몇 수학함수들이 존재한다. 만약 우리가 작업하고있는 main.py에서 math.py에 있는 함수(모듈)을 사용하고 싶다면, **from, import**를 통하여 가져올 수 있다.

### Creating a New Module
새로운 모듈을 만들기 위해서는 .py 접미사가 붙은 파일에 원하는 함수를 정의하고 from ~ import ~ 를 통해 가져오면 된다.

> 모듈이 스크립트로 직접 호출되는 경우에는 실행되지만, 모듈로 실행되는 경우에는 실행되지 않는 코드는 다음 조건문을 배치해야 한다.
```python
if __name__ == "__main__":
```
