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

이 클래스의 공개 인터페이스는 대부분 파이썬 리스트와 유사하지만, array 클래스의 생성자는 첫 번째 매개변수로 데이터 유형을 지정하는 타입 코드가 필요하다. 

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

### Amortization Analysis을 사용한 효율성 증명
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

Python의 `list` Class가 어떻게 동적 배열을 사용하여, 요소들을 관리하는지 알아볼 수 있다.

```python
from time import time

def compute_average(n):
    """빈 리스트에 n개의 요소를 append하는 데 걸리는 평균 시간을 반환"""
    data = []
    start = time()  # 시작 시간 기록
    for k in range(n):
        data.append(None)
    end = time()  # 종료 시간 기록
    return (end - start) / n  # 연산당 평균 시간 계산
```

이 코드를 n = $10^2$, $10^3$, ..씩 늘려가면서 돌려본 결과를 표로 나타내보았다.
![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/58b64368-ee03-4bbc-a0af-865468ba7736)

이 표를 보면 n회의 append가 상수시간이 걸린다는것을 확인할 수 있다. (실제로 코드를 실행해봐도 비슷하게 나온다.)

<hr>

# 5.4 Efficiency of Python’s Sequence Types

## 5.4.1 Python’s List and Tuple Classes

### 비변형 동작

> 리스트와 튜플 모두 비변형(nonmutating) 연산을 지원하며, 이 연산들은 배열(sequence)를 수정하지 않는다.

| 연산                | 실행 시간     |
|--------------------|--------------|
| `len(data)`        | O(1)         |
| `data[j]`          | O(1)         |
| `data.count(value)`| O(n)         |
| `data.index(value)`| O(k + 1)     |
| `value in data`    | O(k + 1)     |
| `data1 == data2`   | O(k + 1)     |
| `data[j:k]`        | O(k − j + 1) |
| `data1 + data2`    | O(n1 + n2)   |
| `c * data`         | O(cn)        |

여기서 `n`은 시퀀스의 길이, `k`는 시퀀스 내의 위치, `n1`, `n2`는 두 시퀀스의 길이라고 하자.

### 상수 시간 연산

- `len(data)`: 이 연산은 시퀀스의 요소 수를 반환하는 함수이다.
- `data[j]`: 인덱스를 사용해서 요소에 접근하는 방법이다.

### 값의 발생 위치 검색

`count`, `index`, `in`과 같은 연산은 **왼쪽에서 오른쪽으로 시퀀스를 순회**하는 방식으로 동작한다.
- `count`: 항상 시퀸스의 전체를 순회하므로 O(n) 시간이 소요된다.
- `index`, `in`: 이 둘의 경우에는, 값이 발견되면 일찍 종료할 수 있어 최악의 경우 O(n) 시간이 걸리지만, 더 빠르게 끝날수도 있다.

#### 사전식 비교

두 시퀀스를 비교(`data1 == data2`, `data1 > data2`와 같은 연산)하는 것은 각 시퀀스의 요소를 처음부터 짝지어서 비교한다. 만약 일치하지 않는 요소가 일찍 발견되면 최적화될 수 있지만, 최악의 경우 O(k + 1) 시간이 소요된다.

#### 새 인스턴스 생성

슬라이싱(`data[j:k]`), 연결(`data1 + data2`), 반복(`c * data`)과 같은 연산은 새 시퀀스를 생성하기 때문에, 새로 생성된 시퀀스의 크기에 비례해서 선형 시간이 걸린다.

#### 리스트 수정 동작


| 연산                  | 실행 시간        |
|----------------------|-----------------|
| `data[j] = val`      | O(1)            |
| `data.append(value)` | O(1)*           |
| `data.insert(k, value)` | O(n − k + 1)* |
| `data.pop()`         | O(1)*           |
| `data.pop(k)`        | O(n − k)*       |
| `del data[k]`        | O(n − k)        |
| `data.remove(value)` | O(n)*           |
| `data1.extend(data2)`| O(n2)*          |
| `data.reverse()`     | O(n)            |
| `data.sort()`        | O(n log n)      |

 `∗`: amortized (평균 시간)

### 5.4.2 Python’s String Class

문자열은 Python에서 불변의 문자 시퀸스이다. 
리스트와 튜플처럼 많은 연산을 지원하지만, 불변성특징 때문에 여러가지 제약이 존재한다.

- `capitalize`, `center`, `strip`과 우리가 주로 사용하는 문자열 연산 함수는 새 문자열을 생성해야 하기 때문에, 선형 시간 복잡도 O(n)가 소요된다.
- 불리언 메서드(`islower` 등)와 비교 연산자(`==`, `<`)도 일반적으로 O(n) 시간에 실행되지만, 일찍 결정될 수 있다면 더 빠르게 종료할 수 있다.

