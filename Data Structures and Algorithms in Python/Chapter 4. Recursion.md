재귀란, 함수가 자기 자신을 실행중에 한번 또는 그 이상 호출하는 것이다. 예시로, 프랙탈, 러시아 마트료시카 인형이 있다.

컴퓨터에서 재귀는 반복되는 작업을 수행할 때, 우아하고 강력한 대안을 제공한다. 사실, 몇몇 프로그래밍 언어는 명시적으로 loop를 지원하지 않는 경우가 있는데, 이 경우에 재귀가 활용된다.

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

