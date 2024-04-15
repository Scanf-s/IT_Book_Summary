재귀란, 함수가 자기 자신을 실행중에 한번 또는 그 이상 호출하는 것이다. 예시로, 프랙탈, 러시아 마트료시카 인형이 있다.

컴퓨터에서 재귀는 반복되는 작업을 수행할 때, 우아하고 강력한 대안을 제공한다. 사실, 몇몇 프로그래밍 언어는 명시적으로 loop를 지원하지 않는 경우가 있는데, 이 경우에 재귀가 활용된다.

<hr>

# 4.1 Illustrative Examples

## 4.1.1 The Factorial Function

먼저 팩토리얼(주로 순열을 구하는데 사용됨)의 특징은 다음 그림을 참고하자.
![](https://velog.velcdn.com/images/calzone0404/post/a622297f-da33-4ba0-8d98-aaeddb42b5d6/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/07be1d50-2336-4921-83b2-f1ee0ddbf0d7/image.png)

```python
def factorial(n):
	if n == 0:
    	return 1
    else: return n * factorial(n - 1)
```
![](https://velog.velcdn.com/images/calzone0404/post/14c80d69-5a88-4bdd-a0c4-ae3a4a837b16/image.png)

<hr>

## 4.1.2 Drawing an English Ruler
![](https://velog.velcdn.com/images/calzone0404/post/a7d3d8d1-1112-4014-971b-b9df022ee7dd/image.png)


사실 팩토리얼을 구하는 알고리즘에 굳이 재귀를 사용하지 않고 반복문을 통해 구현해도 된다. 더 복잡한 재귀 문제의 예시로, 일반적인 "자"의 마킹을 그리는 방법에 대해 이야기해보자.

각 인치마다, 숫자 라벨과 함께 하나의 tick을 놓는다. 이 tick을 **major tick**이라고 하고, 그 길이를 **major tick length**라고 하자. 

두 인치들 사이에 있는 마크들을 **minor tick**이라고 하자. 1/2 지점에 하나, 1/4 지점에 하나, 1/8 지점에 하나씩 놓여있다. 이 tick 사이의 간격이 절반으로 줄어들 때 마다, tick 길이는 하나씩 줄어든다.

<hr>

### A Recursive Approach to Ruler Drawing

자에 있는 패턴은 프랙탈 구조의 예시이다. 즉, 재귀 패턴을 가지고 있는 대표적인 예시이다.

먼저 **사진에 있는 것 중, (B)에 대해서,**

0과 1 숫자는 무시하고, major tick 길이는 5이다. 이 두 major tick 사이에 놓인 tick들을 어떻게 놓아야할지 생각해보자.

중앙에 놓인 central tick의 길이는 4이다. 그리고, 그 양 옆에 끼어있는 위아래 패턴의 중앙 tick 길이는 3이다.

따라서 일반화하자면, central tick 길이 $L >= 1$에 대해서,

- Central tick 위에 있는 central tick의 길이 = $L - 1$
- Central tick의 길이 = $L$
- Central tick 아래에 있는 central tick의 길이 = $L - 1$

```python
# 선 그려주는 함수를 따로 지정
def draw_line(tick_length, tick_label = ""):
    line = '-' * tick_length
    if tick_label:
        line += " " + tick_label
    print(line)

# 프랙탈 구조 재귀함수
def draw_interval(center_length):
    if center_length > 0:
        draw_interval(center_length - 1) # 중앙 선 위에 있는 프랙탈 구조 그리기
        draw_line(center_length) # 중앙 선 그리기
        draw_interval(center_length - 1) # 중앙 선 아래에 있는 프랙탈 구조 그리기


def draw_ruler(num_inches, major_length):
    draw_line(major_length, '0') # 0 line을 일단 그려두고
    # 1부터 위아래로 존재하는 프랙탈 구조를 그려버린다.
    for j in range(1, 1 + num_inches):
        draw_interval(major_length - 1) # 프랙탈 구조 그리기
        draw_line(major_length, str(j)) # 프랙탈 구조 다 그렸으면, major line 그리기


if __name__ == '__main__':
    draw_ruler(2, 3)
```

![](https://velog.velcdn.com/images/calzone0404/post/be75305e-53c5-4f22-8006-4462b1276173/image.png)

<hr>

## 4.1.3 Binary Search
```python
def binary_search(arr, target, low, high):
    if low > high:
        return False
    else:
        mid = (low + high) // 2
        if arr[mid] == target:
            return True
        elif arr[mid] > target:
            return binary_search(arr, target, low, mid - 1)  # return 추가
        else:
            return binary_search(arr, target, mid + 1, high)  # return 추가


if __name__ == '__main__':
    nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    print(binary_search(nums, 7, 0, len(nums) - 1))
```

![](https://velog.velcdn.com/images/calzone0404/post/22a6c07f-b43e-4205-bfae-96d1edafe684/image.png)

<hr>

## 4.1.4 File Systems

현대 운영체계에서는 파일시스템 디렉토리를 재귀적인 방법을 통해 구현한다. 파일시스템은 가장 상위레벨의 디렉토리와, 그 안에 딸려있는 여러 파일 및 하위 디렉토리로 구성된다. 

![](https://velog.velcdn.com/images/calzone0404/post/3b725f87-0c5e-44bb-af6b-dc6a4caa5661/image.png)
우리는 위 사진에서 디렉토리 위 숫자가 의미하는 디스크 총 사용량을 재귀적인 방법으로 구할 것이다. 이 때, 디렉토리 내부에 존재하는 숫자는 해당 디렉토리의 개별 디스크 사용량이라고 생각하면 된다.

<hr>

### Python's os Module

파이썬에서는 os 모듈을 통해 디스크 총 사용량 프로그램을 구할 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/21a9005c-06bc-4a1e-aa02-07fae7fd6f62/image.png)

```python
import os


def disk_usage(path):
    total = os.path.getsize(path)
    if os.path.isdir(path):  # 해당 파일이 디렉토리라면
        for filename in os.listdir(path):
            child_path = os.path.join(path, filename)  # 폴더 안에 들어있는 하위 디렉토리 경로 생성
            total += disk_usage(child_path)

    print('{0:<7}'.format(total), path)
    return total


if __name__ == '__main__':
    disk_usage(os.getcwd()) # 현재 작업 디렉토리의 디스크 총 사용량을 구해보자

```

실제로 위 코드를 실행해보면, 아래와 같이 결과가 출력된다.
![](https://velog.velcdn.com/images/calzone0404/post/8423bdc9-61ff-4c13-b366-603b34e657ba/image.png)

<hr>

# 4.2 Analyzing Recursive Algorithms

### Computing Factorials

팩토리얼의 효율성을 계산하는것은 상대적으로 쉽다. factorial(n)을 계산할 때, n, n-1, ... 1까지 총 n + 1회의 계산과정이 존재하므로 이를 빅오표기법으로 표현하면 $O(n)$이다.

<hr>

### Drawing an English Ruler

이 프로그램을 분석할 때, draw_interval(c)가 얼마나 많은 출력 라인을 호출하는지에 대해서 조사를 해보아야 한다. 소스코드와 재귀 흔적을 조사함으로써 어느정도 분석이 가능하다. 0보다 큰 C(이때 C는 central tick의 길이)에 대해서 draw_interval 함수 호출시, c-1 central tick을 그리는 함수 호출을 두번 수행하고, 중앙 선을 그리는 호출 한번이 수행된다는점을 알 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/1b0b0aa7-8686-491c-a56c-f6036836486a/image.png)

따라서 이 알고리즘의 시간복잡도는 수학적 귀납법을 사용하여 구할 수 있다. 사실 대부분의 재귀알고리즘을 검증하는데에는 수학적 귀납법이 사용된다. 자를 그리는 알고리즘에 한번 적용해보도록 하자.

> 귀납식 : $L(c)=1+2⋅L(c−1)$
$L(c) = 2^c - 1$

1. $c = 0$ 이면 $2^c - 1$은 0이다.
2. c일 때 위 귀납식을 만족한다고 할 때, c + 1은 위 식을 만족할까?

$L(c + 1)=1+2⋅L(c)$
-> $L(c) = 2^c - 1$
-> $L(c + 1)=1+2(2^c - 1)$
-> $L(c + 1)=2^{c+1} - 1$

따라서 수학적 귀납법에 따라 귀납식을 만족하므로, 이 알고리즘의 시간복잡도는 $O(2^c)$이다. (c는 tick의 길이)

<hr>

### Performing a Binary Search

이진탐색에서, 상수시간이 소요되는 잡다한 요소는 제외하고, 재귀쪽에 집중해보자. 재귀가 수행될때 마다, 탐색하는 영역이 n -> n/2 -> n/4 -> n/8 ... 점점 좁혀진다. 

따라서 수행홧수를 r이라고 할 때, $n/2^r < 1$이다.
이 수행횟수에 대한 시간복잡도를 구하면
$r = logn + 1$ 이므로
시간복잡도는 $O(logn)$이다.

### Computing Disk Space Usage

이 알고리즘의 Problem size를 설정해야 하는데, 우리는 n을 파일시스템에 존재하는 파일의 개수라고 설정해보자.

- 재귀 호출의 수: 파일 시스템의 각 엔트리에 대하여 디스크 사용량 함수는 한 번씩 호출된다. 이는 코드의 for 루프에서 각 디렉토리 내의 엔트리를 처리할 때 발생합니다. 각 엔트리는 고유한 디렉토리에 속하며 해당 디렉토리 내에서 한 번만 탐색된다.

- 시간 복잡도 분석: 함수의 각 호출에서 os.path.getsize 같은 일정한 단계가 있긴 하지만, 엔트리가 디렉토리인 경우 추가적으로 그 디렉토리 내의 모든 엔트리를 반복하는 for 루프를 포함합니다. 이론적으로, 
$O(n^2)$의 시간 복잡도를 가질 수 있지만, 실제로는 
$O(n)$의 시간 복잡도를 증명할 수 있다.

- 효율적인 복잡도 증명: 전체 for 루프 반복 횟수는
$n−1$번으로, 각 반복은 디스크 사용량에 대한 재귀 호출을 수행한다. 이는 각 디렉토리가 $n$에 비례하는 수의 엔트리를 포함할 수는 있지만, 모든 디렉토리가 그럴 수는 없다는 사실에 기반합니다.

따라서, 이 재귀 알고리즘은 전체적으로 $O(n)$의 시간 복잡도를 가지며, 파일 시스템의 효율적인 트리 순회를 나타낸다. 

<hr>

# 4.3 Recursion Run Amok

재귀는 매우 강력한 도구이지만, 효율적으로 사용되지 못한 재귀는 매우매우 좋지 않다.

예를 들어서, element uniqueness problem 문제를 살펴보자.
```python
def unique3(S, start, stop): # S[start:stop]에 중복되는 요소가 존재하는지 판단
	if stop - start <= 1: return True # 요소가 하나만 남았으면 True
    elif not unique(S, start, stop-1): return False
    elif not unique(S, start+1, stop): return False
    else: return S[start] !+ S[stop-1]
```
딱 보면, 재귀 수행 시 중복되는 연산이 너무 많음을 볼 수 있다.
만약 S의 size를 n이라고 했을때,
- n = 1이라면, $O(1)$
- n >= 2 이라면, 2 * (1 + 2 + $2^2$ + ... + $2^n$)이므로
최종적인 시간복잡도는 $O(2^n)$인 매우 비효율적인 알고리즘이다.

또 다른 예로, 피보나치 구현에 있다. 우리가 알고있는 정석적인 피보나치 구현은 사실 매우 비효율적이다.

예를들어, n번째 피보나치 수를 구할 때, exponentail 호출횟수가 필요로 하다. 예를 들어, $c_{n}$이 n번째 피보나치를 구하기 위해 필요한 숫자라고 해보자.

![](https://velog.velcdn.com/images/calzone0404/post/aa973836-bc92-458f-94fd-5021abd205f6/image.png)

위 그림을 보면, 
- $c_{4}$는 $c_{2}$보다 두배 더 많은 수행이 필요하며, 
- $c_{5}$는 $c_{3}$보다 두배 더 많은 수행이 필요하다.

따라서 $c_{n} > 2^{n/2}$ 이므로 시간복잡도가 $O(2^n)$인 매우 비효율적인 알고리즘임을 알 수 있다.

### An Efficient Recursion for Computing Fibonacci Numbers

설명된 최적화 방법은 함수의 반환 값을 변경하여 피보나치 수를 효율적으로 계산합니다. "좋은 재귀"라고 불리는 이 접근법은 피보나치 수 $F(n)$만 반환하는 대신, 연속된 두 피보나치 수 $F(n)$과 $F(n−1)$을 함께 반환합니다. 

이 변화는 각 재귀 수준에서 이미 계산된 값을 다음 수준으로 전달함으로써 중복 계산을 방지합니다. 예를 들어, 함수는 $F(n−1)$을 계산한 후, 이 값을 이용하여$F(n)$을 계산할 수 있습니다. 이로 인해 각 재귀 호출이 
n을 1씩 감소시키며, n번의 함수 호출이 포함된 재귀 추적을 통해 전체 계산이 $O(n)$의 시간 복잡도로 실행됩니다.

```python
def good_fib(n):
	if n <= 1:
    	return (n, 0)
    else:
    	(a, b) = good_fib(n - 1)
        return (a + b, a)
```

## 4.3.1 Maximum Recursive Depth in Python

재귀를 사용함에 있어서, base condition에 도달하지 않고, 무한히 재귀 Loop를 도는 경우가 존재한다.
이를 **infinite recursion**이라고 한다.

만약 infinite recursion이 발생한다면, CPU자원과 Memory자원을 아주 빠르게 소비시킨다. 따라서, 이러한 infitinte recursion을 발생시키지 않기 위해, 프로그래머는 반드시 base condition과, 그에 도달하는 알고리즘을 잘 구성해두어야 한다.

운이 좋게도, 파이썬에서는 infinite recursion을 방지하는 모듈을 지원한다.

```python
import sys

old_recursion = sys.getrecursionlimit() # 현재 설정된 재귀함수 최대 깊이를 가져온다
sys.setrecursionlimit(1000000) # 최대 재귀함수 깊이를 1000000으로 설정함
```

<hr>

# 4.4 Further Examples of Recursion

## 4.4.1 Linear Recursion

재귀함수가 body 안에서 최대 한번만 새로운 재귀함수를 호출하는 경우, **Linear recursion**이라고 한다. 물론 우리가 이전에 구현해보았던 factorial, fibonacci, binary_search가 이에 해당한다.

Linear recursion의 결과는 모든 recursion 경로를 단일 호출 sequence로 표현할 수 있다는 점이다.

<hr>

### Summing the Elements of a Sequence Recursively

Linear Recursion은 데이터 배열을 다르는데 효율적인 도구가 될 수 있다. 예를 들면, 배열의 Summation 문제를 linear recursion을 이용해 풀어보도록 하자.

- n : 배열 요소의 개수

1. n = 0이면, 당연히 Summation S는 0이다.

2. 2개의 원소의 총 합은 1개의 원소의 합 + 마지막 원소의 합이다.

3. 3개의 원소의 총 합은 2개의 원소의 합 + 마지막 원소의 합이다.

4. 따라서, n개의 원소의 총 합은 n-1개의 원소의 합 + 마지막 원소의 합이다.

```python
def linear_sum(arr, n):
    if n == 0:
        return 0
    else:
        return linear_sum(arr, n-1) + arr[n-1]
```

위 알고리즘의 시간복잡도를 구해보면, n개의 합을 구하기 위해 총 n회의 재귀 호출이 수행되므로, $O(n)$이다.

![](https://velog.velcdn.com/images/calzone0404/post/d87b2edb-f1fa-497e-959a-37c7040de898/image.png)

<hr>

### Reversing a Sequence with Recursion

이번에는 n size sequence를 뒤집는 알고리즘을 linear recursion를 통해 풀어보도록 하자.

- 첫번째 요소와, 마지막 요소 (n-1)를 바꾼다.
- 두번째 요소와, n-2번째 요소를 바꾼다....

따라서 linear recursion을 이용하자면, 재귀적으로 첫번째 index와 last index를 좁혀나가며 구해나가면 된다.

이때, base condition으로, 
- 만약 Sequence의 크기가 짝수라면 start == stop 인 경우 종료
- 만약 Sequence의 크기가 홀수라면 start == stop - 1 인 경우 종료

![](https://velog.velcdn.com/images/calzone0404/post/e9338fe0-3664-406e-84dd-109f76b5722f/image.png)
위 사진은 array size 7에 대해 총 4회 연산을 수행하였다.
따라서 위 알고리즘의 시간복잡도를 구해보면, array size n에 대해, floor(n/2) + 1 이므로, $O(n)$임을 알 수 있다.

```python
def reverse_array(arr, start, stop):
    if start >= stop:
        return arr
    else:
        arr[start], arr[stop] = arr[stop], arr[start]
        return reverse_array(arr, start + 1, stop - 1)

nums = [1, 2, 3, 4, 5, 6, 7, 8, 9]
reversed_arr = reverse_array(nums, 0, len(nums) - 1)
print(reversed_arr)
```
![](https://velog.velcdn.com/images/calzone0404/post/be311be9-ecf6-402e-8a34-33331d3fcf43/image.png)

### Recursive Algorithms for Computing Powers

Linear recursion을 통해 거듭제곱 연산도 구현할 수 있다.
즉, $power(x, n) = x^n$인 함수를 구할수 있다는 것이다.

basic condition으로는, 지수법칙에 의해 n = 0일때 1이다.

따라서 재귀 함수를 다음과 같이 표현할 수 있다.
![](https://velog.velcdn.com/images/calzone0404/post/8c4ba059-3bb2-48de-b1ff-a14af9e4b444/image.png)

```python
def power(base, exponent):
    if exponent == 0:
        return 1
    else:
        return base ** power(base, exponent-1)
```

딱 코드를 보면, 재귀가 선형으로 호출되므로 $O(n)$이 걸린다는 것을 알 수 있다.

**그런데, 위 알고리즘보다 더 빠르게 작동하는 알고리즘이 존재한다.**

> $x^n = (x^k)^2$ 으로 표현하기 위해, k = floor(n/2)라고 해보자.
>
>만약 n이 짝수라면, floor(n/2) = $n/2$이므로
>$(x^k)^2 = (x^\frac{n}{2})^2 = x^n$이다.
>
>만약 n이 홀수라면, floor(n/2) = $\frac{n-1}{2}$이므로,
$(x^k)^2 = (x^\frac{n-1}{2})^2 = x^{n-1}$이다.
>
따라서, $x^n = x * (x^k)^2$이다.
예를 들면, $2^{13} = 2 * 2^6 * 2^6$이다.

![](https://velog.velcdn.com/images/calzone0404/post/3117e0c8-c569-4646-b17c-352af5abb11e/image.png)

```python
def power(x, n):
    if n == 0:
        return 1
    else:
        partial = power(x, n // 2)
        result = partial * partial
        if n % 2 == 1:
            result *= x
        return result

print(power(2, 10))
```
![](https://velog.velcdn.com/images/calzone0404/post/8cf4209e-e592-4e8f-ae67-7ab17b40c475/image.png)

![](https://velog.velcdn.com/images/calzone0404/post/8f26acca-0b64-4976-ac03-acd9591882d5/image.png)

위 코드는 n을 2로 나누면서 진행하기 때문에, 수행횟수가 지수적으로 줄어들어, 시간복잡도는 $O(logn)$이다.


## 4.4.2 Binary Recursion

어떤 함수가 2회의 recursive 호출을 수행하면, **binary recursion**을 한다고 말한다. 예를들어, Drawing English ruler문제나, bad_fibonacci가 그 예시이다.

4.4.1에서 구한 Sequence summation을 binary recursion으로 한번 구해보자.
```python
def binary_sum(S, start, stop): # S[start:stop]까지의 summation을 구한다
    if start >= stop: # slice에 element가 존재하지 않는 경우
        return 0
    elif start == stop - 1: # slice에 element가 한개만 존재하는 경우
        return S[start]
    else:
        mid = (start + stop) // 2 # 두 수의 mid index 기준으로 두 구역으로 나누어버림
        return binary_sum(S, start, mid) + binary_sum(S, mid, stop)

nums = [1, 2, 3, 4, 5, 6, 7, 8]
print(binary_sum(nums, 0, len(nums)))
```
![](https://velog.velcdn.com/images/calzone0404/post/c07ddae4-9397-46e1-ba1f-9d1deb9cddf5/image.png)

위 사진처럼, 범위가 재귀 호출마다 **절반으로 감소하므로** 재귀의 깊이는 $1 + log_{2}n$이다.

따라서 위 알고리즘의 공간복잡도는 $O(logn)$이다. 기존 방식의 공간복잡도 $O(n)$에 비하면 매우 효율적임을 알 수 있다.

하지만, 위 그림에서 볼 수 있다싶이 총 호출 수는 n = 8일때, 
$2 * 8 - 1회 (15회)$이므로 시간복잡도는 $O(n)$이다.

> 그럼에도 불구하고, 공간복잡도의 향상은 곧 스택메모리의 가용성을 향상시키므로 더 깊은 재귀 호출을 사용할 수 있게 된다.

## 4.4.3 Multiple Recursion

Binary recursion이 있다면 당연히 그 이상의 recursion도 존재한다. multiple recursion이 바로 그것이다.

우리가 보았던 예시중에서는, Disk usage를 구하는 알고리즘이 바로 이것이다. 왜나하면, 디렉토리 내부에 존재하는 하위 디렉토리의 개수마다 재귀 호출 개수가 증가하기 때문이다.

또 다른 새로운 예시로, Summation puzzles가 있다.

> 합산 퍼즐 문제:
각각의 문자에 하나의 숫자를 할당하여, 그 문자들로 구성된 단어를 숫자로 표현했을 때, 주어진 수학적 등식이 성립하는지 알아보는 문제이다.

이해가 잘 되지 않으니 예를 한번 들어보자.
![](https://velog.velcdn.com/images/calzone0404/post/fda1dcf9-7c60-4fa7-ade9-303122085c17/image.png)

$pot + pan = bib$라는 합산 퀴즈가 있다고 해보자.

나는 위 식의 각 문자에 대해 중복되지 않는 숫자를 mapping해보도록 하자 (마음대로 아무거나 넣어보도록 하자)
$p = 5, o = 3, t = 4, a = 2, n = 6, b = 9, i = 1$

이 할당에 따라 mapping을 수행하면 위 등식은 다음과 같이 계산된다.
$pot = 534$
$pan = 526$
$bib = 919$

이제 등식을 만족하는지 알아보자.
534 + 526 = 1060인데, 919가 아니므로 이 숫자할당은 올바르지 않다는것을 알 수 있다.

> 따라서, 이 문제를 해결하려면 가능한 모든 숫자 조합을 할당해보고 등식이 성립하는 알맞는 숫자 할당 조합을 찾는것이다.

1. 각 문자에 가능한 모든 숫자 $U = \{0, 1, ..., 9\}$를 할당해본다.
2. 할당된 숫자로 단어를 숫자로 변환한다.
3. 변환된 숫자를 사용해서, 등식을 평가한다.
4. 모든 가능한 조합을 시도해서 등식이 만족하는 조합을 찾는다.

![](https://velog.velcdn.com/images/calzone0404/post/77cc329b-0ae2-408d-a225-0373844157fa/image.png)

```python
def puzzle_solver(k, S, U):
    """ k: 몇 번 숫자를 뽑아야 하는지에 대한 index, S: 선택된 숫자 리스트, U: 선택 가능한 숫자 리스트 """
    for i in range(len(U)):
        v = U[i]
        S.append(v)  # 선택된 숫자 v를 리스트 S에 추가
        U_new = U[:i] + U[i+1:]  # v를 제외한 새로운 U 생성
        if k == 1:  # 필요한 모든 숫자를 선택했으면
            if checker(S):  # S를 검증
                return "가능한 조합: {}".format(S)
        else:
            result = puzzle_solver(k - 1, S, U_new)  # 다음 숫자 선택을 위한 재귀 호출
            if result: # result 결과가 None이 아니라면
                return result
        S.pop()  # 백트래킹: S에서 가장 최근에 넣은 숫자 제거

    return None  # 해당 조합이 실패한경우 None


def checker(S):
    """ 주어진 S에 대해 'pot + pan = bib' 검증 """
    mapping = dict(zip('pontiab', S))
    pot = 100 * mapping['p'] + 10 * mapping['o'] + mapping['t']
    pan = 100 * mapping['p'] + 10 * mapping['a'] + mapping['n']
    bib = 100 * mapping['b'] + 10 * mapping['i'] + mapping['b']
    return pot + pan == bib  # 계산된 값이 일치하는지 확인


if __name__ == "__main__":
    solution = puzzle_solver(7, [], list(range(10)))
    if solution:
        print(solution)
    else:
        print("실패")
```
![](https://velog.velcdn.com/images/calzone0404/post/ca30836f-25fa-4413-9f47-1fd3991f1203/image.png)

# 4.5 Designing Recursive Algorithms

재귀 알고리즘은 다음 규칙을 따른다.

1. Base case 테스트를 해보자.
재귀 알고리즘은 base case를 테스트하는것부터 시작한다. Base case를 테스트하면서 모든 재귀 호출이 base case로 들어오는지 확인해보아야 한다.

2. 재귀
base case가 아니라면, 하나 또는 그 이상의 재귀 호출을 수행하자. base case까지 도달하는 재귀호출문을 작성해야 한다.

### Parameterizing a Recursion

> 재귀적 알고리즘을 설계할 떄는 원래 문제와 같은 일반 구조를 가진 서브 문제들을 어떻게 정의해야할 지 생각해보아야한다.

예를 들어서, 이진 탐색의 경우
우리가 자연스럽게 생각나는 재귀 함수 형태는 아래와 같다
```python
binary_search(data, target)
```
위 코드는 low, high index가 존재하지 않기 때문에 리스트의 절반에 대한 탐색을 호출하는 유일한 방법은 새로운 리스트를 생성하는 방법 뿐이다.

하지만 새로운 리스트를 형성하는 과정속에서 $O(n)$이 소요되므로 이진 탐색의 이점을 앗아간다.

따라서 아래와 같이 추가적인 파라미터를 달아서 이진탐색의 장점을 가지는것이 바람직하다.
```python
binary_search(data, target, low, high)
```

# 4.6 Eliminating Tail Recursion

> 이 챕터는 이해가 안되어서 Chat GPT의 도움을 받았습니다.

Tail recursion은 재귀 함수의 매우 특별한 형태로, 재귀 호출이 함수의 마지막 명령어로 실행되는 경우를 말합니다. 이러한 구조는 함수의 반환 값이 재귀 호출의 결과와 직접적으로 연결되어 있어, 추가적인 연산 없이 그 결과를 바로 반환할 수 있습니다. Tail recursion의 주요 장점은 컴파일러나 인터프리터가 이를 최적화하여, 일반적인 재귀 호출에서 발생하는 스택 오버플로우 문제를 피할 수 있다는 것입니다.

## Tail Recursion의 작동 방식
일반적인 재귀 함수는 각 호출마다 스택 메모리에 새로운 실행 컨텍스트를 생성합니다. 이로 인해 깊은 재귀 호출이 발생할 경우, 시스템의 스택 메모리가 고갈되어 스택 오버플로우 에러를 유발할 수 있습니다. 그러나 tail recursion을 사용하면, 현재의 실행 컨텍스트를 재사용할 수 있어, 각 호출이 새로운 스택을 필요로 하지 않습니다.

## Tail Recursion 최적화
많은 현대 컴파일러와 인터프리터는 "tail call optimization" (TCO)을 지원하여, 재귀 호출을 일반적인 반복문(loop)으로 변환하여 실행합니다. 이 최적화는 재귀 함수가 자기 자신을 호출하는 대신, 실행 상태를 업데이트하고 함수 시작 지점으로 점프하는 것을 가능하게 합니다. 결과적으로 추가적인 스택 프레임을 생성하지 않고, 기존의 스택 프레임을 재활용함으로써 메모리 사용을 상당히 줄일 수 있습니다.

## 예시: Factorial 함수
Tail recursion을 활용한 팩토리얼 함수 예시입니다:

```python
def factorial(n, accumulator=1):
    if n == 0:
        return accumulator
    else:
        return factorial(n - 1, accumulator * n)
```
여기서 factorial 함수는 마지막에서 재귀 호출을 수행하며, 추가적인 연산 없이 결과를 바로 반환합니다. accumulator는 각 단계의 계산 결과를 캐리하며, 최종적으로 n이 0이 되면 그 값을 반환합니다.

## 주의점
모든 프로그래밍 언어나 환경이 tail call 최적화를 지원하는 것은 아니며, 예를 들어 Python은 공식적으로 이 최적화를 지원하지 않습니다. 따라서 Python과 같은 환경에서는 tail recursion이 꼭 필요한 경우, 반복문으로 리팩토링하는 것이 좋을 수 있습니다. Tail recursion이 이론적으로는 스택 오버플로우 문제를 해결할 수 있지만, 실제로는 최적화가 적용되지 않는 경우가 많으므로 주의가 필요합니다.