#### 패턴 매칭

문자열 내에서 substring 또는 패턴을 찾는 것은 문자열(`n`)과 패턴(`m`)의 길이에 따라 달라지게 된다.
- 단순한 패턴 매칭 구현은 $O(mn)$ 시간에 실행된다.
- 하지만, 추후 13장에서 배우는 알고리즘을 통해 특정한 문자열 매칭의 경우는 $O(n)$ 시간으로 줄여버릴 수 있다.

#### 문자열 구성

단순히 반복문을 통해 (`letters += c`)을 사용하는 것은 매우 비효율적이며 O(n^2) 시간 복잡도를 가지게 된다. 하지만, 리스트 컴프리헨션 또는 `join`을 사용하여 효율적으로 구성할 수 있다.
```python
temp = []
for character in document:
    if character.isalpha():
        temp.append(character)
letters = ''.join(temp)
```
이 방법을 사용해서 선형 시간 복잡도 O(n)를 보장할 수 있다.

---

# 5.5 UsingArray-BasedSequences

## 5.5.1  StoringHighScoresforaGame

이 코드는 비디오 게임의 최고 점수들을 저장하는 방법에 대한 예제이며, 게임뿐만이 아니라 병원의 환자 기록이나 축구 팀의 선수 이름을 저장하는 것과 같은 부분에서 많이 활용되고 있다. 

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/f5be0f83-507d-4984-9774-5eee3d34c362)

### GameEntry Class
```python
class GameEntry:
    """
    플레이어 이름, 점수를 저장하는 객체
    """    
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def get_name(self):
        return self.name
    
    def get_score(self):
        return self.score

    # print(GameEntry인스턴스)로 아래 형식을 통해 출력된다. (Java의 toString)
    def __str__(self):
        return '({0}, {1})'.format(self.name, self.score)
```

### Scoreboard Class

Scoreboard 클래스는 일정 수의 최고 점수를 저장하는 객체이다. 
새로운 점수가 추가될 때마다 가장 낮은 점수보다(배열의 맨 뒤에 있는 요소보다) 높으면 보드에 추가되는 형태이다.

```python
class Scoreboard:
    """
    스코어보드 객체
    """

    # 만약 초기화할때, Scoreboard()로만 초기화했다면,
    # 10개의 요소를 가리키는 포인터로 이루어진 _board가 생성됨
    def __init__(self, capacity=10):
        self._board = [None] * capacity  # reserve space for future scores
        self._n = 0                      # number of actual entries

    # Scoreboard인스턴스[k]를 통해 가져올 수 있음
    def __getitem__(self, k):
        """Return entry at index k."""
        return self._board[k]
    
    def add(self, entry):
        # GameEntry인스턴스에서 점수를 가져옴
        score = entry.get_score()
        
        # 보드의 용량을 초과하지 않거나 // 보드의 맨 뒤에 있는 점수보다 지금 입력된 점수가 더 높다면 TRUE
        good = self._n < len(self._board) or score > self._board[-1].get_score()
        
        if good:
            # 아직 보드가 다 안채워졌다면 그냥 넣는다.
            if self._n < len(self._board):  # no score drops from list
                self._n += 1                # so overall number increases

            # 보드가 이미 꽉 차있다면, 맨 뒤 요소 부터 비교해서 올바른 자리에 넣는 동작
            j = self._n - 1
            while j > 0 and self._board[j-1].get_score() < score:
                self._board[j] = self._board[j-1]
                j -= 1
            self._board[j] = entry
```

### 새롭게 들어온 entry를 삽입하는 방법

위 코드의 맨 하단부분에 대해 설명을 해보자면,

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/31d776ee-2e3f-46e0-b795-fbcb2e3a0fdd)

`Jill/740`이 새롭게 들어왔을 때, `Jack/510`부터 `Mike/1105`까지 비교하면서 적절한 자리를 찾아가는 방식이다.
740점보다 작은 엔트리들은 배열의 오른쪽으로 쭉 밀리고, 밀리고 남은 한자리에 entry가 들어가게 된다.

## 5.5.2 시퀀스 정렬

### 삽입 정렬 알고리즘

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/5cb78a62-7117-4c0e-bedb-977b9329d9b4)

삽입 정렬은 배열의 각 요소를 순차적으로 선택하고 해당 요소를 적절한 위치에 `삽입`하여 배열을 정렬하는 알고리즘이다.
인덱스 `j=1`인 요소부터 시작하며, `arr[j-1]`과 `arr[j]`를 비교하게 되는데, 
만약 오름차순의 경우 `arr[j-1]`이 더 크다면, `arr[j]`와 교환하는 방식으로 진행되며,
총 array의 크기(`k`)만큼 반복해야 한다. 따라서 시간복잡도는 $O(n^2)$이다.

