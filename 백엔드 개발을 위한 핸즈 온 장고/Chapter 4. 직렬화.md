# 4.1 직렬화란?

직렬화(Serialization)는 데이터 구조나 객체 상태를 저장하거나 전송할 수 있는 형식으로 변환하는 과정이다. 이는 프로그래밍에서 중요한 개념으로, 특히 네트워크 통신이나 데이터 저장 시 필수적이다.

### Python class, dictionary를 다른 언어에서 인식할 수 있을까?

Python의 class나 dictionary는 Python 특유의 데이터 구조이므로 다른 언어에서 직접 인식할 수 없다. 따라서 이를 공통적으로 이해할 수 있는 형식(예: JSON, XML)으로 변환해야 한다. 이 과정이 바로 직렬화이다.

## 4.1.1 Python만으로 Class -> Dictionary로 직렬화하는 방법

Python class를 dictionary로 직렬화하는 방법은 여러 가지가 있다. 가장 간단한 방법 중 하나는 `__dict__` 속성을 사용하는 것이다.

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

person = Person("John Doe", 30)
person_dict = person.__dict__

print(person_dict)  # 출력: {'name': 'John Doe', 'age': 30}
```

더 복잡한 경우, 커스텀 메서드를 만들 수 있다:

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def to_dict(self):
        return {
            'name': self.name,
            'age': self.age
        }

person = Person("John Doe", 30)
person_dict = person.to_dict()

print(person_dict)  # 출력: {'name': 'John Doe', 'age': 30}
```

## 4.1.2 Python만으로 Dictionary -> Json으로 직렬화하는 방법

Python의 `json` 모듈을 사용하면 dictionary를 JSON으로 쉽게 직렬화할 수 있다.

```python
import json

person_dict = {'name': 'John Doe', 'age': 30}
json_string = json.dumps(person_dict)

print(json_string)  # 출력: {"name": "John Doe", "age": 30}
```

### Json이 인식할 수 있는 Data type

JSON이 인식할 수 있는 데이터 타입은 다음과 같다:
- 문자열 (string)
- 숫자 (number)
- 불리언 (boolean)
- 객체 (object)
- 배열 (array)
- null

Python의 데이터 타입과 JSON의 데이터 타입은 다음과 같이 대응된다:
- dict -> object
- list, tuple -> array
- str -> string
- int, float -> number
- True/False -> true/false
- None -> null

### 날짜의 경우 어떻게 직렬화해야하는가?

날짜는 JSON에서 기본적으로 지원하지 않는 데이터 타입이다. 따라서 문자열로 변환하여 직렬화해야 한다. 일반적으로 ISO 8601 형식을 사용한다.

```python
import json
from datetime import datetime

class DateTimeEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        return super().default(obj)

data = {
    'name': 'John Doe',
    'birthdate': datetime(1990, 1, 1)
}

json_string = json.dumps(data, cls=DateTimeEncoder)
print(json_string)  # 출력: {"name": "John Doe", "birthdate": "1990-01-01T00:00:00"}
```

# 4.2 DRF Serializer

Django Rest Framework (DRF)의 Serializer는 복잡한 데이터 타입(예: 쿼리셋, 모델 인스턴스)을 Python 기본 데이터 타입으로 변환하여 JSON, XML 등으로 쉽게 렌더링할 수 있게 해준다.

## 4.2.1 데이터 직렬화

DRF Serializer를 사용하면 Django 모델을 쉽게 직렬화할 수 있다.

```python
from rest_framework import serializers
from .models import Person

class PersonSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    age = serializers.IntegerField()

person = Person(name="John Doe", age=30)
serializer = PersonSerializer(person)
print(serializer.data)  # 출력: {'name': 'John Doe', 'age': 30}
```

## serializers.Serializer

`serializers.Serializer`는 가장 기본적인 형태의 Serializer이다. 필드를 명시적으로 정의해야 하며, 데이터의 검증과 저장 로직을 직접 구현해야 한다.

