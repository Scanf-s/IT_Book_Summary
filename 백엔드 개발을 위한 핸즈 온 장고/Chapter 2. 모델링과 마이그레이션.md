# 2.1 ER 모델링

> Entitiy Relation : 관계형 데이터베이스인 데이터베이스에서 구현될 수 있는 데이터 또는 정보 구조를 정의하는 추상적인 데이터 모델

백엔드 개발 프로젝트를 시작할 때 가장 먼저 하게 되는 작업으로, 데이터베이스 테이블 정의 및 관계를 어떻게 매핑할 지 결정해야 한다.

백엔드 개발자는 **ER모델링** 단계에서 기획자가 준 기획서 또는 고객의 요구사항을 반영하는 모델을 설계해야한다.
만약, 적절하지 못한 기획서 또는 백엔드 개발자의 잘못된 기획내용 파악으로 인해 올바르지 못한 ER 모델링이 이루어진다면, 개발 진도에 차질이 생기게 된다.

이 책에서는 ER 모델링을 어떻게 효율적으로 정확하게 해야할지보다는, ER 모델링된것을 Django로 어떻게 표현할 지 알려주는데 초점이 맞추어져 있다.

# 2.2 장고의 모델

## 2.2.1 모델과 필드를 사용해야 하는 이유

Django는 실제 Database 모델링과 테이블 생성까지 한번에 해준다.
Django 자체적으로 Model 객체와 Field 클래스를 지원하면서, 실제 데이터베이스 테이블을 어떻게 정의하고, 어떤 Column에는 어떤 Type으로 정할지와 Column의 세부 Attribute까지 적용할 수 있다.

```python
from django.db import models

class IamDjangoModel(models.Model): # 미리 Djangod에서 짜놓은 Model을 상속받아서 원하는 데이터베이스 테이블 구현
	str_attr = models.CharField() # 미리 Django에서 정해놓은 타입별 Field를 사용해서 원하는 데이터타입의 Column 생성
```

> 위와 같이 작성하면, 파이썬으로 작성해야할 작업과 SQL로 작성해야 할 작업을 한번에 해결할 수 있다.


## 2.2.2 다양한 옵션

장고는 Model을 커스터마이징할 수 있는 다양한 옵션을 지원한다. `Meta`라는 Inner class를 만들어서 Django model을 제어할 수 있다.

```python
from django.db import models
from django.core import validators

class Student(models.Model):
	name = models.CharField(max_length=64)
    phone = models.CharField(
    	validators=[validators.RegexValidator(regex=r"\d{2,3}-\d{3,4}-\d{4}")]
    )
    
    class Meta:
    	abstract = False
        managed = False
        proxy = False
        
        db_table = "My custom database table name"
```

### Meta를 사용하여 커스터마이징 할 때 사용하는 속성들

#### 1. abstract

> abstract는 Java의 추상클래스와 비슷하게 동작한다고 생각하자.

만약 abstract = True라면, 해당 모델을 추상 모델로 취급해서 **데이터마이그레이션 수행 시, 제외된다.**

즉, 데이터베이스 모델이 테이블로 반영되지 않는다는 뜻이다.

```python
class Human(models.Model):
	name = models.CharField(max_length=32)
    age = moodles.IntegerField()
    
    class Meta:
    	abstract = True
        # True인경우, db_table을 지정할 수 없다.
        
class Man(Human):
	class Meta:
    	db_table = "man"

class Woman(Human):
	class Meta:
    	db_table = "woman"
       
```
 
 위 처럼, Human 모델 객체를 추상 모델 객체로 선언하면, 해당 객체를 구체화하는 객체 Man, Woman을 구현해서 데이터 마이그레이션 시 반영하는 것이다. 
 
> 따라서 Man과 Woman에는 자동적으로 Human의 속성, models.Model의 속성들이 전부 들어가져 있다.

> 참고로 abstract의 기본값은 False이므로, 굳이 abstract=False라고 명시하지 않아도 된다.

#### 2. managed

> managed=True이면, 데이터베이스 마이그레이션에서 아예 제외한다는 뜻이다.

만약 managed=True이면, Django에서 관리하지 않고 따로 데이터베이스 모델을 관리하겠다라는 뜻이다.

다음과 같은 상황에서 managed를 사용한다.

- 이미 생성된 데이터베이스 테이블에 ORM을 적용할 때
- Django가 특정 테이블을 수정하는 것을 방지하고 싶을 때
- 특정 테이블이 아닌 SELECT 쿼리를 모델에 매핑할 때 (View table같은거)

> managed의 기본값은 False이다. 따라서 굳이 명시하지 않아도 된다.

#### 3. proxy

> 하나의 테이블을 2개 이상의 모델로 나눠서 표현하고 싶을 때 사용한다.

> proxy의 기본값은 False이므로 명시하지 않아도 된다.

