# 3.1 ORM

> ORM : 관계 지향형 패러다임의 RDBMS 시스템, 객체 지향형 패러다임의 Python, Java간의 차이 때문에 복잡해질 수 있는 로직을 객체 지향적인 코드로 작성할 수 있도록 도와주는 기술

일반적으로 백엔드 프로그래밍의 흐름은 다음과 같다.

1. 클라이언트에서 요청이 들어온다.
2. 서버는 클라이언트의 요청에 해당하는 데이터를 가져오기 위해 SQL을 작성해서, 데이터를 가져온다.
3. 요청의 성공 여부를 클라이언트에게 전달하고, 다른 요청을 대기한다.

#### @dataclass

파이썬의 Class를 작성할 때, @dataclass를 작성하면 `def __init__()`, `__repr__` 같은 추가적으로 작성해야 하는 메서드를 구현하지 않아도 된다.

```python
from dataclasses import dataclass

@dataclass
class ExampleUser:
	id: int
    username: str
    password: str
```

만약 @dataclass 없이 위 객체를 사용하려면 __init__ 메소드를 만들어서 데이터를 객체에 넘겨주도록 해야하지만, @dataclass가 붙어있다면 자동으로 생성되어, 필요한 field 및 사용자 정의 method만 정의해주면 된다. **개발의 편의성이 향상된다**

## 3.1.1 지루하게 반복되는 코드

만약 ORM 기술을 사용하지 않고, SQL만으로 모든 Database 작업만을 처리하게 된다면, SQL문을 계속 바꿔가면서 요청을 처리하거나, 비슷비슷한 SQL문이 여러개 생기게 된다.

비슷한 코드가 여러 곳에서 나타난다면, 개발자가 소프트웨어를 유지보수 할 때 큰 어려움이 생기게 된다. 만약 반복되는 코드가 10개인줄 알고 10개만 고쳤는데, 실제로는 12개라면? 그 2개때문에 에러가 발생하게 된다.

## 3.1.2 개발자의 시간 및 자원 낭비

Python이나 Java같은 언어는 SQL문을 알지 못하므로 단지 String 형으로 파악하게 된다. 따라서 개발자가 무심코 SQL문을 작성했을 때 발생하는 오타, 문법 오류를 컴파일러, 개발자 둘다 잡지 못하게 된다. 이렇게 작성하다 보면 자잘한 실수가 생겨서 시간 및 자원 낭비가 발생한다.

## 3.1.3 RDBMS와 객체 지향형 언어 간의 이질성

- 파이썬에서는 모델간 상속 구조를 가지도록 설계가 가능하지만, 관계 지향형에서는 상속 개념이 존재하지 않는다.
- 관계 지향형 모델에서는 데이터 간 관계를 Foreign key를 통해 설정하면 되지만, 파이썬같은 객체 지향형에서는 직접 객체를 매핑해야한다.

예를 들면,
```python
from __future__ import annotations
# 식당 주인은 여러개의 음식점을 가질 수 있고, 음식점은 한명의 Owner를 가진다
class Restaurant:
	name: str
    owner: Owner

class Owner:
	name: str
    restaurants: List[Restaurant]
```

```python
CREATE TABLE owner (
    id SERIAL PRIMARY KEY NOT NULL,
    name VARCHAR(10) NOT NULL
);

CREATE TABLE restaurant (
    id SERIAL PRIMARY KEY NOT NULL,
    name VARCHAR(10) NOT NULL,
    owner_id INTEGER NOT NULL,
    CONSTRAINT fk_owner FOREIGN KEY (owner_id) REFERENCES owner(id)
);
```

이러한 패러다임 차이를 극복하는 소스코드를 직접 구현하려면 골치가 아프다. 따라서 ORM을 써서 이 문제를 해결해야 한다.

## 3.1.4 ORM의 필요성

SQL을 사용하게 되면, 쿼리 필터 조건만 약간씩 다르고 거의 비슷비슷한 코드가 쓰이게 된다.
많은 개발자가 위에서부터 계속 이야기한 내용에 대해 해결하기 위하여 ORM을 개발하게 된 것이다.

## 3.1.5 장고 ORM으로 지루한 코드 개선

장고 ORM을 사용하는 예시를 한번 들어보자.
```python
# settings.py
# Django가 데이터베이스에 접속하기 위해 필요한 정보를 설정해놓는다.

DATABASES = {
	'default' : {
    	'ENGINE': 'django.db.backend.postgresql', # postgre engine 사용
        'NAME': 'django_backend_programming_db', # 사용할 schema 이름
        'USER': 'postgres', # 접속 시 사용할 User
        'PASSWORD': '1234',
        'HOST': '127.0.0.1',
        'PORT': '5432'
    }
}
```