주요 특징:
1. 필드 정의: 각 필드를 명시적으로 정의해야 한다.
2. 유연성: 모델과 관계없이 자유롭게 필드를 정의할 수 있다.
3. 검증: `validate_<field_name>` 메서드로 각 필드의 유효성을 검사할 수 있다.
4. 생성 및 수정: `create()`와 `update()` 메서드를 구현하여 객체의 생성과 수정 로직을 정의할 수 있다.

예시:

```python
class PersonSerializer(serializers.Serializer):
    name = serializers.CharField(max_length=100)
    age = serializers.IntegerField()
    email = serializers.EmailField()

    def validate_age(self, value):
        if value < 0:
            raise serializers.ValidationError("Age must be a positive number")
        return value

    def create(self, validated_data):
        return Person.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.name = validated_data.get('name', instance.name)
        instance.age = validated_data.get('age', instance.age)
        instance.email = validated_data.get('email', instance.email)
        instance.save()
        return instance
```

## serializers.ModelSerializer

`serializers.ModelSerializer`는 Django 모델을 기반으로 자동으로 필드를 생성하는 Serializer이다. 

주요 특징:
1. 자동 필드 생성: 모델의 필드를 자동으로 Serializer 필드로 변환한다.
2. 검증: 모델의 필드에 정의된 제약 조건을 자동으로 검증한다.
3. 간편한 구현: `create()`와 `update()` 메서드가 자동으로 구현된다.
4. 커스터마이징: 필요에 따라 필드를 추가, 수정, 제거할 수 있다.

예시:

```python
class PersonSerializer(serializers.ModelSerializer):
    class Meta:
        model = Person
        fields = ['id', 'name', 'age', 'email']
        # 또는 fields = '__all__'로 모든 필드를 포함할 수 있다.
```

### Meta.depth

`Meta.depth` 옵션을 사용하면 관계된 객체의 중첩 수준을 지정할 수 있다.

```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ['id', 'title', 'author']
        depth = 1  # author 필드가 중첩된 객체로 표현된다.
```

### many=True 옵션

`many=True` 옵션은 여러 객체를 직렬화할 때 사용한다.

```python
people = Person.objects.all()
serializer = PersonSerializer(people, many=True)
print(serializer.data)  # 여러 Person 객체의 리스트가 출력된다.
```

### ManyToManyField()를 Serializer에 매핑 시 매개 모델 (through) 참조하기

ManyToManyField에 through 모델이 있는 경우, 이를 Serializer에서 참조할 수 있다.

```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author, through='BookAuthor')

class BookAuthor(models.Model):
    book = models.ForeignKey(Book, on_delete=models.CASCADE)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    contribution = models.CharField(max_length=100)

class BookSerializer(serializers.ModelSerializer):
    authors = serializers.SerializerMethodField()

    class Meta:
        model = Book
        fields = ['id', 'title', 'authors']

    def get_authors(self, obj):
        book_authors = BookAuthor.objects.filter(book=obj)
        return [{'id': ba.author.id, 'name': ba.author.name, 'contribution': ba.contribution} for ba in book_authors]
```

### read_only, write_only를 언제 사용할까?

`read_only`와 `write_only` 옵션은 Django Rest Framework (DRF)의 Serializer에서 필드의 동작을 제어하는 데 사용된다. 
이 옵션들을 적절히 사용하면 API의 보안성과 사용성을 향상시킬 수 있다.

### read_only=True

1. 사용 시기:
   - 클라이언트가 읽을 수는 있지만 수정할 수 없어야 하는 필드
   - 서버에서 자동으로 생성되거나 계산되는 필드
   - 보안상 중요한 정보를 포함하지만 클라이언트에게 표시해야 하는 필드

2. 예시:
```python
class UserSerializer(serializers.ModelSerializer):
    last_login = serializers.DateTimeField(read_only=True)
    is_staff = serializers.BooleanField(read_only=True)
    
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'last_login', 'is_staff']
```