```python
class Product(models.Model):
    class ProductType(models.TextChoices):
        # https://docs.djangoproject.com/en/5.0/ref/models/fields/#enumeration-types
        GROCERY = "grocery", "식료품"
        FURNITURE = "furniture", "가구"
        BOOKS = "books", "책"

    name = models.CharField(max_length=128, help_text="Product Name")
    price = models.IntegerField(help_text="Product Price")
    created_at = models.DateTimeField(auto_now_add=True)
    product_type = models.CharField(choices=ProductType.choices, max_length=32)


class GroceryProductManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(product_type=Product.ProductType.GROCERY)
    

class GroceryProduct(Product):
    # Proxy 모델은 이런 식으로 구현이 완료된 Model을 상속받아야 한다
    objects = GroceryProductManager()
    
    class Meta:
        # 이 테이블에서는 GroceryProduct에 대해서만 취급
        proxy = True
        

class FurnitureProductManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(product_type=Product.ProductType.FURNITURE)
    
    
class FurnitureProduct(Product):
    
    objects = FurnitureProductManager()
    
    class Meta:
        # 이 테이블에서는 FurnitureProduct에 대해서만 취급
        proxy = True
        
    # 또한 자식 클래스 상속과 동일하게 작동하므로,
    # 여기에 is_heavy()같은 대형 가구인지 판별하는 메서드도 작성 가능
```

이렇게 Proxy를 사용하는 이유는, Product Model이 Product Type마다 사용하는 메서드가 다르기 때문에 상속해서 새로운 메서드를 생성하는것 처럼 사용하기 위해서이다.


#### 4. db_table

> 데이터베이스 마이그레이션 시 생성되는 테이블명을 정해주는 속성이다.

기본적으로 db_table을 작성해놓지 않았다면, Django가 알아서 정해주게 되는데, 형식은 다음과 같다.

``장고 앱 이름 + 장고 모델 이름``

예를 들어서, Product Model에 db_table이 선언되어 있지 않고, 앱 이름이 shopping_mall이라면, 테이블 명은 다음과 같다.

``shopping_mall_product``

db_table을 설정하려면 다음과 같이 설정해주면 된다.


```python
class Meta:
	db_table = "원하는테이블명"
```

이렇게 db_table을 사용해서 테이블명을 지정해주는것이 실무에서 사용하는 방법이라고 한다.

#### 5. db_table_comment

> SQL의 Comment (데이터베이스 테이블을 설명하는 주석)를 Django ORM을 통해서 사용할 수 있도록 돕는 속성이다.

```python
class Meta:
	db_table_comment = "이 테이블은 어쩌고저쩌고..."
```

model에 대한 Comment뿐만이 아니라, Field에 대한 Comment역할을 수행하는 `db_comment`인자와 `help_text`인자가 존재한다.

둘의 차이점은 `help_text`는 Django에서만 볼 수 있는 주석이고, `db_comment`는 실제 Database에서도 볼 수 있는 주석이다.

따라서 만약 DB관리를 백엔드가 혼자서 전부 관리한다면 help_text를 사용해도 무방하지만, 팀프로젝트를 한다면 DBA나 데이터 분석가가 있을 수 있기 때문에 `db_comment`를 사용하는것을 권장한다.

#### 6. get_latest_by

> 장고 Queryset 호출 시, 사용되는 옵션이다. 즉, Queryset의 latest()를 호출할 때 가장 최근값의 기준을 어떤 필드로 사용할 것인지 설정하는 옵션이다.

```python
class Posting(models.Model):
    owner_name = models.CharField(max_length=128, help_text="Name of posting owner")
    contents = models.CharField(max_length=32, help_text="Contents of posting")
    
    created_at = models.DateTimeField(auto_now_add=True, help_text="Creation time")
    modified_at = models.DateTimeField(auto_now=True, help_text="Last modified time")
    
    class Meta:
        db_table = "posting"
        get_latest_by = ("modified_at", "created_at")
        # modified_at 값이 가장 최신인 것이 가장 최신 Posting임.
        # 만약 modified_at의 값이 같다면, created_at 값을 기준으로 함.
```

위와 같이 정의되어 있을 때, latest()를 호출하면 modified_at 값이 가장 최신인 Row를 가져온다
```python
Posting.objects.latest()
# 결과로 가장 최근 Row를 가져온다
```

만약 latest()에 다른 column값을 인자로 전달하면, get_latest_by가 설정한 값에 override되어 전달한 인자를 기준으로 쿼리해온다.

> get_latest_by는 기본값으로 primary key로 설정된 값을 가져온다.

#### 7. ordering

> Django ORM을 이용하여 데이터 조회 시 정렬 방법을 설정할 때 사용한다. 기본값으로 primary key (id)값으로 설정되어 있다.

#### 8. indexes

> Index : 특정 Column을 조건절로 사용할 때 SQL의 조회성능을 훨씬 더 빠르게 해주는 데이터베이스 기능이다.

```python
class Product(models.Model):
    class ProductType(models.TextChoices):
        # https://docs.djangoproject.com/en/5.0/ref/models/fields/#enumeration-types
        GROCERY = "grocery", "식료품"
        FURNITURE = "furniture", "가구"
        BOOKS = "books", "책"

    name = models.CharField(max_length=128, help_text="Product Name")
    price = models.IntegerField(help_text="Product Price")
    created_at = models.DateTimeField(auto_now_add=True)
    product_type = models.CharField(choices=ProductType.choices, max_length=32)

    class Meta:
        indexes = (
            models.Index(fields=["created_at"], name="created_at_index"),
            models.Index(fields=["name", "product_name"], name="name_pt_composite_index"),
            
            # postgredb 사용하는 경우 hashindex 사용
            # from django.contrib.postgres.indexes import HashIndex
            HashIndex(fields=["name"], name="name_hash_index"),
        )
```