Django app 내부의 models.py에 사용할 model을 생성한다.
```python
from django.db import models

class MyUser(models.Model):
	# id는 models.Model을 상속받으면서 자동으로 생성된다.
	username = models.CharField(max_length=50)
    email = models.CharField(max_length=100)
```

위와 같이 모델이 구성되어 있을 때, user의 id가 1인 데이터를 가져오려면
```python
user = MyUser.objects.get(id=1)
```

user의 email이 asdf@asdf.com인 데이터를 가져오려면
```python
user = MyUser.objects.get(email="asdf@asdf.com")
```

이렇게 한줄만으로 쿼리를 사용할 수 있다. 

> MyUser 모델 class는 models.Model을 상속받게 되면서, 자동으로 `objects` 변수명도 상속받게 된다. 위에서 사용한것처럼 모델명.objects를 통해 get, filter, 등등.. 다양한 기능을 사용할 수 있게 된다.

# 3.2 쿼리셋

> Django에서의 QuerySet은 Django 개발에서 가장 많이 접하는 데이터셋중 하나이다.

Django 공식 문서를 확인해본적이 있다면, Queryset is evaluated라는 문구가 자주 등장하는데, 이는 `Queryset 내부에서 SQL 연산이 발생하였다`라는 의미로 이해하도록 하자.

## 3.2.1 쿼리셋이란?

> QuerySet: Model로 데이터베이스의 데이터를 제어할 수 있도록 해주는 객체

```python
User.objects.filter(username="엄준식")
```

이 코드의 리턴타입이 바로 QuerySet이다.

## 3.2.2 QuerySet과 List[Model]의 차이점

장고 ORM을 처음 배워서 사용을 하게 된다면,
```python
user_list = User.objects.all()
```
User.objects.all()이 반환되는 결과가 List[User]타입일 것이라고 착각을 하게 된다.

하지만, 실제로는 QuerySet[User]가 들어가있다.

즉, 데이터베이스에 SQL을 질의해서 user 리스트를 가져올 ***준비***가 된 쿼리셋을 반환한다.

따라서 정확한 리턴타입을 표시하자면, 다음과 같다.
```python
user_queryset: QuerySet[User] = User.objects.all()
```

쿼리셋에서 실제로 User 데이터 목록을 가져오는 시점은 다음과 같다.
```python
user_list = list(user_queryset) # 실제로 queryset에 담긴 sql이 실행된다.
```
이 동작은 지연로딩 방식으로도 말하는데, 추후 자세히 설명할테니 일단 넘어가자.

만약, 지연로딩 방식을 사용하고 싶지 않다면 아래처럼 사용해주면 된다.
```python
user_list = list(User.obejects.all())
```

## 3.2.3 쿼리셋의 특징

> QuerySet은 SQL과 동일한 것이 아니다!

QuerySet의 동작 과정에 대해 자세히 이해하기 위해 다음 예제를 살펴보도록 하자.

```python
def func(self):
	user_queryset: QuerySet[User] = User.objects.filter(username="asdf1234") # (1)
    
    if user_queryset.exists(): # (2)
    	user_queryset: QuerySet[User] = user_queryset.filter(first_name="준식") # (3)
        user_list: List[User] = list(user_queryset) # (4)
        user1: User = user_queryset[0] # (5)
```

위 코드에서 실제로 데이터베이스에 쿼리해오는 횟수가 얼마일까? 아래 글을 천ㅊ천히 읽으면서 답을 알아가보도록 하자.

### 쿼리셋은 정말 필요한 시점에 SQL 질의를 한다.

> (1)에서는 queryset만 생성할 뿐, 실제로 데이터베이스에 질의하지 않는다.

즉, QuerySet에 호출될 준비가 된 SQL문만 생성되어 대기중이다.
따라서 QuerySet에는 query문이 다음과 같이 구성된다.

```sql
query = "SELECT * FROM USER" + "WHERE username='asdf1234'" _result_cache = None
```

> (2)에서는 실제 값이 필요하므로 데이터베이스에 질의한다.

django ORM의 exists()함수는 쿼리셋에 존재하는 SQL에 대한 필터링 결과로, 해당 데이터가 존재하는지에 대한 **여부**만을 묻는 함수이다.

여기서 가장 중요한것은, 실제 데이터를 가져오지 않고, 필요한 부분인 **존재 여부**만을 SQL로 질의한다
```sql
SELECT 1 FROM USER WHERE username='asdf1234' limit 1;
```

**따라서 아직 쿼리셋에는 해당 필터에 대한 결과가 담겨있지 않다.**

### 쿼리셋은 SQL의 발생을 최대한 지연시킨다.

