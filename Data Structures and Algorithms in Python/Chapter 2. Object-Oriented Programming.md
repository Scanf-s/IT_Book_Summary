# 2.1 Goals, Principles, and Patterns

이름에서 유추할 수 있듯이, 여기서 말하고자 하는 주요한 내용은 바로 **Obejcts**라고 불리우는 객체지향 파라다임을 소개하는 것이다. 
각 오브젝트는 **class의 instance**라고 불린다. 각 class들은 외부세계를 정확하고 일관적인 시각으로 표현할 수 있는 instance를 생성한다. 클래스는 **instance variables(data members)**와 **member functions**으로 구체화하여 정의한다.

<hr>

## 2.1.1 Object-Oriented Design Goals

### Robustness (견고함)
모든 훌륭한 프로그래머들은 모든 가능한 input에 대해 올바른 output을 도출해 내는 프로그램을 개발하기를 원한다. 또한, 프로그래머들은 **견고한** 소프트웨어를 원한다. 즉, unexpected input들에 대해서도 항상 handling을 할 수 있어야 한다.

예를 들어서, 만약 프로그램이 양의 정수를 입력받는 것을 예상하였으나, 음수를 입력받았다면, 프로그램은 이 예외에 대해서 아주 우아하게 처리할 수 있어야 한다.

생명과 직결되는 프로그램의 경우, 가장 명시해야할 점은 만약 프로그램이 **robust**하지 않다면, 프로그램 에러가 부상을 야기하거나 생명을 앗아갈수도 있다는 점이다.
<hr>

### Adaptability (적용성)
웹브라우저, 인터넷 검색 엔진과 같은 현대 소프트웨어 프로그램들은 수년간 사용되고 있는 아주 큰 프로그램들을 포함하고 있다. 따라서 소프트웨어는 시간이 지남에 따라 변화하는 조건과 환경에 따라 진화해왔다. 그러므로, 소프트웨어 퀄리티의 가장 중요한 목표는 **adaptability(evolvability)**를 지향하는 것이다.

이와 관련된 개념으로, OS나 Hardware가 다르더라도 항상 소프트웨어가 실행될수 있어야 한다는 **Portability**가 있다. 파이썬으로 개발된 프로그램의 가장 큰 장점은 언어 자체가 **Portability**하기 때문에 어디에서든 실행이 가능하다.

<hr>

### Reusability (재사용성)
또 다른 중요한 점은, 소프트웨어는 재사용이 가능해야 한다. 즉, 똑같은 코드는 다른 시스템의 프로그램에서 구성요소로 사용 될 떄, 항상 사용이 가능해야 한다는 것이다. 품질이 좋은 프로그램을 개발한다는 것은 비용이 많이 드는 작업이며, 이 비용은 재사용성을 통하여 추후 소프트웨어 개발 비용을 줄일 수 있다.

<hr>

## 2.1.2 Object-Oriented Design Principles

### Modularity (모듈화)

현대 소프트웨어 시스템은 대부분 여러가지 구성요소들이 서로 상호작용하며 전체 시스템을 적절히 작동하도록 구성되어 있다. 이렇게 상호작용을 올바르게 작동하기 위해서는 모든 구성요소가 잘 구성되어져 있어야만 한다.

집을 예시로 들어보면, 집을 구성하는 전기 시설, 난방, 기둥, 건축자재 등등의 요소가 서로 상호작용하여 구성되어 있다. 따라서 집이나 아파트를 짓기 위한 건축 디자인 시스템을 거대한 별도의 선들, 환풍구, 파이프, 보드로 보는것 보다는, 별도의 모듈이 서로 상호작용한다고 보아야한다.

마찬가지로 모듈화를 소프트웨어 시스템에 적용하는 것은 아주 강한 프레임워크를 구성고, 구현을 명료하게 하는 것에 도움을 준다. 파이썬에서 우리는 이미 module은 함수들과 클래스들이 서로 가까이 관련되어있는 collection임을 이미 알고 있다. 파이썬의 표준 라이브러리중에 예시로, math라는 모듈이 있다. math 모듈을 통해서 주요한 수학적 상수 또는 함수들을 사용할 수 있다. 또, os 모듈도 사용할 수 있는데, 이 모듈은 OS와 상호작용할 수 있도록 도와준다.

이러한 모듈들의 사용으로 인해, 소스코드의 Robustness를 지킬 수 있다. 왜냐하면, 소스코드 테스트, 여러가지 구성요소를 디버그하는데 아주 쉬워지기 때문이다. 또, 버그를 잡을때에도 별도로 분리된 구성요소로 들어가서 잡으면 되기 때문에, 유지 보수가 쉬워진다.

