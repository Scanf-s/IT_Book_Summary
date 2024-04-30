# 5.1 Python's Sequence Types

파이썬은 **``list``, ``tuple``, ``str`` class**를 통해 sequence type을 지원하고 있다.
그리고 각 sequence type은 **seq[k]**와 같은 형식을 통해 개별적인 element에 접근이 가능하다.

# 5.2 Low-Level Arrays
파이썬이 표현하는 sequence type을 정확하게 알기 위해서는 먼저 Low-Level 컴퓨터 구조를 알아야 한다.

> 컴퓨터는 메모리에 정보를 bit의 집합 (주로 8bit = 1byte 단위로)으로 저장한다.

컴퓨터는 메모리에 저장한 정보를 **Memory address**를 이용하여 접근할 수 있다.
![](https://velog.velcdn.com/images/calzone0404/post/bceb8dbb-2b3a-4e77-997a-ac7aa63434b1/image.png)

이론적으로, 메모리 주소를 이용하여 모든 메인 메모리(**RAM**)에 접근이 가능하다. 이를 통해, 각 데이터를 메모리에 접근하거나, 저장할 때 메모리 주소로 바로 접근이 가능하므로 $O(1)$시간이 소요된다.

따라서 우리가 구현하는 프로그램도 어떤 변수를 선언하고 할당할 때, 메모리를 이용하게된다. 
어떠한 일련의 변수들의 집합(Array같은)들이 메모리에 저장될때에는, **``컴퓨터의 메모리에 연속적인 공간을 할당해서 저장``**하게 되는데, 이는 메모리의 지역성을 고려하여 컴퓨터 하드웨어가 효율적으로 데이터를 가져올 수 있도록 위해서이다.

예를 들면, Python에서 text string이 저장되는 방식을 살펴보자.
![](https://velog.velcdn.com/images/calzone0404/post/e7d9890a-7036-4770-9471-131bbe4d463f/image.png)
파이썬에서는 거의 대부분의 컴퓨터 시스템에서 사용되는것처럼 문자 하나를``Unicode Character``형식의 16bits(2 bytes) 데이터 형태로 연속적인 메모리를 이용하여 저장한다.
위 사진이 바로 이 설명에 해당하는데,
- 2146 ~ 2147 : S가 저장된다.
- 2148 ~ 2149 : A가 저장된다.
...
- 2156 ~ 2157 : E가 저장된다.

<hr>

Array의 각각의 위치를 **cell**이라고 표현한다. 우리는 이 Cell을 ``정수형 INDEX``를 이용하여 특정한 Cell의 위치를 찾아갈 수 있다. 

하나의 Array에 배정된 각 cell들은 모두 동일한 크기를 가져야만 하며, 각 cell들은 다음 공식을 통해 접근이 가능하다.

$start + cellsize * index$

즉, 시작주소 + Cell에 배정된 크기 X index
이때 index는 사진에 나와있는 0 ~ 5까지의 숫자이다.
예를 들어서, 만약 M이 들어있는 Cell에 접근하고 싶다면, $2146 + (2bytes * 2) = 2146 + 4 = 2150$이므로 
2150번지에 접근하게되면, M을 얻을 수 있다.

## 5.2.1 Referenctial Arrays

앞서 말했다 싶이, 배열의 각 Cell들은 동일한 크기의 byte를 할당받는다. 
하지만, 위 방식을 따른다면 String과 같은 경우 각 Cell들은 서로 다른 크기의 byte가 할당되어야만하는 문제가 존재한다.

따라서 Python은 string 자체를 Cell에 저장하는게 아니라(Java도 마찬가지로) 해당 string을 표현하는 **Literal**을 생성해서, 해당 Literal을 가리키는 포인터를 배열에 저장한다.

![](https://velog.velcdn.com/images/calzone0404/post/4aa0ddc3-62fa-4452-9ed0-e8a076fe20a2/image.png)

각 element의 size는 서로 다르지만, 동일한 포인터를 사용하여 literal을 가리키고 있기 때문에 Array의 각 cell들은 Pointer 크기로 할당된다.
위와 같이 할당하면, None(NULL) Object도 포인터를 사용하여 가리킬 수 있게 된다.

<hr>

또 다른 예시로, 배열을 Slicing하여 다른 배열에 넣는경우를 생각해보자
![](https://velog.velcdn.com/images/calzone0404/post/815a96d3-58e6-41be-b52d-77de07812e2e/image.png)

```python
temp = primes[3:6]
```
위 코드같이 primes 배열을 Slicing하는 경우, temp는 새로운 7, 11, 13값을 생성하는것이 아니라,
기존 primes에서 가리키고 있는 (기존에 생성된 Literal) ``Integer Literal 7, 11, 13``을 가리키게 된다.
이 예시처럼 ``Immutable data type``을 복사하는 경우를 **Shallow copy**라고 한다.

반대로, ``Mutable data type``이 들어있는 list를 복사하는 경우는 **deep copy**라고 한다. 즉, 새로운 list와 새로운 값이 생성된다.

<hr>

또 다른 예시로, size = 8인 배열이 모두 0값을 가지도록 초기화하는 경우를 떠올려보자
```python
temp = [0] * 8
```
위 코드의 결과는 다음과 같다.
![](https://velog.velcdn.com/images/calzone0404/post/df2ac5ac-fa80-4272-925e-f32b67bf0405/image.png)
즉, 0을 개별적으로 생성하는 것이 아니라, Integer literal 0을 생성하여 모두 이곳을 가리키게된다.

<hr>

마지막으로, 배열을 extend하는 경우를 살펴보자
```python
primes.extend(extras)
```
![](https://velog.velcdn.com/images/calzone0404/post/bdb20ebc-c4c5-468d-acc8-f085bc37e7f1/image.png)

기존 extra가 23, 29, 31을 가리키고 있을 때, extra를 primes배열 뒤에 붙인다면, primes는 새로운 cell을 생성하여 기존 extra가 가리키고 있던 Integer literal을 가리킨다.

<hr>

## 5.2.2 Compact Arrays in Python

**Compact Array**는 **참조 구조**보다 컴퓨팅 성능 면에서 여러 가지 이점을 가진다. 

> 콤팩트 구조의 전체 메모리 사용량이 참조 구조에서 사용하는 메모리 사용량보다 훨씬 낮게 사용한다

이는 기본 데이터 외에 메모리 참조의 순서를 명시적으로 저장하는 데 필요한 오버헤드(해당 요소를 가리켜주는)가 없기 때문이다.

**참조 구조**는 배열에 저장된 메모리 주소에 64비트를 사용하고, 그 위에 요소로 간주되는 객체를 가리키는 데 사용되는 비트 수가 또 필요하다.
 
> 콤팩트 구조는 기본 데이터가 메모리에 연속적으로 저장된다. 

참조 구조의 경우는 그렇지 않으며, 리스트는 메모리 주소의 순서는 관리하지만 그 요소들이 실제로 메모리의 어디에 위치하는지는 정하지 않는다.

컴퓨터의 캐시 및 메모리 계층 구조의 작동 방식 때문에, 같은 계산에서 사용될 다른 데이터 근처에 데이터를 저장하는 것이 유리하다.

파이썬은 기본 데이터 유형의 배열을 위해 array 모듈을 제공하고 있으며, 이 모듈은 콤팩트한 저장을 위한 array 클래스를 지원하고있다.

이 클래스의 공개 인터페이스는 대부분 파이썬 리스트와 유사합니다. 그러나 array 클래스의 생성자는 첫 번째 매개변수로 데이터 유형을 지정하는 타입 코드가 필요하다. 

```python
prime = array('i', [2, 3, 5, 7, 11, 13, 17, 19])
```
![](https://velog.velcdn.com/images/calzone0404/post/335d4893-425b-40c2-9649-20d1190e1091/image.png)

- 타입 코드 'i'는 부호 있는 정수의 배열을 지정하고, 각 정수는 **16비트(int형 이므로)**를 사용하여 표현된다. 
- 타입 코드는 인터프리터가 배열의 각 요소에 필요한 비트 수를 정확히 결정할 수 있도록 도와주기 위해 사용한다.

사용자 정의 데이터 유형의 콤팩트 배열을 만드는 기능은 array 모듈에서 제공하지 않지만, ctypes 모듈의 하위 수준 지원을 통해 이러한 구조의 콤팩트 배열을 생성할 수 있다.

<hr>

# 5.3 Dynamic Arrays and Amortization