```python
def insertion_sort(A):
    """Sort list of comparable elements into nondecreasing order."""
    for k in range(1, len(A)):       # from 1 to n-1
        cur = A[k]                   # current element to be inserted
        j = k                        # find correct index j for cur
        while j > 0 and A[j-1] > cur: # element A[j-1] must be after cur
            A[j] = A[j-1]
            j -= 1
        A[j] = cur                   # cur is now in the right place
```

## 5.5.3 간단한 암호화
문자열과 리스트를 사용한 간단한 암호화 기법인 Caesar cipher 알고리즘이다.
Caesar cipher는 각 문자를 알파벳에서 일정 수만큼 뒤에 있는 문자로 대체한다.

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/bcb2c6db-1fcc-4017-822e-5b670eebf061)

### Caesar 암호화
```python
class CaesarCipher:
    """Class for doing encryption and decryption using a Caesar cipher."""
    
    def __init__(self, shift):
        """Construct Caesar cipher using given integer shift for rotation."""
        # shift만큼 forward하여 암호화했다면 복호화할때는 shift만큼 backward를 하면 된다.
        self._forward = ''.join([chr((k + shift) % 26 + ord('A')) for k in range(26)])
        self._backward = ''.join([chr((k - shift) % 26 + ord('A')) for k in range(26)])
    
    def encrypt(self, message):
        """Return string representing encrypted message."""
        return self._transform(message, self._forward)
    
    def decrypt(self, secret):
        """Return decrypted message given encrypted secret."""
        return self._transform(secret, self._backward)
    
    def _transform(self, original, code):
        """Utility to perform transformation based on given code string."""
        # 원문을 list에 담아서
        msg = list(original)
        # 문자 하나하나마다 변환을 시작한다.
        for k in range(len(msg)):
            if msg[k].isupper(): # code(forward or backward)를 통해 암호화 또는 복호화
                j = ord(msg[k]) - ord('A')
                msg[ch] = code[j] # 암호화된 문자로 대체
        return ''.join(msg)
```

### Example usage
```python
cipher = CaesarCipher(3) # 3회 shift
message = "THE EAGLE IS IN PLAY; MEET AT JOE'S."
coded = cipher.encrypt(message)
print('Secret:', coded)
decoded = cipher.decrypt(coded)
print('Message:', decoded)
```

# 5.6 Multidimensional Data Sets

Python에서 리스트, 튜플, 문자열은 모두 1차원 배열(시퀸스)을 이용한다. 
이러한 시퀀스의 각 요소에 접근하려면 단일 인덱스를 통해서 접근이 가능하다. 
하지만, 2차원 이상의 리스트의 경우에는 단일 엔덱스만으로는 개별 요소에 접근하기 힘들다.

### 2차원 배열의 예

2차원 배열은 `행렬(matrix)`이라고 불린다. 
행렬의 cell을 참조하려면 일반적으로 두 개의 인덱스(i, j)를 사용해야한다. 

> 첫 번째 인덱스는 **행 번호**를, 두 번째 인덱스는 **열 번호**를 나타낸다. 

