# 3.1 Experimental Studies

Python에서는 다음과 같이 알고리즘 수행시간을 측정할 수 있다.

```python
from time import time

start_time = time()
알고리즘 수행
end_time = time()
elapsed = end_time - start_time
print(elapsed)
```

이 접근 방식은 알고리즘의 효율성을 꽤 잘 반영하지만 완벽하지는 않다. 왜냐하면, 실세계의 시간에 대해서 상대적인 시간을 측정하며, 다른 프로세스가 동시에 CPU를 사용할 경우에는 이 측정시간이 영향을 받을 수 있기 때문이다.

### 실험적으로 알고리즘 실행 시간을 분석하는 할 때, 몇 가지 제한 사항

**비교성**: 동일한 하드웨어와 소프트웨어 환경에서 실험이 수행되지 않는 한 두 알고리즘의 실험적 실행 시간을 직접 비교하는 것이 어려울수밖에 없다 (성능이 다르기 때문).

**테스트 입력의 한계**: 실험에서는 제한된 테스트 입력만 사용할 수 있으며, 실험에 포함되지 않은 입력의 실행 시간을 누락시킬 수 있다(이러한 입력이 중요할 수 있음).

**구현의 필요성**: 이러한 테스트를 수행하기 위해서는 알고리즘이 완벽하게 구현되어 있어야 합니다. 이는 다양한 접근 방식을 고려 중인 디자인 초기 단계에서는 그다지 좋은 방법이 아닐 수 있다.

이러한 요소들은 생산 품질의 코드를 미세 조정할 때 실험적 분석을 적합하게 만들지만, 알고리즘 또는 데이터 구조 선택의 초기 단계에서는 좋지 않다. 

<hr>

## 3.1.1 Moving Beyond Experimental Analysis

이러한 시간 측정 방법을 사용하지 않고 알고리즘 실행시간을 측정하기 위해서는, 고급 언어로 기술한 알고리즘을 분석하보아야 한다(의사코드 or 코드조각).

<hr>

### Measuring Operations as a Function of Input Size

알고리즘 실행시간의 증가하는 정도를 포착하기 위해서는, input size가 n정도로 실행되는 primitive operation들의 특징을 함수 f(n)으로 표현할것이다.

<hr>

### Focusing on the Worst-Case Input