또한, 소스코드의 재사용성에도 큰 도움을 주는데, 만약 소프트웨어 모듈이 일반적인 방법으로 작성되었다면, 모듈은 다른 context들에서 필요로 할 때, 재사용이 가능해진다. 이것은 특히 자료구조를 공부할때와 연관이 깊은데, 충분한 추상화와 일반화가 수많은 프로그램에서 사용이 가능하다는 의미를 갖는다.
<hr>

### Abstraction (추상화)
추상화란, 복잡하게 구성된 시스템을 근본적인 부분으로 최적화하는 것이다. 일반적으로 시스템의 일부분을 묘사하는것은 그 일부분의 기능을 설명하는 이름을 붙여주는 것이다. 자료구조 설계에 추상화 파라다임을 붙여주는 것이 바로 **ADT(Abstract Data Type)**이다. ADP는 자료구조의 수학적 모델으로, 어떤 타입의 데이터가 저장되어야 하는지, 어떠한 작업 또는 기능을 수행할 것인지, 작업을 할 때 어떠한 파라미터가 필요한지에 대해서 기술한다.

>ADT는 어떠한 기능이 **어떻게** 동작하는지 관심을 두는게 아니라, **무엇을**하는 기능인지에 대해 서술하는 것이다.


일반적으로 파이썬은 추상 데이터 타입을 **ABC(abstract base class)**를 통해서 지원하고 있따. ABC는 인스턴스화될 수 없지만, 하나 또는 하나 이상의 추상화된 메서드를 구현해야만 하는 공통된 메서드들이 존재한다. 다시 말해서, ABC는 하나 또는 하나 이상의 ABC를 구현하는 **concrete class**들에게 상속하여 ABC에 존재하는 추상화 메서드를 구현하도록 해야 한다.
<hr>

### Encapsulation (캡슐화)

캡슐화란, 소프트웨어를 구성하는 수많은 구성요소들의 내부적인 데이터 또는 요소들을 외부로 공개해서는 안된다는 의미를 가지고 있다. 

캡슐화의 장점은 어떤 프로그래머 A가 자유롭게 프로그래밍을 할 때, 다른 프로그래머들이 A의 코드를 부정확하게 사용하는 것을 방지할 수 있게 된다. 즉, 다른 프로그래머들은 A가 선언한 public으로 선언된 구성요소들만 사용할 수 있다. 따라서 캡슐화는 소스코드의 견고성 및 재사용성을 확보할 수 있다고 말할 수 있다. 

따라서 버그가 발생해도, A의 소스코드가 견고성과 재사용성이 보장되어 있다면, 다른 프로그래머가 A의 소스코드를 이용하여 개발할 때, 버그가 발생하면 자신이 개발한 소스코드에서만 버그를 찾아내면 되는 편리함이 생긴다.
<hr>

## 2.1.3 Design Patterns

객체지향 디자인은 재사용성, 견고성, 적용성을 용이하게 한다. 그러나 좋은 코드를 설계하는것은 단순히 객체지향 방법론을 이해하는것 이상의 것을 필요로 한다.

컴퓨터학 연구자 및 실무자들은 객체지향 프로그램 설계시, 매주 정확하게 작동하는 효율적인 개념들과 방법들을 개발해왔다. 이 책에서 소개하고자하는 내용이 바로 이 특별한 디자인 패턴들을 소개하는 것이다. 이 패턴은 수많은 상황에서 일반적으로 적용 가능한 정형화된 템플릿을 제공한다. (자료구조랑 알고리즘을 배우는 이유)

<hr>

# 2.2 Software Development

일반적으로 소프트웨어 개발은 아래 세 단계를 거친다고 할 수 있다.

1. 설계
2. 구현
3. 테스트 및 디버깅

이 챕터에서 위 세 단계에 대해 소개하고자 한다.

<hr>

## 2.2.1 Design

객체지향을 적용한 프로그래밍을 개발할 때, Design step은 가장 중요한 요소이다.

Design 시 중요한 세가지 규칙에 대해 알아보고, 어떻게 Class를 설계해야하는지 알아보도록 하자.

### Responsibilities

하나의 작업을 여러 Actor들로 나누어서, 책임을 분산시켜야 한다.

### Independence

각각의 클래스들은 서로 독립적으로 작업해야한다. 각 Class가 프로그램의 일부 측면에 대해 자율성을 갖도록 학급 간 책임을 세분화해야 한다.

### Behaviors