> 이를 LazyLoading 이라고 한다.

(3)을 보면, 여기서도 filter조건을 걸고있다. (1)과 마찬가지로, 필터링할 조건을 추가하여 SQL을 구성하는 것이지, 데이터를 아직 가져오라는 명령을 한것이 아니다. 따라서 QuerySet에 대기하고있는 SQL문은 다음과 같이 구성된다.

```sql
query = "SELECT * FROM USER" + "WHERE username='asdf1234'" AND "first_name = '준식'" _result_cache=None
```

> ***당장 필요한 데이터가 아니니까 최대한 SQL문의 실행을 지연시킨다***

---

이제 (4)에서는 더이상 SQL을 지연시키지 못한다. 왜? list()에 담기려면 반드시 데이터가 필요하기 때문이다. 따라서 QuerySet에 저장되어있던 query를 데이터베이스에 날려서 데이터를 진짜로 가져오게 된다.

위에 sql문을 보면 뒤에 `_result_cache`라는 부분을 볼 수 있는데, 바로 여기가 데이터베이스에서 가져온 데이터를 담아두는 **Cache**이다.

따라서 _result_cache는 다음과 같이 담긴다.
```python
_result_cache = [User(), User(), User(), ...]
```

### 쿼리셋은 캐싱된 데이터를 재활용해서 SQL 호출을 줄인다.

> (5)에서 수행되는것이 바로 캐싱된 데이터를 재활용하는 것이다.

`_result_cache`에 저장된 데이터를 가지고오게 된다.

만약 (5)가 (4)보다 앞에 있었다면? 당연히 SQL 질의를 보내서 데이터를 가져오게 될것이다.

## 3.2.4 SQL 발생 측면에서의 최적화

3.2.3절에서 주어진 문제의 해답은 `2`이었음을 잘 알 수 있었다. (2), (4)

위 예시를 살펴보면서, 쿼리셋은 다음과 같은 특징을 가지고있다고도 이해할 수 있다.

1. 쿼리셋은 진짜 필요한때에만 SQL 질의를 한다.
2. 쿼리셋은 Lazy Loading 방식을 사용한다.
3. 쿼리셋은 캐싱된 데이터를 재사용해서 SQL 질의 횟수를 최대한 줄인다.

> Django ORM의 속성 : LazyLoading, Caching, EagerLoading

LazyLoading과 Caching에 대해서는 앞 예제에서 소개했으므로 충분할 것이다. 하지만, EagerLoading은 무엇일까?

즉시 로딩 옵션은 `select_related()`, `prefetch_related()`함수가 제공한다. 이는 3.2.5절에서 소개할 내용이라고 한다.

### 쿼리셋 특징을 이용한 예제 코드 최적화

```python
def func(self):
	user_queryset: QuerySet = User.objects.filter(username="asdf1234")
    
    # 미리 땡겨와서 추후에 발생하는 쿼리를 안하도록 만들면 되지 않을까??
    user_list: List[User] = list(user_queryset)
    if usser_queryset.exists():
    	first_user_named_junsik = next((user for user in user_list if user.first_name == "준식"), None) # 쿼리셋이 받아온 리스트에 결과가 있다면 해당 결과를 넣고, 없다면 None을 넣는 next()함수를 사용
    user1: User = first_user_named_junsik # 데이터를 가져왔기 때문에 그냥 변수에 넣어주면 된다.
```

## 3.2.5 get(), filter(), first()

> User.objects.get():
- get()을 사용하면 즉시 SQL로 데이터베이스에 질의한다.
- 딱 하나의 데이터만 받을 수 있으므로 만약 조건에 해당하는 데이터가 2개 이상 존재하면 **MultipleObjectsReturned** Exception이 발생한다.

주로 PK, Unique 특성이 걸려있는 것에 대해 질의할 때 사용하는것이 좋다.
만약 get()을 사용했을 때 반드시 데이터가 1개라고 보장할 수 없다면, 예외처리를 해주어야 한다.

```python
user = None
try:
	user = User.objects.get(first_name="랄로")
except MultipleObjectsReturned as e:
	print("랄로가 2명 이상 있어요.", e)
    user = None
except User.DoesNotExist as e:
	print("랄로라는 사람은 없는데요?", e)
    user = None
```

위처럼 에러 처리를 해주면 되는데, 무조건 user=None으로 처리해서는 당연히 안되고, 비즈니스 로직에 따라 처리를 해주면 된다.

> User.objects.filter():
- filter()함수는 LazyLoading함수이다.
- 반환되는 값은 QuerySet이다.
- 나중에 데이터를 가져올 때 여러개의 값을 가져올 수 있다.