알고리즘은 몇몇 input에 대해는 기대한 정도보다 더 빠르게 수행될수도 있다. 그러므로 우리는 이 알고리즘의 실행시간이 평균 이상의 모든 input들에 대해서도 같은 결과를 기대할 수 있다.하지만, 이러한 average-case 분석은 꽤 어려운 일이다.
![](https://velog.velcdn.com/images/calzone0404/post/6276cfe8-b20a-40b1-9895-545b61f509e6/image.png)

아래 사진을 보면, 독립된 입력들에 대해서 worst-case와 best-case time사이의 모든 지점이 average case가 될 수 있다. 또, 만약 Input이 A와 G뿐이라면? 최악, 최선, 평균 시간이 모두 달라지게 된다.

따라서 이 책에서는 특히 **Worst-case**에 대해서만 집중할 것이다. 훨씬 측정하기 쉽고, Worst-case input에 대해서만 측정해도 되며, 간단하다.

<hr>

## 3.2 The Seven Functions Used in This Book

상수함수, 로그함수, 지수함수,.. 등의 함수를 소개하는 챕터이다.

<hr>

## 3.2.1 Comparing Growth Rates

요약하자면, 각 함수의 증가시간은 다음과 같다.
![](https://velog.velcdn.com/images/calzone0404/post/412d2405-5c06-4832-9934-e18e02942641/image.png)
![](https://velog.velcdn.com/images/calzone0404/post/2c3234ac-d4ee-49ec-9f43-7f60d248ad4e/image.png)

<hr>

### The Ceiling and Floor Functions
![](https://velog.velcdn.com/images/calzone0404/post/93ce211b-3a8f-48c6-b9f2-86b1b0f75f13/image.png)

<hr>

# 3.3 Asymptotic Analysis

알고리즘 분석에 있어서, 우리는 input size n에 대해서 함수가 실행되는 시간의 증가율에 대해서 집중할 것인데, 충분히 큰 n에 대해서 우리는 **어느쪽으로 성장곡선이 수렴해가는지만 알면 충분하다.**

아래 코드는 성장곡선이 n으로 수렴한다.

```python
def find_max(data):
	biggest = data[0]
    for val in data:
    	if val > biggest
        	biggest = val
    return biggest
```

## 3.3.1 The "Big-Oh" Notation

![](https://velog.velcdn.com/images/calzone0404/post/fd81db79-d3e7-4db8-9ea5-314d8e6da290/image.png)
즉, $$f(n)$$이라는 알고리즘보다 항상 큰 $$c*g(n)$$이 존재함을 증명하는 것이다.

이때 이 $$g(n)$$을 단순화해서 표기하는 것을 빅-오 표기법이라고 생각하면 된다.
<hr>

### Big-Omega
![](https://velog.velcdn.com/images/calzone0404/post/387954fe-34b0-4b47-a7db-60770a31013e/image.png)
즉, $$f(n)$$이라는 알고리즘보다 항상 작은 $$c*g(n)$$이 존재함을 증명하는 것이다.

<hr>

### Big-Theta
![](https://velog.velcdn.com/images/calzone0404/post/4ea7f8f6-4364-4e5c-af83-b42c01935b93/image.png)

빅-오, 빅-오메가 사이에 정의될 수 있는 함수가 존재하는지에 대한 것이다. (평균시간)

<hr>

## 3.3.2 Comparative Analysis

알고리즘 A의 시간복잡도가 $$O(n)$$이고, 알고리즘 B의 시간복잡도가 $$O(n^2)$$일때, **A는 B보다 수렴적으로 더 낫다** 라고 말한다. 이처럼 우리는 빅-오 표기법을 통해 시간복잡도를 알아낼 수 있다. 

### Some Words of Caution
빅 오 표기법의 몇가지 주의사항이 있다.

1. 빅 오 표기 시, 숨겨진 상수가 매우 큰 수인경우
예를들어서, $$10^100n$$이 $$O(n)$$임은 틀림없는 사실이지만, 사실 실행시간이 $$10nlogn$$인 알고리즘과 비교하였을 때, 선형 시간이 빠르지만 상수 요인으로 인해서 **실행시간이 로그인 알고리즘**을 택하는것이 좋다. 

 그 이유는, 상수가 너무 큰 수 (우주의 원자 숫자의 최대 상한)값이기 때문이다. 물론, 이렇게 큰 숫자가 입력 크기로 사용될 가능성은 거의 없지만, 유의하는게 좋을 것 같다.

2. 일반적으로 $$O(nlogn)$$시간에서 실행되는 모든 알고리즘은 효율적으로 간주되나, 일부 상황에서는 $$O(n^2)$$도 충분히 빠를 수 있다.

 하지만, 지수시간이 걸리는 알고리즘은 사용을 하지 않는것으로 하자..

<hr>

## 3.3.3 Examples of Algorithm Analysis

여러가지 코드에 대한 시간복잡도가 무엇인지 설명하는 챕터이다.

# 3.4 Simple Justification Techniques

때로는, 이 알고리즘이 옳거나 더 빠르게 동작한다는 것을 보여주기 위해 주장해야하는 경우가 존재한다. 이러한 주장을 풍부하게 하기 위해서는, 반드시 수학적 용어를 통해 "증명"을 해야 한다.

## 3.4.1 By Example

예를 들어, "속성 P를 가지는 집합 S안에 요소 x가 존재한다" 라는 주장에 대해서 증명하기 위해서는, 모든 요소 x가 속성 P를 가지는 집합 S안에 존재함을 보여야 한다.

그런데, 모든 x에 대해서 검증하는것은 불가능하므로, 쉽게 x가 S에 포함되지 않는 반례를 찾으면 된다.

![](https://velog.velcdn.com/images/calzone0404/post/bea4a5c1-fc17-42c7-bbc8-a50359f5af53/image.png)

<hr>

## 3.4.2 The "Contra" Attack

### contrapositive : 대우
위 두가지를 통해 주장이 참 또는 거짓임을 증명할 수 있다.

"p가 참이라면, q는 참이다"에 대우를 적용하면
"q가 거짓이라면, p는 거짓이다"이다.

논리적으로 이 두 문장은 완전히 동일하다.
![](https://velog.velcdn.com/images/calzone0404/post/09cca60f-b6fe-4329-ba44-4ebf94b9e977/image.png)

#### DeMorgan's Law

"p or q" -> "not p and not q"
"p and q" -> "not p or not q"

<hr>

### Contradiction : 모순

주로 드모르간 법칙을 통해 모순 방법을 사용하게 된다.
예를 들어, q is true임을 증명할 때, 먼저 q is false라고 가정하고 시작한다. 이후, 논리적으로 보이며 이 주장은 모순이 발생한다는 것을 설명해주면된다.

![](https://velog.velcdn.com/images/calzone0404/post/593e1c47-31bd-4468-a465-be231d3bf1a4/image.png)

## 3.4.3 Induction and Loop Invariants

### Induction

수학적 귀납법을 사용해서도, 시간 복잡도와 관련된 주장을 확실하게 증명해낼 수 있다.

![](https://velog.velcdn.com/images/calzone0404/post/a7b2f35f-3b35-4eb1-a0d4-1c8b4d2c1539/image.png)

### Loop Invariants

루프 불변식은 알고리즘 분석에서 알고리즘 내의 루프의 정확성을 증명하는 데 사용되는 강력한 도구입니다. 루프 불변식을 이해하고 적용함으로써 우리는 체계적으로 루프가 의도한 대로 작동함을 보여줄 수 있으며, 결국 알고리즘의 정확성을 보장할 수 있습니다.

### 루프 불변식 이해하기
루프 불변식은 다음 조건이 참인 조건입니다:

1. 루프가 시작되기 이전 (초기화).
2. 이전 반복 후 참이었다면 각 반복 전에 참 (유지).
3. 루프가 완료된 후에 프로그램에 대해 유용한 진술을 만듭니다 (종료).

이러한 속성은 루프의 모든 실행 단계에서 논리적으로 루프의 정확성을 주장하는 데 도움이 됩니다.

예시: 루프 불변식 사용하기
제공된 Python 함수 find는 리스트에서 값을 검색하고 그 값이 처음 나타나는 인덱스를 반환합니다. 값이 발견되지 않으면 -1을 반환합니다. 함수는 다음과 같이 설명됩니다:

### find 함수의 루프 불변식

```python
def find(S, val):
    """Return index j such that S[j] == val, or -1 if no such element."""
    n = len(S)
    j = 0
    while j < n:
        if S[j] == val:
            return j  # 인덱스 j에서 일치하는 항목이 발견됨
        j += 1
    return -1  # S에 val이 없음
```

이 함수에 대한 루프 불변식 $$L_{j}$$를 다음과 같이 정의합니다:

$$L_{j}$$: "val은 S의 첫 j개 원소 중 어느 것과도 같지 않다."

1. 초기화
루프가 시작될 때 (j=0일 때), L0은 자명하게 참입니다. 왜냐하면 S의 첫 0개 원소 중에서 val과 비교할 원소가 없기 때문입니다.

2. 유지
$$L_{j}$$가 반복 j 전에 참이라고 가정합시다. 

 반복 동안 S[j] == val이면 함수는 j를 반환하고 알고리즘의 정확성이 명확하게 확인됩니다. 
 
 S[j] != val이면, j가 증가됩니다. 불변식은 val이 첫 j+1개 원소와 같지 않다는 것을 유지해야 합니다(첫 j개 원소와 같지 않았으며 j번째 원소와도 같지 않음). 이로써 다음 반복으로 넘어갈 때 불변식을 유지합니다.

3. 종료
루프가 원소를 찾지 못하고 종료되면 (즉, j가 n에 도달함), 불변식은 S에 val과 같은 원소가 없다고 주장합니다 (Ln이 참). 따라서 함수는 정확하게 -1을 반환하여 S에 val이 없음을 나타냅니다.

4. 결론
이러한 방식으로 루프 불변식을 사용하는 것은 알고리즘의 정확성을 증명하는 데만 도움이 되는 것이 아니라 복잡한 루프의 흐름과 논리를 이해하는 데도 도움이 됩니다. 초기화, 유지, 종료로 증명을 나누어 루프 작동의 각 부분을 체계적으로 검증할 수 있습니다. 이 접근 방식은 알고리즘의 정확성이 응용 프로그램의 성공에 중요할 때 신뢰할 수 있는 소프트웨어를 개발하는 데 필수적입니다.