모든 class끼리 잘 상호작용하기 위해서는 각 class들이 수행하는 행동들을 매우 신중하게 결정해야 한다. 이러한 행동은 class의 method와 interface를 implement를 함으로써 구현할 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/e39d9d04-062f-4c77-978b-fad43bd70e9a/image.png)

위 사진을 보면, CreditCard Class가 어떠한 행동을 수행하고, 어떤 책임이 있는지 파악할 수 있다.

## 2.2.2 Pseudo-Code

의사코드는 설계를 구현하고자하기 전에, 중간 단계로, 프로그래머가 **인간 수준의 언어로 자신이 구현하고자 하는 코드를 표현한 것이다.** 의사코드는 컴퓨터 언어가 아니지만, 우리가 일상생활에서 사용하는 언어보다는 조금 더 구조가 컴퓨터 언어쪽에 가깝다고 할 수 있다.

## 2.2.3 Coding Style and Documentation

- 파이썬 코드블록은 반드시 space 4번정도로 들여써야 한다. (이 책에서는 공간이 좁아서 2번정도로 들여썼다)

- 변수 이름은 의미있고, 확실하고, 눈에 잘 띄는 이름으로 짓자.

  - 클래스명은 반드시 첫글자는 대문자로, 명사형으로 작성해야한다.
  
  - 함수명이나, 멤버함수 이름은 반드시 소문자로 쓰며, camelCase 또는 언더바를 활용하여 작성하자
  > 자바는 camelCase, 파이썬은 언더바를 주로 활용하는것 같다
  
  - 변수 이름 (파라미터, 인스턴스 변수, 지역 변수)은 소문자로 짓고, 명사형으로 작성하자
  
  - 어떠한 상수값같은 고정된 값에 대한 식별자 이름은 모두 대문자로 작성하자.
  
  - 파이썬에서 변수이름 앞에, 언더바가 있는것은 encapsulation을 적용한 것이다 (private)

- 모호하거나 혼란을 줄 수 있는 코드에는 반드시 적절한 주석을 달아놓자.

## 2.2.4 Testing and Debugging

### Testing

프로그램을 작성할 때, 가장 필수적인 부분이 바로 테스트 계획을 짜는것이다. 물론, 모든 입력에 대해서 프로그램이 올바르게 동작하는지에 대해서 검사하는것은 불가능한 일이다. 

하지만 프로그램이 실행 될 때, 대표적으로 들어오는 입력값에 대해서는 테스트를 해보아야한다. 최소 조건으로 프로그래머는 모든 클래스의 메소드가 최소 한번은 테스트를 거쳐야한다.

프로그램은 주로 특이한 입력값 때문에 오류가 발생한다. 이러한 케이스들에 대해서 우리는 주의깊게 테스트를 해보아야 한다. 

예를 들어서, 입력된 정수들을 정렬하는 메서드를 테스트한다고 해보자. 이때, 프로그래머는 다음과 같은 포인트에 대해서 테스팅을 해보아야 한다.

- 빈 배열이 들어온 경우
- 하나의 원소만 존재하는 경우
- 모든 원소가 동일한경우
- 배열이 이미 정렬되어 있는 경우
- 배열이 거꾸로 정렬되어 있는 경우

이것 이외에도, 프로그래머는 프로그램 테스트 시, 아주 큰 크기의 랜덤으로 생성된 입력값에 대해서도 테스트해보아야 한다.

<hr>

프로그램의 클래스와 함수 간 의존성이 계층 구조를 형성한다.
이게 무슨 말이냐면, A라는 함수가 B함수를 호출하거나, A 함수가 클래스 B의 인스턴스를 매개변수로 사용하는 경우, A는 계층 구조에서 B보다 상위에 있다고 할 수 있다.

> **Top-down** 테스트 : 프로그램 계층 구조의 상단에서 하단으로 진행되고, Stubbing을 사용하는 기법을 사용한다. Stubbing은 하위 단계의 구성요소를 Stub으로 대체하여 원래 컴포넌트의 기능을 테스트하는 기법이다.

예를 들어서, A함수가 파일의 첫 줄을 얻기 위해 B함수를 호출하였을 때, A를 테스트하는 동안 B를 고정된 문자열을 반환하는 Stub으로 대체하는 경우이다.

> **Bottom-up** 테스트 : **하향 테스트와 반대로, 상향 테스트는 하위 레벨 컴포넌트로부터 시작하여 상위 레벨 컴포넌트로 진행된다.**

예를 들어, 다른 함수를 호출하지 않는 가장 하위 레벨의 함수들이 먼저 테스트되고, 그 다음에는 오직 하위 레벨 함수들만을 호출하는 함수들이 테스트된다. 

