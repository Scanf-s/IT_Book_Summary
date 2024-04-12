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
