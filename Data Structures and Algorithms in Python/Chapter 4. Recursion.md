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

따라서 일반화하자면, central tick 길이 $$L >= 1$$에 대해서,

- Central tick 위에 있는 central tick의 길이 = $$L - 1$$
- Central tick의 길이 = $$L$$
- Central tick 아래에 있는 central tick의 길이 = $$L - 1$$

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

팩토리얼의 효율성을 계산하는것은 상대적으로 쉽다. factorial(n)을 계산할 때, n, n-1, ... 1까지 총 n + 1회의 계산과정이 존재하므로 이를 빅오표기법으로 표현하면 $$O(n)$$이다.

<hr>

### Drawing an English Ruler

이 프로그램을 분석할 때, draw_interval(c)가 얼마나 많은 출력 라인을 호출하는지에 대해서 조사를 해보아야 한다. 소스코드와 재귀 흔적을 조사함으로써 어느정도 분석이 가능하다. 0보다 큰 C(이때 C는 central tick의 길이)에 대해서 draw_interval 함수 호출시, c-1 central tick을 그리는 함수 호출을 두번 수행하고, 중앙 선을 그리는 호출 한번이 수행된다는점을 알 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/1b0b0aa7-8686-491c-a56c-f6036836486a/image.png)

따라서 이 알고리즘의 시간복잡도는 수학적 귀납법을 사용하여 구할 수 있다. 사실 대부분의 재귀알고리즘을 검증하는데에는 수학적 귀납법이 사용된다. 자를 그리는 알고리즘에 한번 적용해보도록 하자.

> 귀납식 : $$L(c)=1+2⋅L(c−1)$$
$$L(c) = 2^c - 1$$

1. $$c = 0$$ 이면 $$2^c - 1$$은 0이다.
2. c일 때 위 귀납식을 만족한다고 할 때, c + 1은 위 식을 만족할까?

$$L(c + 1)=1+2⋅L(c)$$
-> $$L(c) = 2^c - 1$$
-> $$L(c + 1)=1+2(2^c - 1)$$
-> $$L(c + 1)=2^{c+1} - 1$$

따라서 수학적 귀납법에 따라 귀납식을 만족하므로, 이 알고리즘의 시간복잡도는 $$O(2^c)$$이다. (c는 tick의 길이)

<hr>

### Performing a Binary Search

이진탐색에서, 상수시간이 소요되는 잡다한 요소는 제외하고, 재귀쪽에 집중해보자. 재귀가 수행될때 마다, 탐색하는 영역이 n -> n/2 -> n/4 -> n/8 ... 점점 좁혀진다. 

따라서 수행홧수를 r이라고 할 때, $$n/2^r < 1$$이다.
이 수행횟수에 대한 시간복잡도를 구하면
$$r = logn + 1$$ 이므로
시간복잡도는 $$O(logn)$$이다.

### Computing Disk Space Usage

이 알고리즘의 Problem size를 설정해야 하는데, 우리는 n을 파일시스템에 존재하는 파일의 개수라고 설정해보자.

- 재귀 호출의 수: 파일 시스템의 각 엔트리에 대하여 디스크 사용량 함수는 한 번씩 호출된다. 이는 코드의 for 루프에서 각 디렉토리 내의 엔트리를 처리할 때 발생합니다. 각 엔트리는 고유한 디렉토리에 속하며 해당 디렉토리 내에서 한 번만 탐색된다.

- 시간 복잡도 분석: 함수의 각 호출에서 os.path.getsize 같은 일정한 단계가 있긴 하지만, 엔트리가 디렉토리인 경우 추가적으로 그 디렉토리 내의 모든 엔트리를 반복하는 for 루프를 포함합니다. 이론적으로, 
$$O(n^2)$$의 시간 복잡도를 가질 수 있지만, 실제로는 
$$O(n)$$의 시간 복잡도를 증명할 수 있다.

- 효율적인 복잡도 증명: 전체 for 루프 반복 횟수는
$$n−1$$번으로, 각 반복은 디스크 사용량에 대한 재귀 호출을 수행한다. 이는 각 디렉토리가 $$n$$에 비례하는 수의 엔트리를 포함할 수는 있지만, 모든 디렉토리가 그럴 수는 없다는 사실에 기반합니다.

따라서, 이 재귀 알고리즘은 전체적으로 $$O(n)$$의 시간 복잡도를 가지며, 파일 시스템의 효율적인 트리 순회를 나타낸다. 

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

- n = 1이라면, $$O(1)$$
- n >= 2 이라면, 2 * (1 + 2 + $$2^2$$ + ... + $$2^n$$)이므로
최종적인 시간복잡도는 $$O(2^n)$$인 매우 비효율적인 알고리즘이다.

또 다른 예로, 피보나치 구현에 있다. 우리가 알고있는 정석적인 피보나치 구현은 사실 매우 비효율적이다.

예를들어, n번째 피보나치 수를 구할 때, exponentail 호출횟수가 필요로 하다. 예를 들어, $$c_{n}$$이 n번째 피보나치를 구하기 위해 필요한 숫자라고 해보자.

![](https://velog.velcdn.com/images/calzone0404/post/aa973836-bc92-458f-94fd-5021abd205f6/image.png)

위 그림을 보면, 
- $$c_{4}$$는 $$c_{2}$$보다 두배 더 많은 수행이 필요하며, 
- $$c_{5}$$는 $$c_{3}$$보다 두배 더 많은 수행이 필요하다.

따라서 $$c_{n} > 2^{n/2}$$ 이므로 시간복잡도가 $$O(2^n)$$인 매우 비효율적인 알고리즘임을 알 수 있다.

### An Efficient Recursion for Computing Fibonacci Numbers

설명된 최적화 방법은 함수의 반환 값을 변경하여 피보나치 수를 효율적으로 계산합니다. "좋은 재귀"라고 불리는 이 접근법은 피보나치 수 $$F(n)$$만 반환하는 대신, 연속된 두 피보나치 수 $$F(n)$$과 $$F(n−1)$$을 함께 반환합니다. 

이 변화는 각 재귀 수준에서 이미 계산된 값을 다음 수준으로 전달함으로써 중복 계산을 방지합니다. 예를 들어, 함수는 $$F(n−1)$$을 계산한 후, 이 값을 이용하여$$F(n)$$을 계산할 수 있습니다. 이로 인해 각 재귀 호출이 
n을 1씩 감소시키며, n번의 함수 호출이 포함된 재귀 추적을 통해 전체 계산이 $$O(n)$$의 시간 복잡도로 실행됩니다.

```python
def good_fib(n):
	if n <= 1:
    	return (n, 0)
    else:
    	(a, b) = good_fib(n - 1)
        return (a + b, a)
```

## 4.3.1 Maximum Recursive Depth in Python