Field 자체에 `db_index=True`로 인덱스를 설정할수도 있는데, 이렇게 하면 팀프로젝트 시 인덱스 관련 정보를 놓칠수 있기 때문에 Meta에 따로 인덱스 관련 정보를 모아서 관리해야 한다

#### 9. constraints

> 데이터베이스 수준에서 어떠한 조건을 제한하는 옵션. indexes처럼 DDL로 생성해서 반영된다.

```python
class Product(models.Model):
    class ProductType(models.TextChoices):
        # https://docs.djangoproject.com/en/5.0/ref/models/fields/#enumeration-types
        GROCERY = "grocery", "식료품"
        FURNITURE = "furniture", "가구"
        BOOKS = "books", "책"

    name = models.CharField(max_length=128, help_text="Product Name")
    price = models.IntegerField(help_text="Product Price")
    created_at = models.DateTimeField(auto_now_add=True)
    product_type = models.CharField(choices=ProductType.choices, max_length=32)

    class Meta:
        constraints = (
            models.CheckConstraint( # price는 무조건 0보다 크거나 같아야한다.
                check=models.Q(price__gte=0),
                name="price_gte_0"
            ),
            models.UniqueConstraint( # name과 product_type은 Unique해야 한다.
                fields=["name", "product_type"],
                name="unique_name_product_type"
            )
        )
```

마찬가지로 Field에 UniqueConstraint()를 `unique=True`로 표현할 수 있는데, 같은 이유로 따로 빼서 자세하게 작성해주는게 좋다.

# 2.3 장고의 필드

이 챕터는 모델링할 때 자주 쓰이는 데이터 필드의 타입들에 대해서 소개하는 챕터이다. 책에서도 굳이 이걸 다 외워서 사용하는게 아니라, 필요할때마다 찾아보면서 학습하라고 나와있다.

## 2.3.1 Primary Key 관련 필드

Django 모델이 생성될 때 반드시 PK를 가지도록 설계된다. 개발자가 따로 PK를 지정해주지 않는다면 알아서 PK=True를 가지는 필드 (ID)를 생성해준다.

### 1. AutoField

장고가 알아서 생성해주는 필드이다. 굳이 작성하지 않아도 된다.
```python
class DjangoModel(model.Model):
	id = models.AutoField(
    	auto_created=True,
        primary_key=True,
        serialize=False,
        verbose_name="ID"
    )
```

### 2. BigAutoField

AutoField는 기본적으로 int 타입이 가질수 있는 최대범위까지만 id를 생성할 수 있는데, **Django 5.x버전 이상부터, 자동으로 BigAutoField**로 선언된다.

BigAutoField는 900경? 이상까지 id값을 생성할 수 있다.

## 2.3.2 문자열 자료형 관련 필드

### 1. CharField

> SQL의 varchar형을 지원해준다.

```python
class DjangoModel(models.Model):
    str_field = models.CharField(
        help_text="주석을 대체하는 Argument. DB에는 영향을 주지 않음",
        db_comment="실제 DB에 반영되는 주석",

        blank=True,  # 빈 문자열을 저장하는것을 허용한다는 의미
        max_length=127,
        null=False,  # Nullable?
        primary_key=False,
        unique=False,
        db_index=False,
        default="기본값 설정",
        
        # Model의 Field명과 db Table의 Column명을 다르게 설정하고 싶다면 사용
        db_column="NEW DB COLUMN NAME",
        validators=(), # 해당 필드의 값이 유효성 검증이 필요할 때 사용
        error_messages={
            # 해당 필드에 이상한 값이 들어온 경우 메세지 처리
            "invalid_choice": "유효한 choice가 아닙니다",
            "null": "null이 들어오면 안됩니다",
            "blank": "blank이면 안됩니다",
        },
    )
```

### 2. TextField

> SQL의 LONGTEXT (4GB), POSTGRE의 TEXT (1GB)를 지원해준다.

```python
class DjangoModel(models.Model):
	text_field = models.TextField()
```

### 3. TextChoices

> Django 문자열 타입 Enum을 지원한다.


Django에서 Enum을 사용하고 싶을 때, 파이썬 자체에서 지원하는 Enum을 사용하기 보다는 이걸 사용하는게 좋다.

사용 방법은 **"실제 db에 들어가는 값", "Label(사람이 읽기 쉬운 값)"**으로 사용하면 된다.

```python
class Product(models.Model):
    class ProductType(models.TextChoices):
        # https://docs.djangoproject.com/en/5.0/ref/models/fields/#enumeration-types
        GROCERY = "grocery", "식료품"
        FURNITURE = "furniture", "가구"
        BOOKS = "books", "책"

    str_choices_field = models.CharField(choices=ProductType.choices, max_length=32)
```

## 2.3.3 숫자 관련 필드

### 1. IntegerField
### 2. FloatField
### 3. DecimalField
```python
decimal_field = models.DecimalField(
	max_digits=5, # 최대 자릿수
    decimal_places=2, # 5자리중, 2자리는 소수점
)
```
### 4. IntegerChoices

> TextChoices와 동일하게 사용하면 된다.