만약 데이터를 하나만 가져오는것처럼 사용하고 싶다면, 아래처럼 작성하게 될것이다.
```python
user: User = User.objects.filter(first_name="파카")[0]
```

그런데 만약 "파카"라는 데이터를 가진것이 없다면? 위 코드는 IndexError가 발생하게 된다.

따라서 다음과 같이 예외처리를 해주어야 한다.
```python
try:
	user: User = User.objects.filter(first_name="파카")[0]
except IndexError as e:
	print("파카라는 사람은 여기에 없어요", e)
    user = None
```

> User.objects.blabla....first():
- 이 함수는 filter()를 사용할 때 하나만 값을 받고싶을 때 주로 사용하게 된다. 위에 있던 예시처럼 복잡해지는 예외 처리 로직을 사용하기 싫다면, first()를 사용하자.

```python
user: User | None = User.objects.filter(first_name="파카").first()
```

만약 데이터를 가져온다면 정상적으로 User 타입의 데이터가 저장되고, 아니라면 None이 저장된다.

**하지만 이 방법도 문제가 존재하는데, 바로 에러가 안난다는 것이다.**

왜 에러가 안나는게 문제일까? 에러가 발생하지 않는다면, 추후 다른곳에서 에러가 터졌을 때 이 코드때문에 발생한 문제임에도, **개발자가 봤을때는 해당 코드에서 발생하는 문제인지 파악조차 못할수 있기 때문이다.**

따라서 무조건 에러가 발생하지 않는 코드가 좋은것은 아니므로 적절한 예외처리를 해주는게 바람직하다.

# 3.3 쿼리셋 활용하기