`last_login`과 `is_staff`는 클라이언트가 읽을 수는 있지만 수정할 수 없어야 하므로 read_only로 설정해준다.

3. 추가 예시:
```python
class PostSerializer(serializers.ModelSerializer):
    author = serializers.StringRelatedField(read_only=True)
    created_at = serializers.DateTimeField(read_only=True)
    
    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'author', 'created_at']
```

여기서 `author`와 `created_at`은 서버에서 자동으로 설정되므로 읽기 전용으로 설정한다.

### write_only=True

1. 사용 시기:
   - 클라이언트가 제공해야 하지만, API 응답에는 포함되지 않아야 하는 필드
   - 비밀번호와 같은 민감한 정보를 포함하는 필드
   - 내부 처리용으로만 사용되는 필드

2. 예시:
```python
class UserCreateSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True)
    confirm_password = serializers.CharField(write_only=True)

    class Meta:
        model = User
        fields = ['username', 'email', 'password', 'confirm_password']

    def validate(self, data):
        if data['password'] != data['confirm_password']:
            raise serializers.ValidationError("Passwords do not match")
        return data

    def create(self, validated_data):
        validated_data.pop('confirm_password')
        return User.objects.create_user(**validated_data)
```

여기에서 `password`와 `confirm_password`는 쓰기 전용으로 설정되어 있는데, 비밀번호와 비밀번호 확인 문자열은 사용자 생성 시 필요하지만, API 응답에는 포함되면 안되므로 write_only를 적용해준다.

3. 추가 예시:
```python
class PaymentSerializer(serializers.ModelSerializer):
    credit_card_number = serializers.CharField(write_only=True)
    cvv = serializers.CharField(write_only=True)

    class Meta:
        model = Payment
        fields = ['amount', 'credit_card_number', 'cvv']

    def create(self, validated_data):
        # 결제 처리 로직
        # credit_card_number와 cvv는 저장하지 않고 처리 후 폐기
        return Payment.objects.create(amount=validated_data['amount'])
```

여기에서 신용카드 정보는 결제 처리에만 사용되고 저장되지 않으므로 쓰기 전용으로 설정해주면 된다.

### 주의 사항

1. `read_only=True`와 `write_only=True`를 동시에 사용하면 해당 필드는 API에서 완전히 무시된다.

2. `ModelSerializer`를 사용할 때, 모델의 필드가 `editable=False`로 설정되어 있다면 자동으로 `read_only=True`가 적용된다.

3. `write_only` 필드는 생성(`create()`)이나 수정(`update()`) 메서드에서 적절히 처리해야한다.

## Serializer 자세한 사용 예시

### models.py
```python
from django.db import models
from django.contrib.auth.models import User

class Author(models.Model):
    # User 모델과 1:1 관계. 작가 삭제 시 연결된 User도 삭제됨
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(blank=True)
    birth_date = models.DateField(null=True, blank=True)

    def __str__(self):
        return self.user.get_full_name()

class Genre(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Book(models.Model):
    title = models.CharField(max_length=200)
    isbn = models.CharField(max_length=13, unique=True)

    # Author와 다대다 관계. BookAuthor를 통해 관계가 설정됨
    authors = models.ManyToManyField(Author, through='BookAuthor')

    # 다대다 관계인데 따로 through로 설정해주지 않으면 Django ORM이 알아서 만들어줌
    genres = models.ManyToManyField(Genre)
    publication_date = models.DateField()
    price = models.DecimalField(max_digits=6, decimal_places=2)

    def __str__(self):
        return self.title

class BookAuthor(models.Model):
    # Book과 Author의 중간 모델. 추가 정보(contribution)를 저장할 수 있음
    book = models.ForeignKey(Book, on_delete=models.CASCADE)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    contribution = models.CharField(max_length=100, blank=True)

    class Meta:
        # 한 책에 대한 작가의 기여는 한 번만 기록될 수 있음
        unique_together = ('book', 'author')

class Review(models.Model):
    # Book과 일대다 관계. related_name을 사용하여 역참조 가능
    book = models.ForeignKey(Book, on_delete=models.CASCADE, related_name='reviews')
    reviewer = models.ForeignKey(User, on_delete=models.CASCADE)
    content = models.TextField()
    rating = models.IntegerField(choices=[(i, i) for i in range(1, 6)])
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"Review for {self.book.title} by {self.reviewer.username}"
```