## 2.3.4 날짜 자료형 관련 필드

> UTC : 국제 표준 시간
> TIME_ZONE : client가 있는 지역의 기준 시간

django server에게 client가 자신의 현재 시각을 저장해달라는 요청을 보냈을 때, client 지역의 시간을 저장하는 것이 아니라 UTC값을 저장한다.

나중에 다시 Client가 저장된 시간 데이터를 가져오려고 할 때, Django에서 자동으로 client 지역의 시간에 맞추어 시간을 변환한 후, 전달하게 된다. 이때 사용하는 옵션이 TIME_ZONE이다.

---

Django는 `config/settings.py`에서 UTC와 TIME_ZONE 옵션을 설정할 수 있다.

```python
TIMEZONE = 'Asia/Seoul' # 아시아의 서울 지역의 시간으로 설정

USE_TZ = True # UTC 표준 시간을 사용해서 TIME_ZONE 값으로 시간을 변환하는 방식을 사용하겠다.
```

django에서 현재 시간을 계산할때에는 python 내장 모듈의 datetime을 사용하는게 아니라, **timezone**객체를 사용해야 한다. 위에 있는 코드처럼 설정하고, python의 datetime모듈을 설정하면 경고 메세지가 출력된다.

```python
from datetime import datetime, date, time
from django.utils import timezone

# datetime 대신 timezone 사용
# datetime.now
timezone.now()

# date.now()
timezone.localdate()

# datetime.now().time()
timezone.localtime()

# 만약 DateTimeField에 datetime 모듈을 사용하면 에러가 발생한다.
# q.datetime_field = datetime(1234, 1, 3) --> 오류 발생
# 제대로 사용하려면 아래와 같이 사용하자
q.datetime_field = timezone.now()
```

### 1. DateTimeField

> - **auto_now=True** # 해당 모델이 save()될 때 마다 최신 날짜 값으로 갱신해준다. (updated_at)
> - **auto_now_add=True** # 해당 모델이 최초로 create()될 때 최신 날짜 값을 채워준다. (created_at)
> 위 두가지 옵션은 같이 선언되지 못하고, 하나만 써야한다.

### 2. DateField

> 년, 월, 일값만 저장하는 필드이다.

### 3. TimeField

> 시, 분, 초값만 저장하는 필드이다.

### 4. DurationField

> 시간 차이를 저장한다.

## 2.3.5 파일 업로드 관련 필드

Django에서 txt, jpg, pdf, docs, csv와 같은 파일은 File()객체로 취급한다. 일반적으로 우리가 아록있는 것 처럼 파일은 데이터베이스에 저장하는게 아니라 AWS S3처럼 따로 이러한 파일들을 업로드하고, 해당 파일의 **저장경로**를 전달해주는 방식으로 저장 및 반환한다.

Django에서는 Image파일을 저장하는 필드인 ImageField를 지원하고 있다. **settings.py**에서 파일 스토리지를 지정하고, 파일을 업로드 후, 파일이 **저장된 주소 경로를 데이터베이스에 저장**하게 된다.

