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

이 섹션에서는 동적 배열과 상환 방법에 대해 설명한다.
동적 배열에 대해 알아보고, 동적 배열을 사용하는 Python의 `list`가 어떻게 element들을 효율적으로 추가하고 관리하는지 알아보도록 하자.

### Dynamic array
> 동적 배열은, 배열의 크기가 `고정`되어 있지만, 현재 할당된 크기 이상의 요소를 추가할 수 있도록 배열의 크기를 동적으로 조정하는 배열이다.

만약, 배열의 용량이 가득찬다면 다음 과정을 통해 배열을 늘리게 된다.
1. 더 큰 용량을 가진 배열을 생성한다
2. 기존 배열의 요소들을 모두 새로 생성한 배열에 복사한다
3. 기존 배열을 가리키는 포인터가 새로운 배열을 가리키게 된다.
4. 해당 포인터를 반환하게 되면 사용자 입장에서는 배열의 크기가 늘어난것처럼 보이게 된다.

```python
import sys  # getsizeof 함수를 제공

data = []
for k in range(27):  # n : 넣을 요소의 개수 27
    a = len(data)  # 요소의 현재 개수
    b = sys.getsizeof(data)  # 실제 크기(바이트 단위)
    print("Length: {0:3d}; Size in bytes: {1:4d}".format(a, b))
    data.append(None)  # 길이를 하나씩 증가
```

위 코드를 실행하면 다음과 같이 출력된다.
```yaml
Length:  0; Size in bytes:   72
Length:  1; Size in bytes:  104
Length:  2; Size in bytes:  104
Length:  3; Size in bytes:  104
Length:  4; Size in bytes:  104
Length:  5; Size in bytes:  136
Length:  6; Size in bytes:  136
Length:  7; Size in bytes:  136
Length:  8; Size in bytes:  136
Length:  9; Size in bytes:  200
Length: 10; Size in bytes:  200
Length: 11; Size in bytes:  200
Length: 12; Size in bytes:  200
Length: 13; Size in bytes:  200
Length: 14; Size in bytes:  200
Length: 15; Size in bytes:  200
Length: 16; Size in bytes:  200
Length: 17; Size in bytes:  272
Length: 18; Size in bytes:  272
Length: 19; Size in bytes:  272
Length: 20; Size in bytes:  272
Length: 21; Size in bytes:  272
Length: 22; Size in bytes:  272
Length: 23; Size in bytes:  272
Length: 24; Size in bytes:  272
Length: 25; Size in bytes:  272
Length: 26; Size in bytes:  352
```

위 실행 결과를 천천히 살펴보면,
- 빈 리스트는 특정 메모리 사이즈 (72 bytes)를 할당받는다.
- 첫 번째 요소가 추가되면, 메모리 할당량이 104 bytes로 증가한다.
- 다섯 번째 요소가 추가되면, 메모리 할당량이 136 bytes로 증가한다.
- 점점 추가되는 양이 많아질수록 할당하는 메모리의 양이 거의 2배로 증가하게 된다.


## 5.3.1 Implementing a Dynamic Array

이번에는, Python의 list가 동적 배열로 어떻게 구현되어 있는지 이야기해보자.
동적 배열의 핵심 요소는, 기존 배열을 효율적으로 확장할 수 있는 방법을 제공하는 것이다.
배열의 용량은 한정되어있기 때문에 실제로 배열을 확장하는것은 아니지만, 
위에 기술한 대로 새로운 배열을 생성해서 실제로 배열이 확장된것처럼 보이게 하면 된다.