### serializers.py
```python
from rest_framework import serializers
from django.contrib.auth.models import User
from .models import Author, Genre, Book, BookAuthor, Review

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'first_name', 'last_name']

class AuthorSerializer(serializers.ModelSerializer):
    user = UserSerializer(read_only=True) # 중첩 직렬화. 읽기 전용으로 설정됨. 즉, user json 형태에 UserSerializer 결과가 반환되어 합쳐지게 된다.
    
    class Meta:
        model = Author
        fields = ['id', 'user', 'bio', 'birth_date']

class GenreSerializer(serializers.ModelSerializer):
    class Meta:
        model = Genre
        fields = ['id', 'name']

class ReviewSerializer(serializers.ModelSerializer):
    reviewer = UserSerializer(read_only=True)

    class Meta:
        model = Review
        fields = ['id', 'reviewer', 'content', 'rating', 'created_at']

class BookAuthorSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True) # 중첩 직렬화

    class Meta:
        model = BookAuthor
        fields = ['author', 'contribution']

class BookSerializer(serializers.ModelSerializer):
    authors = BookAuthorSerializer(source='bookauthor_set', many=True, read_only=True)
    genres = GenreSerializer(many=True, read_only=True)
    reviews = ReviewSerializer(many=True, read_only=True)

    # 모델에 정의된 필드가 아닌, 직렬화 시 추가적으로 필요한 필드를 SerializerMethodField()로 정의해준다.
    # !!!get_average_rating 과 같이 get_필드명 으로 메서드 명을 지정해줘야 정확하게 인식한다!!!
    average_rating = serializers.SerializerMethodField()

    class Meta:
        model = Book
        fields = ['id', 'title', 'isbn', 'authors', 'genres', 'publication_date', 'price', 'reviews', 'average_rating']

    def get_average_rating(self, obj): # Book.review_set.all()으로 해당 책의 리뷰를 모두 가져와서 average rating 계산
        reviews = obj.reviews.all() # related_name이 설정되어 있으므로 reviews.all()
        if reviews:
            return sum(review.rating for review in reviews) / len(reviews)
        return None

    def create(self, validated_data): # create 함수를 오버라이딩 하여 원하는 방식으로 동작하도록 구현
        genres_data = validated_data.pop('genres', [])
        authors_data = validated_data.pop('authors', [])
        book = Book.objects.create(**validated_data)
        
        for genre_data in genres_data:
            genre, _ = Genre.objects.get_or_create(**genre_data) # 있으면 가져오고 없으면 만듬. 이때 created 여부 flag도 같이 제공되는데, 딱히 필요 없으므로 변수명을 _로 설정한것임
            book.genres.add(genre)
        
        for author_data in authors_data:
            author = author_data.pop('author')
            BookAuthor.objects.create(book=book, author=author, **author_data)
        
        return book

    def update(self, instance, validated_data): # update 함수를 오버라이딩 하여 원하는 방식으로 동작하도록 구현
        genres_data = validated_data.pop('genres', [])
        authors_data = validated_data.pop('authors', [])
        
        instance = super().update(instance, validated_data)

        # 기존 관계를 모두 제거하고 새로 추가
        instance.genres.clear()
        for genre_data in genres_data:
            genre, _ = Genre.objects.get_or_create(**genre_data)
            instance.genres.add(genre)
        
        # 기존 관계를 모두 제거하고 새로 추가
        instance.bookauthor_set.all().delete()
        for author_data in authors_data:
            author = author_data.pop('author')
            BookAuthor.objects.create(book=instance, author=author, **author_data)
        
        return instance
```