![image](https://github.com/Scanf-s/CS_Book_Summary/assets/105439069/d5fd3b0e-3912-4ba6-ac04-506f3d322e8b)

이 데이터 세트가 `stores`라는 이름이라면, `stores[3][5]`의 값은 100이고, `stores[6][2]`의 값은 632라고 읽으면 된다.

### 다차원 리스트 초기화 방법

1차원 리스트를 빠르게 초기화하려면 `data = [0] * n`과 같은 구문을 사용할 수 있다. 
하지만 2차원 리스트를 만들 때는 생각을 좀 해봐야한다.

예를 들어, `r` 행과 `c` 열을 가진 2차원 리스트를 만들고 모든 값을 0으로 초기화하려고 할 때 아래처럼 하면 오류가 발생한다.

```python
data = ([0] * c) * r
```
이 명령은 길이가 `r * c`인 단일 리스트를 생성하는 코드이다.

따라서, 각 열이 별도의 리스트가 되도록 하려면 아래와 같이 작성해야 한다.
```python
data = [[0] * c for _ in range(r)]
```
이 방법은 리스트 comprehension을 사용해서 각 행이 별도의 리스트로 생성되도록 한다.
이렇게 하면 각 loop마다 `[0] * c`만큼의 1차원배열이 `r`개 생성되는 2차원 배열로 생성된다.

### 예시 : 틱택토 (Tic-Tac-Toe)
```python
class TicTacToe:
    """틱택토 게임을 관리하는 클래스 (전략은 포함되지 않음)."""
    
    def __init__(self):
        """새 게임 시작."""
        self.board = [['']*3 for _ in range(3)]
        self.player = 'X'
    
    def mark(self, i, j):
        """(i,j) 위치에 현재 플레이어의 표시를 놓음."""
        if not (0 <= i <= 2 and 0 <= j <= 2):
            raise ValueError("유효하지 않은 보드 위치")
        if self.board[i][j] != '':
            raise ValueError("보드 위치가 이미 점유됨")
        if self.winner() is not None:
            raise ValueError("게임이 이미 완료됨")
        self.board[i][j] = self.player
        self.player = 'O' if self.player == 'X' else 'X'
    
    def is_win(self, mark):
        """주어진 플레이어의 승리 여부를 확인."""
        board = self.board
        return (mark == board[0][0] == board[0][1] == board[0][2] or
                mark == board[1][0] == board[1][1] == board[1][2] or
                mark == board[2][0] == board[2][1] == board[2][2] or
                mark == board[0][0] == board[1][0] == board[2][0] or
                mark == board[0][1] == board[1][1] == board[2][1] or
                mark == board[0][2] == board[1][2] == board[2][2] or
                mark == board[0][0] == board[1][1] == board[2][2] or
                mark == board[0][2] == board[1][1] == board[2][0])
    
    def winner(self):
        """승리한 플레이어의 표시를 반환, 무승부는 None을 반환."""
        for mark in 'XO':
            if self.is_win(mark):
                return mark
        return None
    
    def __str__(self):
        """현재 게임 보드의 문자열 표현을 반환."""
        rows = ['|'.join(self.board[r]) for r in range(3)]
        return '\n-----\n'.join(rows)

# 클래스 테스트
game = TicTacToe()
game.mark(0, 0)
game.mark(1, 1)
game.mark(0, 1)
game.mark(2, 2)
print(game)
```

### 추가) C에서의 2차원 배열 및 메모리에 대해서

C에서 `int`형 4x4 2차원 배열을 선언하는 경우에 대해 생각해보자.
`int`형 데이터 타입은 일반적으로 4바이트를 사용하므로, 4x4 배열은 총 16개의 `int` cell이 존재한다.

#### 메모리 크기 계산

1. **각 `int`형 요소의 크기**: 4bytes
2. **전체 요소의 수**: 4 * 4 = 16개
> **$Total memory size = 16 * 4bytes = 64bytes$**

#### 예제 코드

```c
#include <stdio.h>

int main() {
    int array[4][4];  // 4x4 int형 2차원 배열 선언
    
    // 배열 초기화 및 출력
    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            array[i][j] = i * 4 + j;  // 간단히 값을 할당
            printf("%d ", array[i][j]);
        }
        printf("\n");
    }
    
    return 0;
}
```
이렇게 생성했을 때, 각각의 cell들은 4바이트를 사용하므로, 총 64바이트의 메모리가 할당된다.

#### 동적 메모리 할당

만약 동적으로 4x4 배열을 할당하려면 아래와 같이 작성해야 한다.

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int rows = 4;
    int cols = 4;

    // 행 포인터를 위한 메모리 할당
    int **array = (int **)malloc(rows * sizeof(int *));
    
    // 각 행에 대한 메모리 할당
    for (int i = 0; i < rows; i++) {
        array[i] = (int *)malloc(cols * sizeof(int));
    }

    // 배열 초기화 및 출력
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            array[i][j] = i * cols + j;  // 간단히 값을 할당
            printf("%d ", array[i][j]);
        }
        printf("\n");
    }

    // 메모리 해제
    for (int i = 0; i < rows; i++) {
        free(array[i]);
    }
    free(array);

    return 0;
}
```

이 코드는 동적으로 4x4 배열을 할당하고, 초기화 후 출력한 다음, 할당된 메모리를 해제한다. 
동적 할당을 사용할 때는 각 행에 대해 **메모리를 개별적으로 할당**하므로 총 64바이트의 메모리가 필요하다.

- 각 행 포인터의 메모리: $4 *$ sizeof(int *) bytes
- 각 행의 메모리: $4 * (4 *$ sizeof(int) $)$ bytes

이 경우, 포인터 배열 자체는 4개의 포인터를 포함하며, 각 **포인터는 일반적으로 8바이트**를 사용한다 (**64비트 시스템의 경우**).
따라서
- 포인터 배열의 총 크기는 $4 * 8 = 32$bytes이며, 
- 각 행은 16개의 `int` 요소를 포함하여 총 $64$bytes를 필요로 한다.
- **따라서 동적 할당의 총 메모리 사용량은 $32 + 64 = 96$bytes**