```python
import ctypes  # 저수준 배열을 제공

class DynamicArray:
    """Python 리스트를 단순화한 동적 배열 클래스"""
    
    def __init__(self):
        """빈 배열 생성"""
        self._n = 0  # 배열에 저장된 요소 개수
        self._capacity = 1  # 현재 배열의 총 용량
        self._A = self._make_array(self._capacity)  # 저수준 배열 (a[1]; 로 초기화)

    def __len__(self):
        """배열에 저장된 요소 수 반환"""
        return self._n

    def __getitem__(self, k):
        """인덱스 k의 요소 반환"""
        if not 0 <= k < self._n:  0 ~ n-1 까지에 대해서만
            raise IndexError('invalid index')
        return self._A[k]  # 배열에서 가져오기

    def append(self, obj):
        """배열 끝에 객체 추가"""
        if self._n == self._capacity:  # 공간 부족 상태에서 추가하려는 경우
            self._resize(2 * self._capacity)  # 용량 두 배로 증가
        self._A[self._n] = obj
        self._n += 1

    def _resize(self, c):  # 비공개 유틸리티
        """내부 배열을 용량 c로 재조정"""
        B = self._make_array(c)  # 새 (더 큰) 배열
        for k in range(self._n):  # 기존 _A에 저장되어 있던 값 복사
            B[k] = self._A[k] # 사실 값을 복사하는건 아니고, B가 A가 가리키고 있던 리터럴을 가리키게 됨
        self._A = B  # 더 큰 배열 사용하도록 포인터 재구성
        self._capacity = c

    def _make_array(self, c):  # 비공개 유틸리티
        """용량 c를 가진 새 배열 반환"""
        return (c * ctypes.py_object)()  # ctypes 문서 참조

# 사용 예시:
arr = DynamicArray()
for i in range(10):
    arr.append(i)
    print(f'Length: {len(arr)}; Capacity: {arr._capacity}')
```

## 5.3.2 Amortized Analysis of Dynamic Arrays
이 섹션에서는 동적 배열의 연산 시간에 대한 자세한 분석을 이야기하고 있다.
알고리즘이나 특정 단계의 실행 시간에 대해 점근적 하한을 제공하기 위해 **빅오메가$Ω$** 표기법을 사용한다.

### 동적 배열의 시간 복잡도
배열이 가득 찰 때마다 새로운, 더 큰 배열로 교체하는 전략은 처음에는 느려보일 수 있다. 
단일 append 연산은 현재 배열의 요소 수 𝑛에 대해 Ω(𝑛) 시간이 필요한것처럼 보일 수 있다.

하지만 배열의 용량을 두 배로 늘리는 경우, 두 배로 늘린 새로운 배열은 배열 교체 이전에 최대 𝑛개의 새로운 요소를 추가할 수 있다. 
따라서, 많은 단순한 `.append` 연산이 하나의 새로운 배열을 만드는비싼 연산과 일치하게 된다. 
이를 통해 초기 비어있는 동적 배열에 대해 일련의 연산을 수행하는 것이 전체 실행 시간 측면에서 효율적이라고 할 수 있다.

# Amortization Analysis을 사용한 효율성 증명
상환 분석을 통해 일련의 append 연산을 수행하는 것이 실제로 매우 효율적임을 증명할 수 있다.
상환 분석을 수행하기 위해 컴퓨터를 일정량의 계산 시간을 위해 하나의 사이버 달러**(즉, 시간당 하나의 사이버 달러)**를 요구하는 동전 투입 기계로 생각해보자.
그리고 연산이 실행될 때, 우리는 현재 "은행 계좌"에 해당 연산의 실행 시간을 지불할 수 있을 만큼 충분한 사이버 달러가 있다고 가정하자.

따라서, 계산에 소요된 총 사이버 달러 양은 계산에 소요된 총 시간에 비례하게된다.

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/5015f12e-2df0-45f6-9018-b438729091bb)

위 그림은 동적 배열에서 일련의 append 연산의 실행 시간을 보이는 막대 그래프이다. 

> 정리 5.1: S를 초기 용량이 1인 동적 배열 구현한 sequence라고 가정하고,
> 배열이 가득 차있을 시 배열의 크기를 두 배로 늘리는 전략을 사용한다고 해보자.
> 
> 이때, S가 비어 있는 상태에서 시작하여 S에서 일련의 n개의 추가 작업을 수행하는 총 시간은 $O(n)$이다.

> 정리 5.2: 각각의 크기가 조정될 때마다 고정된 배열 증가량을 사용하여
> 초기에 빈 동적 배열에 대해 n회의 append를 수행하는 데는 $Ω(n^2)$ 시간이 걸린다.

### Memory Usage and Shrinking an Array

동적 배열의 용량이 가득 차지 않으면 메모리를 비효율적으로 사용하게 된다.
일정 수 이하의 요소를 가지는 경우 배열 용량을 축소하는 전략을 사용하여 메모리 사용을 최적화할 수 있다.

예를 들어, 요소 수가 용량의 1/4 이하로 감소하면 배열 용량을 절반으로 줄이는 전략을 사용할 수 있다.

## 5.3.3 Python's List Class