마찬가지로, 다른 클래스에 의존하지 않는 클래스는 그러한 의존성을 가진 다른 클래스보다 먼저 테스트될 수 있다(바텀업이기 때문에).

이러한 테스트 형태는 일반적으로 단위 테스트로 묘사되며, 특정 컴포넌트의 기능이 더 큰 소프트웨어 프로젝트와 격리되어 테스트된다. 만약, 이 전략을 제대로 사용하면 이미 철저하게 테스트되었어야 할 하위 레벨 컴포넌트에 의존하는 테스트 중인 컴포넌트의 오류 원인을 더 잘 파악할 수 있게 된다.

<hr>

파이썬은 자동화된 테스트 기능을 제공하는 형식이 존재한다. 함수들이나 클래스들이 정의되어 있는 모듈에서, 다음과 같은 코드를 추가하면, 그 파일 자체로만 테스트를 할 수 있다.

```python
if __name__ == "__main__":
	테스트코드 작성
```

<hr>

### Debugging
가장 간단한 디버깅 테크닉은 print문을 주구장창 코드사이사이에 끼워넣는 것이다. 그러나, 이 방법의 문제점은 결국에 언젠가는 print문을 전부 지워버려야 한다는 점이다.

차라리 이 방법보다는, 디버거를 사용하는것이 더 편리하다. breakpoint를 설정하는것을 통해 원하는 지점에서 어떻게 동작하는지 확인할 수 있다.

Python은 pdb라는 디버깅 모듈이 존재한다.

<hr>

# 2.3 Class Definitions

```python
class CreditCard:
    def __init__(self, customer, bank, acnt, limit):
        self._customer = customer
        self._bank = bank
        self._account = acnt
        self._limit = limit
        self._balance = 0  # 잔고

    def get_customer(self):
        return self._customer

    def get_bank(self):
        return self._bank

    def get_account(self):
        return self._account

    def get_limit(self):
        return self._limit

    def get_balance(self):
        return self._balance

    def charge(self, price): # 충전 함수
        if price + self._balance > self._limit:  # 한도 초과시
            return False
        else:
            self._balance += price  # 사용 금액 추가
            return True

    def make_payment(self, amount):  # 결제 함수
        self._balance -= amount 
```
<hr>

### The Constructor

```python
나라사랑카드 = CreditCard("Scanf-s", "국민은행", "1234 1234 1234 1234", 1000)
```

<hr>

### Encapsulation

변수명에 언더바가 하나 앞에 존재하는것이 바로 Python의 private 변수라고 생각하면 된다. 즉, 해당 클래스 내부에서만 사용하는 변수이다.

<hr>

### Error Checking

사실 CreditCard는 견고성을 가지고있지는 않다. 왜냐하면, 첫째로 들어오는 값 (파라미터)에 대한 명시적인 타입체크를 하지 않았다. 

만약 사용자가 visa.charge("파이썬")과 같이 비정상적인 입력을 전달했을경우, 이 코드는 crash가 발생할 것이다. 따라서 이러한 에러를 방지하기 위해 TypeError와 같은 풍부한 Error Checking이 필요하다.

<hr>

### Testing the Class