> 쿼리셋은 ORM을 사용하여 SQL의 다양한 문법을 사용할 수 있도록 다양한 함수를 지원한다.
참고 문서 : 
[Django documentation](https://docs.djangoproject.com/en/5.0/ref/models/querysets/#),
[Conditional Expressions](https://docs.djangoproject.com/en/5.0/ref/models/conditional-expressions/)
[Models Expressions](https://docs.djangoproject.com/en/5.0/ref/models/expressions/)

> 모든 함수를 적지 않고, 생소하고 이해가 필요한 부분만 따로 적었습니다.

### 참조 모델과 함께 검색
> {참조 모델명}__{필드명}={원하는 검색 키워드}

```python
product_queryset = Product.objects.filter(store__name__contains="설렁탕")
print(product_queryset.query) # SQL문 출력
```

`Product` 모델이 `외래 키(ForeignKey)`로 연결된 `store 모델`의 `name 필드`에 "설렁탕"이라는 문자열이 포함된 모든 제품들을 조회하는 조건

`store__name`: Product와 store가 외래 키로 연결되어 있을 때, Product에서 store의 name 필드를 사용하려면 이렇게 사용하면 된다.

### and, or, not

> And : https://docs.djangoproject.com/en/5.0/ref/models/querysets/#and
Or : https://docs.djangoproject.com/en/5.0/ref/models/querysets/#or
Xor: https://docs.djangoproject.com/en/5.0/ref/models/querysets/#xor
Not: https://docs.djangoproject.com/en/5.0/topics/db/queries/#complex-lookups-with-q-objects

### annotate()

![](https://velog.velcdn.com/images/calzone0404/post/b7637c93-434c-4182-add1-1a56eef7378f/image.png)

코드를 보면, 실제 store 모델에는 `store_name_slug`, `total_order_cnt`, `total_revenue`는 선언되어 있지 않은데, annotate()를 사용하여 임시 프로퍼티를 생성했기 때문에 코드와 같이 조회가 가능하다.

### QuerySet slicing은 LazyLoading 방식이다.

> User.objects.filter(first_name__contains="엄")[0:10] # 0번째부터 9번째까지 데이터 슬라이싱

슬라이싱을 해도, 나중에 값이 필요할 때 해당 범위의 값에 대해서만 SQL문을 사용하여 값을 가져오면 되기 때문이다.

### aggregate()

> 통계 관련 쿼리 함수이며, Dictionary를 반환한다. : https://docs.djangoproject.com/en/5.0/ref/models/querysets/#django.db.models.query.QuerySet.aggregate

통계 관련 함수만 사용할 때 사용하면 된다. 만약, 통계 관련 함수가 안에 들어있지 않다면 `TypeError`가 발생한다.

```python
store_aggregate = Store.objects.aggregate(
	total_order_cnt=Count("order"), # 각 Store 객체에 연결된 order 객체들의 개수를 계산하여 total_order_cnt라는 키로 딕셔너리에 저장
    total_revenue=Sum("order__total_price"), # order 객체들의 total_price 필드 값의 합계를 계산하여 total_revenue라는 키로 딕셔너리에 저장
)
```

결과 :
```python
{
    'total_order_cnt': <총 주문 수>,
    'total_revenue': <총 매출 합계>
}
```

### Subquery(), Exists()

> Subquery() : https://docs.djangoproject.com/en/5.0/ref/models/expressions/#subquery-expressions
메인 쿼리에서 서브쿼리를 작성할 때 도움을 주는 함수이다.

```python
from django.db.models import OuterRef, Subquery

newest = Comment.objects.filter(post=OuterRef("pk")).order_by("-created_at") 
# 특정 포스트(post id -> pk)에 속하는 댓글(Comment) 중 최신 댓글을 조회

Post.objects.annotate(newest_commenter_email=Subquery(newest.values("email")[:1])) 
# 각 포스트(Post)에 대해 최신 댓글 작성자의 이메일을 추가
# annotate를 사용하여 임시 프로퍼티인 newest_commenter_email을 선언하였다.
```

- `OuterRef`: 서브쿼리 내부에서 외부 쿼리의 필드를 참조할 수 있도록 도와주는 함수. 위 코드에서는 Comment의 서브쿼리 내부에서 Post (외부 쿼리 필드)를 참조하도록 돕고 있다.

> 쿼리셋 2개(Comment, Post)를 작성하고, 2개의 쿼리셋을 연결해줄 Column을 선언(OuterRef)해서 Subquery()함수로 감싸주면 `Scalar Subquery`가 된다.

- `Scalar Subquery` : 메인 쿼리의 각 Row(Post)에 대해 서브쿼리(Comment)는 ***최대 1개의 Row만 반환***해야 하며, 이 Row는 ***1개의 Column만 포함***해야 한다.

```python
newest_commenter_email=Subquery(newest.values("email")[:1])
```
이 코드가 Scalar subquery를 잘 설명하고 있는데, `최신 댓글`의 `이메일 주소` 1개만 반환하므로 조건을 만족한다.

> Exists() :
https://docs.djangoproject.com/en/5.0/ref/models/expressions/#exists-subqueries
존재 여부를 나타내는 Boolean 타입만 가질 수 있다.

```python
from django.db.models import Exists, OuterRef
from datetime import timedelta
from django.utils import timezone

one_day_ago = timezone.now() - timedelta(days=1)
recent_comments = Comment.objects.filter(
	post=OuterRef("pk"),
	created_at__gte=one_day_ago,
)
Post.objects.annotate(recent_comment=Exists(recent_comments))
```

### Inline View (From 절에 들어가는 Subquery)

> Django ORM은 지원하지 않는 기능이다.

### Nested Subquery (Where절에 들어가는 Subquery)

> Scalar Subquery와 사용 방법이 동일하고, 사용하는 곳이 다를 뿐이다. Scalar 방식은 annotate() 메서드 안에서 사용하지만, **Nested Subquery는 filter() 메서드 안에서 사용한다.**

```python
from datetime import timedelta
from django.utils import timezone

yesterday = timezone.now() - timedelta(days=1)
post_subqueryset = Post.objects.filter(published_at__gte=yesterday)

Comments.objects.filter(post__in=Subquery(post_subqueryset.values("pk")))
```

- 당연히 pk는 Post의 id이다. 즉, 어제 생성된 포스트에 달린 댓글 목록을 조회하는 쿼리셋이다.

```python
yesterday = timezone.now() - timedelta(days=1)
yesterday_comment_subqueryset = Comment.objects.filter(
	post=OuterRef("pk"),
    published_at__gte=yesterday,
)

Post.objects.filter(Exists(yesterday_comment_subqueryset))
```

- 어제 생성된 Post중 댓글이 하나라도 존재하는 게시물 목록을 조회하는 쿼리셋

### SQL 직접 사용하는 raw()

> https://docs.djangoproject.com/en/5.0/topics/db/sql/#mapping-query-fields-to-model-fields

# 3.4 지연 로딩과 즉시 로딩

아래와 같은 모델을 정의했다고 해보자.
```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

## 3.4.1 지연 로딩 (Lazy Loading)

지연 로딩은 데이터베이스에게 질의하는 질의문을 바로 보내는것이 아니라, 진짜 필요한 순간에 질의문을 전송해서 데이터를 가져오는 방식을 말한다.

예를 들어 모든 Book 데이터를 가져오는 코드를 작성했다고 해보자.
```python
query_set = Book.objects.all()
# SQL문만 작성된 상태고 아직 데이터베이스에게 질의하지 않은 상태 (지연 로딩)

first_book = query_set[0]
# 이걸 수행하려면 진짜 데이터가 필요하니까 쿼리셋에 담겨있는 SQL문을 데이터베이스에게 날려서 실제 데이터를 가져오게 된다.
```

### The N+1 Query Problem

> N개의 데이터를 가져오기 위해 1개의 쿼리를 수행했는데, 지연 로딩 정책 때문에 N번의 쿼리가 추가적으로 발생하는 문제

---

#### 정방향 참조 모델, 역방향 참조 모델
먼저 이를 이해하기 위해서는 정방향 참조 모델과 역방향 참조 모델에 대해서 이해해야 한다.

위 모델에서,
Author : 1명의 저자는 N개의 Book을 써낼 수 있다.
Book : N개의 Book은 1명의 저자를 가질 수 있다.

즉, Author와 Book은 1:N관계이며(Author의 역방향 참조모델은 Book), Book과 Author는 N:1 관계이다(Book의 정방향 참조 모델은 Author).

**처음 보면 이해가 잘 가지 않는데, 쉽게 말해서**
- **내가 가진 필드에서 Foreignkey를 타고 다른 모델을 참조할 수 있다면 `정방향 참조 모델`**
- **내가 가진 필드에는 Foreignkey가 없지만 book_set.all()과 같이 참조할 수 있다면 `역방향 참조 모델`**

이라고 생각하면 된다.

---

N+1 문제는 정방향 참조 시 발생하는 문제인데, 예를 들어
```python
books = Book.objects.all()
for book in books:
    print(book.author.name)
```

위 코드를 분석해보면, 
- 첫번째 줄의 `Book.objects.all()`는 지연 로딩이 걸려 있다.
- 두번째 줄에서 for loop을 돌려야 하므로 쿼리문이 데이터베이스에 전달되어 데이터를 가져온다.
- for loop을 돌때마다 book에서 참조하는 author 모델에 대한 정보를 가지고 있지 않으므로 author model 데이터를 가져오는 쿼리가 수행된다. (즉, for loop 마다 수행된다.)

**따라서 1개의 쿼리로 book 데이터를 가져왔지만 N회의 추가적인 쿼리가 발생하게 되는 문제가 발생한다**

이게 바로 N+1 Problem이다.

---

## 3.4.2 즉시 로딩 (Eager Loading)

> 즉시 로딩 동작은 메인 객체와 관련되어 있는 객체들을 하나의 쿼리를 통해 바로 가져올 수 있도록 하는 방식이다.

위에서 봤던 것처럼 지연 로딩이 걸린 쿼리셋에 정방향 참조를 하는 코드가 있다면, N+1 문제가 발생하는데, 이걸 해결하는 방법이 바로 즉시 로딩 방법이다.

### select_related()

> JOIN을 사용한 정방향 참조 모델 즉시 로딩

SQL의 INNER JOIN을 사용해서 한 번에 가져오는 것으로 이해하면 된다.

```python
books = Book.objects.select_related('author').filter(title__contains="무슨책")
# "무슨책"에 해당하는 책을 가져올 때 author 정보도 같이 가져온다.
```

위에서 이야기한 N+1 문제를 이렇게 해결해주면 된다.

```python
books = Book.objects.select_related('author').all()
for book in books:
    print(book.author.name)
```

만약 정방향 참조하는 모델에 여러 개의 `ForeignKey`가 있다면 `select_related()`에 여러 개의 필드를 넣어주면 된다.

```python
books = Book.objects.select_related('author', 'publisher').all()
# ForeignKey가 걸린 책 저자와 출판사 정보도 가져온다.
```

이렇게 작성한다면 실제 SQL 쿼리문에는 INNER JOIN이 총 2개 걸리게 된다.

### prefetch_related()

> 추가 쿼리셋 역방향 참조 모델 즉시 로딩

이 함수는 `다대다 관계`에서 사용하거나, 역방향 참조 모델을 즉시 로딩할 때 사용하는 함수이다.

```python
authors = Author.objects.prefetch_related('book_set').all()
# 역방향 참조 즉시 로딩
```
이와 같이 작성한다면 하나의 쿼리에 모든 author 정보를 가져오면서 해당 author가 집필한 모든 책 정보도 함께 가져오게 된다.

`select_related()` 함수와는 달리 1개의 쿼리만 발생하는 것이 아닌 1~N개의 추가적인 쿼리가 발생한다. author에서 바로 참조할 수가 없기 때문에 `INNER JOIN`같은 JOIN 문법을 사용하지 않는다.

따라서 book 정보에 대한 쿼리가 추가적으로 필요하므로 위 예제에서는 총 2회(author 정보 select + book 정보 select)의 쿼리가 발생한다.

`book_set`뿐만 아니라 다른 역방향 참조 관계가 여러 개 있을 때는 `prefetch_related()` 함수에 여러 개 인자를 담아서 넣어주면 되고, 인자의 개수만큼 쿼리 횟수가 늘어난다.

```python
authors = Author.objects.prefetch_related('book_set', 'article_set').all()
# Author 모델이 book_set과 article_set을 모두 역방향 참조
```

위 예제에서는 총 3회의 쿼리가 발생한다. 첫 번째 쿼리는 모든 `author` 정보를 가져오고, 두 번째와 세 번째 쿼리는 각각 `book_set`과 `article_set`에 대한 정보를 가져온다.

### Prefetch()

> `prefetch_related()`에 조건을 걸고 싶다면? 내부에 이걸 사용한다.

`prefetch_related()`내부에서 실행되므로 작동방식은 동일하다. 단지 조건을 걸고 싶을 때 내부에 사용하면 된다.

예시는 다음과 같다.

```python
from django.db.models import Prefetch

# 조건이 걸린 Prefetch 예제
active_authors_with_books = Author.objects.prefetch_related(
    Prefetch('book_set', queryset=Book.objects.filter(is_published=True))
).all()
```

위 예제에서는 `Prefetch` 객체를 사용하여 `book_set`에 대해 추가적인 조건을 걸었다. `is_published=True`인 책들만 미리 가져오도록 하는 조건이다. 이렇게 하면 현재 활동하고 있는 저자와 해당하는 저자들이 출판한 책들만 가져올 수 있다.

또 다른 예시로 `Prefetch`를 사용해서 좀 더 세밀한 쿼리 최적화를 할 수 있다. 예를 들어, 여러 조건을 걸어서 각기 다른 데이터를 효율적으로 가져올 수 있다.

```python
# 여러 조건이 걸린 Prefetch 예제
authors_with_filtered_books_and_articles = Author.objects.prefetch_related(
    Prefetch('book_set', queryset=Book.objects.filter(is_published=True)),
    Prefetch('article_set', queryset=Article.objects.filter(is_featured=True))
).all()
```

위 예제에서는 `book_set`과 `article_set`에 각각 조건을 걸었다. `book_set`에는 `is_published=True` 조건을, `article_set`에는 `is_featured=True` 조건을 걸어서, 출판된 책과 주목받는 기사만 가져오도록 했다. 이렇게 하면 총 3개의 쿼리가 실행된다: 첫 번째 쿼리는 모든 저자 정보를 가져오고, 두 번째와 세 번째 쿼리는 각각 출판된 책과 주목받는 기사에 대한 정보를 가져온다.

`Prefetch`를 활용하면 다양한 조건을 걸어 복잡한 데이터 요구사항을 효율적으로 처리할 수 있다. 이를 통해 쿼리 성능을 최적화하고, 필요하지 않은 데이터를 불러오는 것을 방지할 수 있다.

## 3.4.4 쿼리셋의 구조

> 3.4.3은 위에서 설명했으므로 생략함

```python
class QuerySet:
    query = Query()  # 메인 쿼리가 담기는 인스턴스
    
    _prefetch_related_lookups = ()  # N개의 추가 쿼리셋으로 조회할 대상을 저장하는 튜플
    _result_cache = []  # 메인 쿼리 + _prefetch_related_lookups에서 가져온 실제 데이터를 저장하는 캐시
    
    _iterable_class = ModelIterable  # for loop 같은 반복문이 가능하도록 설정하는 변수
```

예를 들어 다음과 같이 쿼리셋을 작성했다고 해보자.

```python
query_set = Book.objects \
                .select_related('author') \
                .filter(name__contains="엄준식") \
                .prefetch_related('publisher', 'review_set')
```

이 쿼리셋을 한번 실행시키면, 실제 쿼리셋에는 이렇게 담긴다.

```python
class QuerySet:
    query = Query()  # 메인 쿼리문 (author inner join 추가됨)
    
    _prefetch_related_lookups = ('publisher', 'review_set')
    _result_cache = [Book(1), Book(2), ... ]
    
    _iterable_class = ModelIterable
```

`_result_cache`에 내용이 채워지면 동일한 질의에 대해서는 SQL문을 질의하지 않고 캐시에서 가져오게 된다.

### 쿼리셋이 생성하는 SQL 구조

위 예시처럼 쿼리셋을 작성하면 아래처럼 SQL이 발생한다.

```sql
SELECT "app_book"."id", "app_book"."name", "app_book"."author_id", "app_author"."id", "app_author"."name"
FROM "app_book"
INNER JOIN "app_author" ON ("app_book"."author_id" = "app_author"."id")
WHERE "app_book"."name" LIKE '%엄준식%';

SELECT "app_publisher"."id", "app_publisher"."name", "app_book_publishers"."book_id"
FROM "app_publisher"
INNER JOIN "app_book_publishers" ON ("app_publisher"."id" = "app_book_publishers"."publisher_id")
WHERE "app_book_publishers"."book_id" IN (1, 2, ...);

SELECT "app_review"."id", "app_review"."content", "app_review"."book_id"
FROM "app_review"
WHERE "app_review"."book_id" IN (1, 2, ...);
```

거의 대부분의 쿼리셋이 만드는 SQL문은 아래 코드의 구조를 따르게 된다. 따라서 내가 만든 쿼리셋이 아래 코드 구조를 벗어나지 않았는지 항상 확인해주어야 한다.

```python
# 일반적인 쿼리셋 구조
query_set = (
    Book.objects
    .select_related('author')  # INNER JOIN을 사용하여 연관된 author를 가져옴
    .filter(name__contains="엄준식")  # 필터링 조건을 추가
    .prefetch_related('publisher', 'review_set')  # 추가 쿼리로 연관된 publisher와 review를 가져옴
)
```

이렇게 작성된 쿼리셋이 실제로 실행되는 SQL문을 잘 이해하고 확인하면, 예상치 못한 성능 문제를 방지할 수 있다.

Django ORM은 직관적이지만, 잘못 사용하면 성능 저하를 초래할 수 있으므로 항상 쿼리셋의 구조와 생성된 SQL문을 확인하는 습관을 들이자.

### len(queryset) vs queryset.count()

> Django에서는 후자의 방법을 사용하라고 권하고 있다.

`len()`보다 `count()`를 써야 하는 이유는 무엇일까?

#### len() 함수

- `len()` 함수는 메모리에 모든 로드된 객체를 저장하게 된다. 만약 데이터셋이 엄청 크다면? 
  - 매우 비효율적이다. 이는 데이터베이스로부터 모든 데이터를 가져와 메모리에 저장한 후 그 길이를 계산하기 때문이다.
  - 예를 들어, 10,000개의 레코드가 있는 테이블에서 `len(queryset)`을 호출하면, 10,000개의 레코드를 모두 메모리에 로드한 후 길이를 계산하게 된다. 이는 엄청난 메모리 사용과 성능 저하를 초래할 수 있다.

#### count() 함수

- `count()` 함수는 SQL에서 제공하는 `COUNT()`를 사용하는 것이므로 데이터베이스 자체에서 처리해주기 때문에 매우 효율적이다.
  - 데이터베이스에서 직접 카운트 쿼리를 실행하여 결과를 반환하므로, 데이터를 메모리에 로드할 필요가 없다.
  - 예를 들어, 10,000개의 레코드가 있는 테이블에서 `queryset.count()`를 호출하면, 데이터베이스는 단일 `COUNT(*)` 쿼리를 실행하여 레코드 수를 반환한다. 이는 메모리 사용량을 최소화하고, 훨씬 더 빠르게 결과를 얻을 수 있다.

다음은 두 방법의 차이를 보여주는 예시이다.

```python
# 비효율적인 방법: len() 사용
books = Book.objects.all()
book_count = len(books)  # 모든 책 객체를 메모리에 로드한 후 길이를 계산
```

```python
# 효율적인 방법: count() 사용
book_count = Book.objects.count()  # 데이터베이스에서 직접 COUNT 쿼리를 실행하여 레코드 수를 반환
```

#### 예시

```python
# len()을 사용한 비효율적인 예시
books = Book.objects.all()
print(f"Number of books: {len(books)}")
# 이 코드가 실행되면 Book 테이블의 모든 레코드가 메모리에 로드된 후 개수를 계산한다.

# count()를 사용한 효율적인 예시
book_count = Book.objects.count()
print(f"Number of books: {book_count}")
# 이 코드는 데이터베이스에서 COUNT(*) 쿼리를 실행하여 바로 개수를 반환한다.
```

#### 결론

- `len(queryset)`은 데이터베이스에서 모든 데이터를 메모리에 로드한 후 길이를 계산하므로, 큰 데이터셋에서는 비효율적이다.
- `queryset.count()`는 데이터베이스에서 직접 `COUNT(*)` 쿼리를 실행하여 개수를 반환하므로, 훨씬 더 효율적이다.

따라서, Django에서는 큰 데이터셋의 경우 `len()` 대신 `count()`를 사용하는 것을 권장한다. 이는 메모리 사용량을 줄이고 성능을 향상시키는 데 도움이 된다.

# 3.5 트랜잭션 관리
# 3.6 매니저
# 3.7 관계 매니저
# 3.8 DB 라우터: 멀티 데이터베이스 관리