> 실제 파일이 어디에 저장할지 결정해서 직접 구현해도 되지만, [django-storages](https://django-storages.readthedocs.io/en/latest/) 라이브러리를 사용하면 settings.py를 수정하는 것만으로 파일 스토리지를 이용할 수 있다.

```bash
poetry add django-storages
```

AWS S3를 파일 스토리지로 사용한다면, [여기](https://django-storages.readthedocs.io/en/latest/backends/amazon-S3.html)를 참고하자

### 1. FileField

https://docs.djangoproject.com/en/5.0/ref/models/fields/#filefield

### 2. ImageField

이미지 필드를 사용하기 위해서는 pillow 라이브러리를 설치해야 한다.

```bash
poetry add pillow
```

https://docs.djangoproject.com/en/5.0/ref/models/fields/#django.db.models.ImageField


## 2.3.6 그 외 필드

https://docs.djangoproject.com/en/5.0/ref/models/fields/#field-types

## 2.3.7 모델 간 매핑 필드

### 1. ForeignKey(1:N 매핑)

> 모델간의 관계가 1:N일때 사용한다.

- 1개의 상점이 N개의 상품을 가지고 있다
```python
class Product(models.Model):
	store = models.ForeignKey(
    	to="Store",
        null=False,
        on_delete=models.CASCADE,
    )
```

- 1명의 고객이 N개의 주문을 가지고 있다
```python
class Order(models.Model):
	customer = models.ForeignKey(
    	to="Customer",
        null=False,
        on_delete=models.SET_NULL,
    )
```

- 1명의 쇼핑몰 점주는 N개의 상점을 가지고 있다.
```python
class Store(models.Model):
	owner = models.ForeignKey(
    	to="ShoppingMallUser",
        null=False,
        on_delete=models.SET_NULL,
    )
```

#### to
- 모델간 관계가 1:N일때, **1에 해당하는 쪽에 to 옵션을 준다**. 위의 예시에서 점주가 1이고, 상점이 N이므로 to에 `ShoppingMallUser(점주)` 옵션을 준것이다.
- to에 모델을 매핑해줄 때 가급적 **문자열**로 매핑해야 한다. 그 이유는 여러개의 Django App에서, models.py간의 import가 생길 때 순환 참조 에러가 발생할 수 있기 때문이다.

#### on_delete
- `CASCADE` : 1에 해당하는 모델 데이터가 삭제되었을 때, 이와 매핑된 N에 해당하는 모델 데이터도 같이 삭제해야 한다. 1개의 상점에 N개의 상품이 있을 때, **CASCADE**옵션을 주어서, 상점이 삭제되면 N개의 상품도 전부 삭제된다.
- `PROTECT` : 1에 해당하는 모델 데이터를 삭제할 때 N에 해당하는 모델이 이미 1을 참조하고 있다면 삭제하지 못하도록 보호하는 설정이다. 만약, 쇼핑몰 점주를 삭제하려고 할 때, 점주가 가지고있는 상점이 하나 이상 존재하면 삭제가 진행되지 않는다.
- `SET_NULL` : 1에 해당되는 모델 데이터가 삭제되었을 때, 이와 매핑된 N에 해당하는 모델 데이터를 모두 NULL로 채워버린다. 하지만, N에 해당하는 모델 데이터가 `null=True`이어야지 동작한다.
- `SET_DEFAULT` : 1에 해당되는 모델 데이터가 삭제되었을 때, 이와 매핑된 N에 해당하는 모델 데이터를 모두 DEFAULT값으로 바꿔버린다. 마찬가지로 N에 해당하는 모델에 **default** 옵션이 설정되어 있어야 한다.
- `SET()` : 1에 해당하는 모델 데이터가 삭제되었을 때, 미리 설정된 함수를 사용해서 가져온 값 또는 미리 설정한 값으로 채워버린다.

```python
def set_deleted_owner():
	return -1234

class Store(models.Model):
	owner = models.ForeignKey(
    	to="ShoppingMallUser",
        null=False,
        on_delete=models.SET(set_deleted_owner),
        # owner 삭제 시, store값은 모두 -1234로 바뀐다
    )
```

### 2. OneToOne(1:1 관계 매핑)

> 모델간 관계가 1:1일 때 사용한다.

- 1명의 학생은 1개의 사물함을 가진다.
```python
class Locker(models.Model):
	student = models.OneToOneField(
    	to="Student",
        null=True,
        on_delete=models.SET_NULL,
    )
```

- 1명의 직원은 1개의 주차 공간을 배정받을 수 있음
```python
class Parkinglot(models.Model):
	employee = models.OneToOneField(
    	to="Employee",
        null=True,
        on_delete=models.SET_NULL,
    )
```

- 1개의 상점은 1개의 상점 정보 테이블을 가짐
```python
class Store(models.Model):
	store_info = models.OneToOneField(
    	to="StoreInfo",
        null=True,
        on_delete=models.CASCADE,
    )
```

---

> OneToOneField는 ForeignKey 클래스를 상속받은 필드이다. 따라서 ForeignKey에서 사용했던 옵션을 전부 사용 가능하다. (실제 소스코드 보면 이렇게 나와있음)

### 3. ManyToMany(M:N 관계 매핑)

> M:N 관계일 때 사용한다.

- Order와 Product는 서로 여러개를 가질 수 있다. 즉, 1개의 Order에 대해서 여러개의 Product가 포함되고, 1개의 Product에 대해 여러개의 Order가 포함될 수 있다.

```python
class Product(models.Model):
	name = models.CharField(max_length='128', help_text="product name")
    
    class Meta:
    	db_table = 'product'

# M:N관계는 주로 중간 테이블을 하나 만들어서 구성한다
class OrderedProduct(models.Model):
	order = models.ForeignKey(
    	to='Order',
        on_delete=models.CASCADE
    )
    product = models.ForeignKey(
    	to='Product',
        on_delete=models.CASCADE,
    )
    count = models.IntegerField(help_text='ordered_product_cnt', default=1)
    
    class Meta:
    	db_table = 'ordered_product'

class Order(models.Model):
	name = models.CharField(max_length='128', help_text="order name")
    product_set = models.ManyToManyField(
    	to='Product',
        through='OrderedProduct'
    )
    
    class Meta:
    	# Database의 order by 문법에 의해 order가 키워드로 이미 지정되어 있다. 따라서 orderz처럼 테이블명을 정하는것임
        # 비슷한 사례로 class 대신 clazz로 이름을 사용하는것도 있음
    	db_table = 'orderz'
```

---

- 게시글과 해시태그는 서로 여러개를 가질 수 있다. 마찬가지로 M:N관계이다.

```python
class Board(models.Model):
    # ...
    hashtag_set = models.ManyToManyField(
        to='HashTag',
        through='BoardHashTagRelation'
    )
    class Meta:
        db_table = 'board'
        
class BoardHashTagRealtion(models.Model):
    board = models.ForeignKey(
        to='Board',
        on_delete=models.CASCADE,
    )
    hashtag = models.ForeignKey(
        to='Hashtag',
        on_delete=models.CASCADE,
    )

class HashTag(models.Model):
    name = models.CharField(max_length=128)
    class Meta:
        db_table = 'hashtag'
```

---

- 학생과 수업의 관계도 M:N 관계이다. 학생은 여러개의 수업을 수강할 수 있고, 수업은 여러명의 학생으로 구성된다.

```python
class CommonModel(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True

class Lecture(models.Model):
    # ...
    class Meta:
        db_table = "lecture"

class StudentLectureRelation(models.Model):
    lecture = models.ForeignKey(to="Lecture", on_delete=models.CASCADE)
    student = models.ForeignKey(to="Student", on_delete=models.CASCADE)

    class Meta:
        # 만약 따로 지정해주지 않으면, django가 student_lecture_set으로 지어버린다.
        db_table = "student_lecture_relation"
```

>  **through** : M:N 매핑은 중계 테이블이 존재해야 하는데, django는 이 중계 테이블을 자동으로 생성해준다. 하지만, 개발자가 직접 정의한 중계 테이블을 사용하고 싶다면 through을 통해서 자신의 정의한 테이블이름을 넣어주ㄴ면 된다.
>

**THROUGH 사용을 권장하는 이유**

1. 예측할 수 없는 확장 가능성 존재

2. 직관적이지 않은 테이블 명으로 생성됨 (Django가 자동으로 생성하게 된다면)

3. Django가 migrate시 존재 여부 파악 불가

M:N관계를 나타내기 위해, 중계 테이블을 생성하지 않고 그냥 migrate를 수행했다면 Django가 알아서 중계 테이블을 만들어서 데이터베이스에 생성한다.

그런데 나중에 through을 통해 개발자가 정의한 중계 테이블을 지정해서 migrate를 실행하면 **이미 등록된 중계테이블이 존재하기 때문에 에러가 발생하게 된다.**

이를 해결하기 위해서는, migrate 명령에 --fake를 붙여주어야 한다. (테이블을 생성하려는 시도를 회피)

하지만, 이미 자동 생성된 중계 테이블이 있는 상태에서 through 옵션을 추가하려면 --fake 옵션을 사용하여 마이그레이션을 처리할 수 있지만, 이 방법은 복잡성과 잠재적인 문제를 초래할 수 있다.

따라서 처음부터 through를 사용하는 것이 좋다.

---

## 2.3.8 장고 모델 모듈

### UnManaged Model

Django에 선언된 Model이 반드시 실제 데이터베이스 테이블과 1:1로 매핑되지는 않는다.

![](https://velog.velcdn.com/images/calzone0404/post/5f99ce9e-82c9-4b82-aa49-72a9f27eadd7/image.png)

예를 들면 View 테이블이나 특정 Select 쿼리로 생성된 테이블을 예시로 들 수 있다. 이러한 뷰, 임시테이블과도 Django에 매핑될 수 있다.

- 주문을 관리하는 테이블이 있다고 해보자.
```python
class Order(models.Model):
	class Status(models.TextChoices):
    	WAITING = "waiting", "주문 수락 대기"
        ACCEPTED = "accepted", "주문 접수"
        REJECTED = "rejected", "주문 거절"
        DELIVERY_COMPLETE = "delivery complete", "배달 완료"
        
   status = models.CharField(max_length=32, choices=Status.choices, help_text="order status")
   total_price = models.IntegerField(default=0)
   store = models.ForeignKey(to="stores.Store", on_delete=models.CASCADE)
   product_set = models.ManyToManyField(to="products.Product", through="OrderedProduct")
   created_at = models.DateTimeField(auto_now_add=True, help_text="주문이 생성된 시간")
```
위 Order 테이블에 대한 **통계 정보**를 Django ORM을 사용해서 관리하고 싶다면, **Unmanaged Model**을 생성해주면 된다.

```python
class DailyReportManager(models.Manager):
    def get_list_by_created_at(self, created_at__gte, created_at__lt) -> List[DailyReport]:
        return list(self.raw(raw_query="""
        SELECT DATE_TRUNC('day', O.created_at) AS day,
        COUNT(*) AS total_cnt,
        SUM(O.total_price) as total_sales
        FROM orders_order O
        WHERE O.created_at >= %s AND O.created_at < %s
        GROUP BY DATE_TRUNC('day', O.created_at);
        """, params=[created_at__gte, created_at__lt]))

class DailyReport(models.Model):
    day = models.DateField(help_text="Date", primary_key=True)
    # Django 모델은 반드시 Primary key가 필요한데, 이건 Unmanaged 모델이라 Django가 알아서 PK를 생성해주지 않는다,
    # 따라서 직접 PK를 지정해주어야 한다.
    total_sales = models.IntegerField(help_text="일 주문 총매출")
    total_cnt = models.IntegerField(help="일 주문 건수")

    objects = DailyReportManager()

    class Meta:
        managed = False # Unmanaged Model

    def __repr__(self):
        return f"{self.day.strftime('%Y-%m-%d')}:DailyReport(total_cnt:{self.total_cnt} total_sales:{self.total_sales})"
```

위 Manager를 사용하고 싶다면, DailyReport 모델을 가져와서 사용해주면 된다.

```python
report_list: List[DailyReport] = DailyReport.objects.get_list_by_created_at(
	created_at__gte=date(2024, 01, 01),
    created_at__lt=date(2024, 06, 10),
)
```

## 2.3.9 CustomField

### Encrypted Field

### max_length

### EncryptedField로 선언된 경우 검색 조건으로 사용 불가

# 2.4 마이그레이션

> Django에서 모델링을 하게 되면, SQL로 해당 모델의 Plan을 작성해준다.

**데이터베이스 마이그레이션은 데이터베이스가 가지고 있는 데이터에 일괄적인 변화를 주는 작업이라고 할 수 있다.**

Django에서는 주로 아래 4가지 명령을 사용하게 된다.

```bash
python manage.py makemigrations

python manage.py sqlmigrate example_app 1234 # {장고 프로젝트 이름} {마이그레이션 파일 번호}

python manage.py showmigrations

python manage.py migrate
```

## 2.4.1 makemigrations

> Django(DBA)에게 DDL 수행 계획을 자문받는 명령어

`makemigrations` 명령은 `yourapp/migrations` 폴더의 하위 파일과 `yourapp/models.py`의 차이점을 비교하는 명령이다.

만약 차이점이 존재하면 새로운 migration 파일을 생성해주고, 정보를 일치시키기 위한 DDL이 포함된다.

실제로 makemigrations를 수행하면 다음과 같이 `000X_blabla.py`처럼 파일이 생성된다.

![](https://velog.velcdn.com/images/calzone0404/post/f268e0fc-355f-4984-843f-9369dbafc101/image.png)

파일을 열어보면, 다음과 같이 나온다.

![](https://velog.velcdn.com/images/calzone0404/post/b2818f28-31ec-405e-9a72-d361216f12d8/image.png)


이렇게 장고가 제안한 DDL을 **반드시 개발자가 살펴보고 적절한지 판단해주어야 한다**. `makemigrations`를 하고 `migrate`를 해주는 이유가 바로 이것이다.

## 2.4.2 Operations

위에 소개한 Migration class를 분석해보자.

```python
class Migration(migrations.Migration):

    initial = True
	
    """
    dependencies :
    마이그레이션 파일이 수행되려면 반드시 여기에 언급된 마이그레이션 파일이 먼저 수행되어야 한다는 제약
    즉, auth 앱이 가지고 있는 0012_alter... 파일이 먼저 수행되어야 한다는 의미이다.
    """
    dependencies = [
        ('auth', '0012_alter_user_first_name_max_length'),
    ]

    operations = [
    	
        """
        Django Model에 명시된 값을 토대로 테이블을 생성하는 DDL을 만들어준다. 
        아래 내용이 실제 DDL로 바뀌게 된다.
        """
        migrations.CreateModel(
            name='AccountModel',
            fields=[
                ('id', models.BigAutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('password', models.CharField(max_length=128, verbose_name='password')),
                ('last_login', models.DateTimeField(blank=True, null=True, verbose_name='last login')),
                ('is_superuser', models.BooleanField(default=False, help_text='Designates that this user has all permissions without explicitly assigning them.', verbose_name='superuser status')),
                ('username', models.CharField(error_messages={'unique': 'A user with that username already exists.'}, help_text='Required. 150 characters or fewer. Letters, digits and @/./+/-/_ only.', max_length=150, unique=True, validators=[django.contrib.auth.validators.UnicodeUsernameValidator()], verbose_name='username')),
                ('first_name', models.CharField(blank=True, max_length=150, verbose_name='first name')),
                ('last_name', models.CharField(blank=True, max_length=150, verbose_name='last name')),
                ('email', models.EmailField(blank=True, max_length=254, verbose_name='email address')),
                ('is_staff', models.BooleanField(default=False, help_text='Designates whether the user can log into this admin site.', verbose_name='staff status')),
                ('is_active', models.BooleanField(default=True, help_text='Designates whether this user should be treated as active. Unselect this instead of deleting accounts.', verbose_name='active')),
                ('date_joined', models.DateTimeField(default=django.utils.timezone.now, verbose_name='date joined')),
                ('created_at', models.DateTimeField(auto_now_add=True)),
                ('updated_at', models.DateTimeField(auto_now=True)),
                ('phone_number', models.CharField(blank=True, max_length=15, null=True)),
                ('groups', models.ManyToManyField(blank=True, help_text='The groups this user belongs to. A user will get all permissions granted to each of their groups.', related_name='user_set', related_query_name='user', to='auth.group', verbose_name='groups')),
                ('user_permissions', models.ManyToManyField(blank=True, help_text='Specific permissions for this user.', related_name='user_set', related_query_name='user', to='auth.permission', verbose_name='user permissions')),
            ],
            options={
                'verbose_name': 'user',
                'verbose_name_plural': 'users',
                'abstract': False,
            },
            managers=[
                ('objects', django.contrib.auth.models.UserManager()),
            ],
        ),
        
        """
        AddField는 Django Field에 명시된 값을 토대로 새로운 Column을 생성한다.
        아래 내용이 실제 DDL로 바뀌게 된다. (ALTER TABLE asdfasdf)
        """
       	migrations.AddField(
       		model_name='NewModel',
            name='asdf_1234_field',
            field=your_app.models.fields.CharField(
            	default='asdf', max_length=32
            )
      	),
    ]
```

### CreateModel, AddModel 차이

> CreateModel은 하나의 CREATE TABLE blabla..를 생성해주는데, AddModel은 ALTER 명령이 총 2개 생성이 된다.

```sql
ALTER TABLE "asdf" ADD COLIUMN "a_1234_field" varchar(32) DEFAULT 'asdf' NOT NULL;
ALTER TABLE "asdf" ALTER COLUMN "a_1234_field" DROP DEFAULT
```

위에 나온 migration python 소스코드에 default='asdf'옵션이 있는것 처럼 sql 코드에서 default로 asdf를 추가해 주도록 설정된다. 그런데 다음 명령이 추가적으로 생성되는데 여기서는 DROP DEFAULT를 수행하게 된다.

왜 굳이 데이터베이스에 DEFAULT를 설정해놓았는데 Django가 이걸 삭제하는 DDL을 또 만들어버렸을까? 이는 Django ORM의 특징을 알아야 한다.

> Django ORM은 SQL의 DEFAULT 옵션을 사용하지 않고, 자기가 알아서 전부 제어하기를 원한다.

아래 그림을 살펴보도록 하자. 그림처럼 DEFAULT 옵션을 제어하는 주체가 다르다.

![](https://velog.velcdn.com/images/calzone0404/post/09b4f00f-a497-47d6-9dc1-e5b7abe60365/image.png)

Django를 통하지 않고, SQLAlchemy나 직접 Database에 접근하게 된다면, 그대로 DEFAULT값이 실제 DB에 반영되고, DB가 해당 DEFAULT값을 제어하게 된다.

![](https://velog.velcdn.com/images/calzone0404/post/baafced5-c4e1-46dd-ba8f-3e465f431d99/image.png)

만약 Django ORM을 통해 접근하게 된다면, DB에서 DEFAULT값을 제어하는것이 아닌, Django ORM이 직접 제어해서 DEFAULT값을 넣어주게 된다.

아래 그림처럼 DEFAULT 옵션이 DB에 걸려있다고 착각하고 직접 SQL을 DB에서 수행하거나 SQLAlchemy같은 어플리케이션으로 접근하게 되면, 불안정한 값이 채워지게 된다.

![](https://velog.velcdn.com/images/calzone0404/post/83eda01a-814d-4aad-b6f7-13536ea554de/image.png)

만약 DB자체에서도 DEFAULT옵션을 관리하도록 설정하고 싶다면 operations = []에 `migrations.RunSQL`로 Raw SQL문을 적어줘서 설정해주어야 한다. [참고링크](https://docs.djangoproject.com/en/5.0/ref/migration-operations/#runsql)

## 2.4.3 sqlmigrate

> Migration 수행 계획 검토하는 명령어

이 명령을 사용하면 실제 수행될 DDL 명령을 출력해서 볼 수 있다.

```bash
python manage.py sqlmigrate {django app 이름} {migration filenumber 또는 filename}
```

![](https://velog.velcdn.com/images/calzone0404/post/90ed8a5c-2f72-41de-a9bf-ac6faff8de9a/image.png)

### --backwards

만약 특정 migration 파일 실행으로 인해 문제가 발생했다면, --backwards 옵션을 추가해서 rollback을 진행할 수 있다. 이 옵션을 붙여서 sqlmigrate 명령을 작성하면 DB를 복구하는 DDL을 출력해준다.

## 2.4.4 migrate

> 실제 Database에 DDL를 반영하는 명령

항상 DB관련 작업을 Django에서 수행하게 되면,
1. makemigrations를 이용하여 DDL을 제안받고, 커스터마이징 해서
2. sqlmigrate를 사용해서 DDL를 확인후,
3. migrate를 사용해서 실제 DB에 적용한다.

```bash
python manage.py migrate {django app name} {migration file number}
```

- migrate 명령은 파일 단위로도 수행이 가능하다. 위 명령어에 django app name과 migration file number을 적어주면 된다. **아무것도 적혀있지 않으면, Django 전체 application에 대한 migration이 수행된다.**

### --fake

migrate 수행 시 실제 DB에 반영하지 않고, 수행 완료 처리를 하는 옵션

사용하는 경우는 다음과 같다.

1. Django 제어 범위 밖에서 DDL이 반영되어 이를 기록해야 하는 경우

2. 모든 Database작업이 Django가 아닌 DBA가 직접 제어하는 경우, 모든 migartion 파일에 이력만 기록해야 하는 경우

3. 특정 migration 파일이 수행 중 에러가 발생하면 해당 migration 파일을 --fake처리해주고, 나머지 DDL을 수행하는 경우

### --plan

migrate 수행 계획을 출력해주는 옵션. sqlmigrate와 달리 Django Operation의 수행 계획을 출력한다. (거의 사용 안함)

## 2.4.5 showmigrations

> migration 이력을 조회하는 명령어

```bash
python manage.py showmigrations
```

![](https://velog.velcdn.com/images/calzone0404/post/2ca7e4b8-0e68-4118-9401-e1d80465d1d4/image.png)

이렇게 X표시가 되어있다면, 수행된 마이그레이션 파일이다.

## 2.4.6 sqaushmigrations

> migration 이력을 통합하는 명령어

여러개의 migration 이력 파일을 하나로 통합해주는 명령이다. 나중에 프로젝트가 오래되고 규모가 커지면 한 번 씩 사용하게 된다.

특정 범위로 제한해서 특정 파일들만 통합할수도 있다.

```bash
python manage.py squashmigrations your_app 0001 0005
```
0001번부터 0005번까지의 이력을 하나로 통합해준다.

## 2.4.7 커스텀 마이그레이션 파일 작성하기