```python
class CreditCard:
    def __init__(self, customer, bank, acnt, limit):
        self._customer = customer
        self._bank = bank
        self._account = acnt
        self._limit = limit
        self._balance = 0  # 잔고

    def get_customer(self):
        return self._customer

    def get_bank(self):
        return self._bank

    def get_account(self):
        return self._account

    def get_limit(self):
        return self._limit

    def get_balance(self):
        return self._balance

    def charge(self, price): # 충전 함수
        if price + self._balance > self._limit:  # 한도 초과시
            return False
        else:
            self._balance += price  # 사용 금액 추가
            return True

    def make_payment(self, amount):  # 결제 함수
        self._balance -= amount


if __name__ == '__main__':
    wallet = []
    wallet.append(CreditCard("김씨", "기업은행", "1234 1234 1234 1111", 2500))
    wallet.append(CreditCard("박씨", "국민은행", "1234 1234 1234 1112", 3500))
    wallet.append(CreditCard("이씨", "신한은행", "1234 1234 1234 1113", 5000))

    for val in range(1, 17): # 돈 충전
        wallet[0].charge(val)
        wallet[1].charge(val)
        wallet[2].charge(val)

    for c in range(3): # 테스팅
        print('Customer = ', wallet[c].get_customer())
        print('Bank = ', wallet[c].get_bank())
        print('Account = ', wallet[c].get_account())
        print('Limit = ', wallet[c].get_limit())
        print('Balance = ', wallet[c].get_balance())
        while wallet[c].get_balance() > 100:
            wallet[c].make_payment(100)
            print('New Balance = ', wallet[c].get_balance())
        print()
```
![](https://velog.velcdn.com/images/calzone0404/post/835a36a4-7d65-4ea0-836f-1b42ce944ed4/image.png)

<hr>

## 2.3.2 Operator Overloading and Python's Special Methods

파이썬은 built-in class들을 통해 연산자 오버로딩을 지원한다. 예를 들어서, a + b를 정수끼리의 연산으로 볼 수 있지만, a와 b가 만약 배열이라면, 두 배열이 합쳐지는 결과를 반환한다.

우리가 새로운 클래스를 만들 때, a + b와 같은 연산자 오버로딩을 지원할지에 대해 정해야 한다. 기본적으로 + 연산자는 새롭게 만든 클래스에서는 정의되지는 않는다. 연산자 오버로딩을 해주어야 하는데, 예를 들어 + 연산자는 **\_\_add\_\_** 메서드를 통해 연산자 오버로딩을 할 수 있다.

즉, a + b라는 연산을 수행하면, 연산자 오버로딩에 의해 a.\_\_add\_\_(b)가 호출되는 것이므로, 다른 연산자도 오버로딩 함수를 통해 간단하게 구현이 가능하다.

만약 클래스가 연산자 오버로딩과 관련된 메서드를 구현하지 않았는데, 연산자를 통해 연산을 하게 된다면, 당연히 Exception을 발생시킨다.

그런데, 따로 구현하지 않아도 연산자 오버로딩을 지원하는 메서드가 존재하는데, 바로 **\_\_bool\_\_**이다. 아래 코드와 같이, 우리가 if문을 사용할 때 자동으로 기본으로 정의된 \_\_bool\_\_함수가 실행된다.
```python
if asdf:
	asdf
```
<hr>

### 연산자, 비연산자 오버로딩 지원 함수

![](https://velog.velcdn.com/images/calzone0404/post/dcf6b220-a984-4841-9c9b-0f2efc1e9242/image.png)

<hr>

### Example: Multidimensional Vector Class

```python
class Vector:
    def __init__(self, d):
        self._coords = [0] * d  # n차원 벡터 생성

    def __len__(self):
        return len(self._coords)

    def __getitem__(self, i):
        return self._coords[i]

    def __setitem__(self, i, val):
        self._coords[i] = val

    def __add__(self, other):
        if len(self) != len(other):
            raise ValueError("Vector must be of equal length")
        result = Vector(len(self))
        for j in range(len(self)):
            result[j] = self[j] + other[j]
        return result

    def __eq__(self, other):
        return self._coords == other._coords

    def __ne__(self, other):
        return not self == other

    def __str__(self):
        return '<' + str(self._coords)[1: -1] + '>'


if __name__ == '__main__':
    v1 = Vector(3)
    for i in range(v1.__len__()):
        v1.__setitem__(i, i + 1)
    print(v1.__str__())

    temp = Vector(3)
    for i in range(temp.__len__()):
        temp.__setitem__(i, i + 1)
    print(temp.__str__())

    v2 = Vector(3)
    for i in range(v2.__len__()):
        v2.__setitem__(i, i + 2)
    print(v2.__str__())

    v3 = v1 + v2
    print(v3.__str__())

    print(v1 != v2)
    print(v1 == temp)
```
![](https://velog.velcdn.com/images/calzone0404/post/60cf20af-68b5-4eb0-a632-831a589707be/image.png)
<hr>

## 2.3.4 Iterators

반복은 자료구조 설계시 아주 중요한 개념이라고 할 수 있다. 즉, 컬렉션을 위한 **iterator**는 하나의 핵심 기능을 제공한다: **\_\_next\_\_** 함수가 반환하는 자료구조의 다음 순서의 요소를 반환하는일을 도와준다. 만약, \_\_next\_\_함수가 더이상 반환할 요소가 존재하지 않는다면, **StopIteration**을 반환하고 종료한다.

우리는 운이 좋게도, Iterator class를 직접 구현하는 일은 매우 드물것이다. 우리는 대신, **Generator** 문법을 이용하여 자동적으로 생성된 값에 대한 Iterator를 만들것이다.

또한 파이썬에서는 자동적으로 iterator를 구현한 함수 및 클래스를 제공하는데, 바로 **\_\_len\_\_과 \_\_getitem\_\_**이다.


```python
class SequenceIterator:
    def __init__(self, sequence):
        self._seq = sequence
        self._k = -1  # 배열 index

    def __next__(self):
        self._k += 1
        if self._k < len(self._seq):
            return self._seq[self._k]
        else:
            raise StopIteration()

    def __iter__(self):
        return self


if __name__ == '__main__':
    seq = SequenceIterator(range(10))
    for i in range(10):
        print(seq.__next__())
```
<hr>

## 2.3.5 Example: Range Class

파이썬 3 출시 이전에, Python 2에서의 range함수는 원래 함수로 구현된 기능이었다. 예를 들어, range(2, 10, 2)는 리스트 [2, 4, 6, 8]을 반환한다.
for loop에서 사용될 때에는, list를 생성하는 작업이 반드시 필요했기 때문에, for loop range(10000)과 같은 작업을 수행하면 메모리 측면에서 단점이 명확했다.

그러나, Python3에서는 Python2의 range 함수의 방식에서 벗어나서, Python2의 xrange와 비슷한 기능을 하도록 구현하였다. 
Python3의 range함수는 **lazy evaluation**이라는 전략을 사용한다. 새로운 리스트를 만들어서 메모리를 할당하는 과정을 수행하지 않고, 원하는 범위의 요소들을 반환할 수 있게 되었다.

```python
class Range:
    def __init__(self, start, stop=None, step=1):
        if step == 0:
            raise ValueError("Step must be greater than 0.")
        if stop is None:
            start, stop = 0, start  # start = 0, stop = start

        self._length = max(0, (stop - start + step - 1) // step)  # 요소의 총 개수 계산

        self._start = start
        self._step = step

    def __len__(self):
        return self._length

    def __getitem__(self, index):
        if index < 0:
            index += len(self)

        if not 0 <= index < self._length:
            raise IndexError("Index out of range.")

        return self._start + index * self._step
```
<hr>

# 2.4 Inheritance

![](https://velog.velcdn.com/images/calzone0404/post/582348a2-6114-48f0-8036-727b0cf65734/image.png)

소프트웨어 패키지를 가장 자연스럽게 구현하는 방법은 가장 상위 계층부터 아래로 내려가면서, 추상적인 구현이 점점 구체화되는 계층적인 구조로 구현하는 것이다.

객체지향 프로그래밍은 상속을 이용하여 구현할 수 있다. 상속은 상위 클래스로부터 내려온 내용들을 점점 구체화할 수 있도록 해주는 기능이다.

객체지향 용어로, 현재 존재하는 클래스를 **base class, parent class, super class**등으로 불리우고, 새롭게 상속하여 정의하는 클래스를 **subclass, child class**라고 부른다.

> 서브클래스에서 자신의 부모 클래스로부터 내려온 메서드를 자신이 새롭게 정의하는 행위를 **오버라이드**라고 한다.

<hr>

# 2.4.1 Extending the CreditCard Class

```python
class CreditCard:
    def __init__(self, customer, bank, acnt, limit):
        self._customer = customer
        self._bank = bank
        self._account = acnt
        self._limit = limit
        self._balance = 0  # 잔고

    def get_customer(self):
        return self._customer

    def get_bank(self):
        return self._bank

    def get_account(self):
        return self._account

    def get_limit(self):
        return self._limit

    def get_balance(self):
        return self._balance

    def charge(self, price): # 충전 함수
        if price + self._balance > self._limit:  # 한도 초과시
            return False
        else:
            self._balance += price  # 사용 금액 추가
            return True

    def make_payment(self, amount):  # 결제 함수
        self._balance -= amount


class PredatoryCreditCard(CreditCard):

    def __init__(self, customer, bank, acnt, limit, apr):

        super().__init__(customer, bank, acnt, limit)  # call super constructor
        self._apr = apr

    def charge(self, price):
        #  충분한 신용 한도가 존재한다고 가정
        #  구매가 신용 한도를 초과하여 거부될 경우, 잔고에 5달러의 수수료를 부과하는 알고리즘 추가
        #  충전이 승인되면 True
        #  충전이 거절되면 False

        success = super().charge(price)
        if not success:
            self._balance += 5
        return success

    def process_month(self):
    # 매월 잔고에 대해 이자를 계산하고 적용함.
    # 이자율은 apr을 기준으로 하여 월별로 계산하고, 
    # 잔고가 양수일 경우에만 이자가 적용됨.
        if self._balance > 0:
            monthly_factor = pow(1 + self._apr, 1/12)
            self._balance *= monthly_factor
```

### Protected Members

위에 정의한 PredatoryCreditCard 상속 클래스에서는, CreditCard 클래스의 멤버인 \_balance에 바로 접근하고 있다. 우리는 앞에서 배웠을 때, 언더바가 한개 앞에 붙여진 변수명은 public이 아니라고 알고있다. 따라서 이렇게 접근하는게 괜찮을지 궁금증이 생길 수 있다.

>일반적인 클래스를 사용하는 유저는 이렇게 해서는 안되지만, Superclass를 상속한 subclass는 이렇게 언더바 멤버 변수에 접근하는것이 가능하다. 

여러 객체지향 언어 Java, C++에서는 public이 아닌 멤버들을 protected 또는 private으로 구별하고 있다.

>protected로 선언된 변수는 sublcass에서 접근이 가능하며, private로 선언된 변수는 public과 subclass에서 접근이 불가능하다.

따라서 \_balance << 이렇게 선언된 멤버변수는 **private**가 아니라 **protected**이다.
>
private처럼 기능하는 변수를 만들고싶다면 언더바 두개를 변수명 앞에 붙여주자.

물론 파이썬은 공식적으로 접근제어자를 제공하고 있지는 않다. 단순히 언더바 1개를 protected로 취급하고, 언더바 2개를 private로 취급할 뿐이다.

<hr>

## 2.4.2 Hierarchy of Numberic Progressions

Progression class를 통해 상속에 대한 예시를 드는 소챕터이다.

<hr>

## 2.4.3 Abstract Base Class

우리가 상속 계층의 한 구조로서 클래스의 그룹을 정의할 떄, 코드의 반복을 피할 수 있는 방법 중 하나는, **최상위 클래스 또는 상위 클래스에서 공통적인 기능을 수행하는 함수를 정의해놓고, 다른 클래스들이 필요할 때, 상속하여 구현하는 추상 클래스를 정의하는 것이다**

추상클래스는 추상메서드를 구체적으로 구현하기 위해 상속받는 클래스의 base가 된다. 

> 추상화는 다형성을 추구하는 가장 좋은 방법이다.

이 책에서 추상클래스에 대해 다루는 이유는, 파이썬이 제공하는 컬렉션 모듈이 여러 사용자 정의 자료구조를 정의하기 위해 도움을 주는 ABC를 제공하고 있기 때문이다.

예를들어, collections.Sequence ABC는 Python의 list, str, tuple 클래스의 공통적인 행동을 정의하는 추상 클래스이다.

```python
from abc import ABCMeta, abstractmethod
# 파이썬에서 추상 키워드를 사용하려면 위에있는 클래스를 가져와야한다.


class Sequence(metaclass=ABCMeta):

	# 추상화 메서드 선언
    @abstractmethod
    def __len__(self):
        """ Return the length of the sequence."""

	# 추상화 메서드 선언
    @abstractmethod
    def __getitem__(self, index):
        """ Return the element at the given index."""

    def __contains__(self, item):
        for i in range(len(self)):
            if self[i] == item:
                return True
        return False

    def index(self, val):
        for i in range(len(self)):
            if self[i] == val:
                return i
        raise ValueError("Value not found in sequence")

    def count(self, val):
        cnt = 0
        for i in range(len(self)):
            if self[i] == val:
                cnt += 1
        return cnt

```

<hr>

# 2.5 Namespaces and Object-Orientation

> **Namespace**는, 특정 범위 내에서 정의된 모든 식별자를 관리하는 추상화이며, 각 이름을 그와 연관된 값에 매핑한다.
>
> **파이썬의 Namespace**는, 변수나 함수와 같은 식별자들이 어디에서 사용되는지를 명확히 하고, 이름 충돌을 방지하는 데 중요한 역할을 합니다. 예를 들어, 같은 이름을 가진 여러 변수가 다른 네임스페이스에서 독립적으로 존재할 수 있다. (다른 모든 언어들도 마찬가지이긴 함)

## 2.5.1 Instance and Class Namespaces

**Instance namespace**는, 개별 객체들의 구체적인 속성을 관리하는 역할을 가진다.

예를 들어서, CreditCard class를 선언하여 새로운 인스턴스를 만들 때 마다, 각 인스턴스들은 별도의 balance, acnt, limit 등등...을 가진다. 즉 각 credit card instance는 별도로 지정된 namespace를 가지고 있는 셈이다.

또, 각 클래스에 **class namespace**가 정의되어 있다. 이 namespace는 단일 클래스에서 생성된 모든 인스턴스에의해 공유된 멤버들을 관리하거나, 특별한 인스턴스 없이 사용된다.

<hr>

![](https://velog.velcdn.com/images/calzone0404/post/4e567e6f-a000-4d0c-9f6a-5963d465b2c3/image.png)

(a) CreditCard의 make_payment 메소드는 각각 생성된 인스턴스에 대해 별도로 생성되는것이 아니라, 미리 CreditCard의 namespace에 생성된다. 마찬가지로, __init__, get_customer, get_bank 등의 메서드도 마찬가지로 Class namespace에 생성된다.

(b) : 상속받은 PredatoryCreditCard Class는, CreditCard와 별도로 구별된 namespace가 존재한다. 따라서 해당 namespace에는 subclass에서 재정의한 __init__, charge, process_month가 저장된다.

(c) : 인스턴스 생성 시, 해당 인스턴스의 namespace에 저장되는 멤버들에 대한 정보가 저장된다.

<hr>

### How Entries Are Established in a Namespace

네임스페이스에서 각 항목이 어떻게 설정되는지 이해하는 것은 객체 지향 프로그래밍 개념, 특히 인스턴스 네임스페이스와 클래스 네임스페이스의 차이점과 사용 방법을 파악하는 데 매우 중요하다.

> **1. instance namespace**

**Instance namespace**는 특정 객체의 고유 데이터를 저장하는 데 사용된다.

예를 들어, CreditCard 클래스의 인스턴스가 생성될 때 __init__ 메소드 내에서 self.balance = 0과 같은 구문을 사용하여 balance라는 항목이 인스턴스 네임스페이스에 설정된다.

여기서 self는 새로 생성된 인스턴스를 가리키는 식별자이다 (Java, Cpp의 this 키워드). 이러한 방식으로 변수에 **self**를 사용함으로써, balance 식별자는 직접적으로 인스턴스 네임스페이스에 추가된다.

> **2. Class namespace**

**Class namespace**는 클래스 정의 내부에서 직접 선언된 모든 선언이 포함된다.

예를 들어, CreditCard 클래스 정의는 
```python
def make_payment(self, amount): 
	...
```
같은 메소드를 포함하는데, 이 메소드는 CreditCard 클래스의 범위 내에서 선언되기 때문에 make_payment라는 이름으로 CreditCard 클래스 네임스페이스에 저장된다.

클래스 네임스페이스에는 **일반적으로** 멤버 함수가 선언되지만, 다른 유형의 데이터 값이나 심지어 다른 클래스도 클래스 네임스페이스 내에서 선언될 수 있다.

> **3. 상속과 네임스페이스**

상속을 사용할 때도 각 객체에는 하나의 인스턴스 네임스페이스만 존재한다.

예를 들어, PredatoryCreditCard 클래스의 인스턴스가 생성될 때, apr 속성과 balance, limit과 같은 속성들은 모두 self.apr와 같은 구문을 사용하여 할당되기 때문에 해당 인스턴스의 네임스페이스에 위치한다.

<hr>

# 2.6 Shallow and Deep Copying

```python
import copy

# 원본 객체
original = [1, 2, [3, 4]]

# 얕은 복사 (값 변경시 원본도 수정됨)
shallow_copied = copy.copy(original)

# 깊은 복사 (값 변경시 원본은 수정 안됨)
deep_copied = copy.deepcopy(original)

# 내부 리스트 수정
original[2].append(5)

print(original)         # [1, 2, [3, 4, 5]]
print(shallow_copied)   # [1, 2, [3, 4, 5]] - 얕은 복사본도 변경됨
print(deep_copied)      # [1, 2, [3, 4]] - 깊은 복사본은 변경되지 않음
```
![](https://velog.velcdn.com/images/calzone0404/post/2807b059-a891-455e-bf58-a33ce86f415b/image.png)

<hr>

**1. Shallow Copy (Alias)**

얕은복사 = 객체의 별칭을 만드는 것.

하나의 동일한 데이터 container의 주소를 여러가지 변수에 할당해서 여러가지 이름으로 접근이 가능하게 된다. 따라서, warmtones를 통해 내부 데이터를 변경하게되면, palette로 접근 시, 변경 사항이 그대로 보이게 된다.

> **파이썬에서 palette = list(warmtones)를 하게 되면, 얕은 복사가 된다. (같은 리터럴을 가리키게됨)**

<hr>

![](https://velog.velcdn.com/images/calzone0404/post/6254c237-f6a6-4e49-8f56-6c09e82db1c6/image.png)

2. Deep Copy (Copy)

깊은복사 = 해당 내용에 대한 메모리를 새로 할당하여 그 공간에 복사본을 새로 만드는 것.

어떤 데이터 container를 deep copy하게되면, 새로운 메모리에 그 내용이 복사된다. 따라서 warmtones에서 수정한 내용이 palette에 적용되지 않고, 마찬가지로 palette에서 수정한 내용이 warmtones의 내용에 적용되지 않는다.

> 파이썬에서는 copy.deepcopy()를 통해 구현할 수 있다.
