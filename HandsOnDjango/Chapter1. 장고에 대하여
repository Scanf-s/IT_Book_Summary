> Django 시리즈는 [백엔드 개발을 위한 핸즈온 장고](https://product.kyobobook.co.kr/detail/S000202404727)책을 기반으로 작성할 것이다. 이 책은 Django 입문, 초급자를 위한 책이지만, 백엔드개발을 할 때, 장고를 어떻게 사용해야 하는지에 대한 책이므로, 장고를 대강 어떻게 사용하는지에 대해서 먼저 학습할 수 있는 입문용 책을 먼저 읽고, 해당 책을 읽는것이 더 도움이 될것이라고 생각한다.

> 필자는 글을 작성하는 시점에서, [이한영의 Django 입문](https://product.kyobobook.co.kr/detail/S000201056504)책을 1회독한 상태이다. 장고 입문 책을 읽으면서, 이해되지 않았던 부분과 생소한 부분이 많았는데, 이 책을 통해 Django에 대한 이해도를 높일것이다.

# 1.1 웹 프로그래밍과 백엔드 프로그래밍

과거 웹 프로그래밍은 Server와 Client 개발을 분리하지 않고, 한 번에 하는 개발 방식이었음.
하지만, 기술이 점점 고도화됨에 따라 웹 프로그래밍 개발자는 많은 지식이 필요하며, 많은 기능 구현이 요구됨.

> SPA : 하나의 페이지만 존재하는 웹 어플리케이션.
웹의 각 페이지마다, HTML 파일이 별도로 존재하며 페이지를 이동하면 브라우저에서 해당 HTML을 받아와서 화면에 표시해주는 방식

따라서 클라이언트측은 `Frontend`, 서버측은 `Backend`로 분업화되었음. 프론트엔드는 주로 Javascript 기반의 프레임워크 (React, Angular, Vue)등을 사용하며, 백엔드는 Python, Java, Ruby, Go, Cpp등 취향에 맞는 다양한 언어를 사용함.

# 1.2 장고 MTV 아키텍쳐: 템플릿의 한계

한국에서 가장 많이 사용하는 Spring의 디자인 패턴은 MVC(Model View Controller)이다.
히지만 Django는 `MTV`패턴을 사용한다.
- Model : 데이터베이스와 관련된 것을 담당
- Template : 주로 사용자가 보게되는 화면 (프론트엔드)를 담당
- View : 웹페이지의 링크 관리, 프론트 측으로 넘겨줄 데이터를 담당

![](https://velog.velcdn.com/images/calzone0404/post/ffd1e640-0d0e-4c43-b342-3668215c6f9e/image.png)

혼자 장고를 사용해서 개발할때에는, MTV패턴을 사용하여 웹개발자 혼자서 프론트와 백엔드를 전부 처리해야 하지만, 요즘 시대의 트렌드는 `분업화`이므로, **MTV패턴의 Template이 해야하는 역할은 모두 프론트측에서 담당한다**

이 책은 백엔드가 수행하는 역할에 대해서만 초점을 맞추었기 때문에, Template, TemplateLoader에 대한 내용을 다루지 않는다. 대신, Model, View에서 사용하는 다양한 기술들을 집중적으로 소개한다.

# 1.3 장고란 무엇인가

## 1.3.1 풀스택 프레임워크
1.2절에서 소개한것처럼, 장고를 사용하면 풀스택 개발이 가능하다. 즉, 장고 생태계 내부에서 대부분의 기능이 제공된다는 뜻이다. 장고에서 모든 기능을 제공하고 있기 때문에 장고에 대해서 매우 잘 이해하고 있다면 빠른 개발이 가능하지만,

반대로 장고에 대해서 잘 알지 못하는 필자와 같은 입문자 수준에서의 사용은, Flask나 FastAPI에 비해 개발속도가 느릴 수 있다.

Flask나 FastAPI는 단순히 main파일에 route를 지정해서 간단한 API를 뚝딱 만들어낼 수 있지만,

Django는 
1. createapp을 통해 app을 만들고
2. config/settings.py에 app을 등록하고
3. views.py에 API를 구현하고
4. urls.py에 URL까지 등록해야 사용할 수 있는 API가 만들어진다.

Django가 이런 방식을 채택하게 된 이유는, **높은 복잡도를 가지는 프로젝트의 각 Domain과, Module간 계층이** `높은 응집도`와 `낮은 결합도` **를 가질 수 있도록 하기 위해서이다.**

> Domain : 소프트웨어로 해결하고자 하는 문제 영역
> DDD : 도메인 주도 설계

> Django App : Django는 Django App이라는 단위를 통해 도메인을 분리할 수 있다.

프로젝트의 **복잡도를 낮추기 위해서 Django는 App을 생성해서 프로젝트를 구성하는 복잡한 시스템을 분리하도록 도와준다.** 

따라서, 장고가 제공하는 서비스를 통해 개발자는 패턴만 지키면서 개발할 수 있다는 장점이 생기게 된다. (물론, 이 패턴을 지키지 않으면 무언가 작동하지 않는 제약이 존재한다.)

반대로 Flask나 FastAPI같은 마이크로 프레임워크에서는 이런 기능을 제공하지 않기 때문에 도메인과 계층을 분리해야하는 고민이 생길수밖에 없다. 따라서 프로젝트의 코드 퀄리티가 온전히 개발자의 역량에 따르게 된다.

## 1.3.2 Django 이외의 다양한 선택지

### Flask
가장 인기있는 파이썬 마이크로 프레임워크로, 가볍고 간결하게 웹개발이 가능하다. ORM기능을 사용하기 위해 `SQLAlchemy`를 사용해야 하며, 프레임워크가 오래되었기 때문에 Flask 기반의 라이브러리들이 많다. (Flask-login, Flask-restx, Flask-migrate 등...)

### FastAPI
2018년도에 소개된 갓 나온 마이크로 웹프레임워크이다. 파이썬 웹 프레임워크중 가장 빠른 성능을 보여주고 문법도 간결해서 최근 많이 주목받고 있다.

## 1.3.3~4 Django를 선택해야 하는 이유

1. `대중성` : 수많은 공식문서와 오류 해결방법, 다양한 예시 코드가 존재하여 개발할 때 편하다
2. `풀스택 프레임워크` : 마이크로 프레임워크와 달리, 입문자에게 동일한 커리큘럼을 제공하여 웹 개발에 필요한 대부분의 기능을 제공하고 있다.
3. `거대한 커뮤니티, 정보량` : 어느정도 숙련된 개발자는 오픈소스를 찾아보거나 공식문서를 읽으면서 오류의 원인을 찾을 수 있지만, 입문자 입장에서는 쉽지 않다. 따라서, 사람들이 직접 도와주는 커뮤니티를 통해 쉽게 해결책을 찾을 수 있다.
4. `마이크로 웹프레임워크 개발 시 단점` : 필자는 Flask로 웹 페이지를 제작해보거나, API를 생성해주는 Application을 개발해보았는데, 개발을 진행할수록 필요한 라이브러리를 찾아서 Application에 적용해야 했다. 이렇게 되면 굳이 Flask고 불편하게 전부 개발해야 하는것에 비해, 장고를 사용해서 좀 더 간결하게 개발하는게 좋을것 같다고 생각한다.

## 1.3.5 Django의 설계 철학

### 낮은 결합도

**낮은 결합도, 높은 응집도**라는 설계 철학은 장고뿐만이 아니라 대부분의 소프트웨어에 적용되는 철학이다.

장고 프레임워크의 각 계층 모듈은 **서로 모르게**설계 되어 있다. 다시 말해, **언제든 다른 모듈로 대체가 가능하다**라는 뜻이다.

예를 들어, 인증 기능을 구현할 때 cookie&session 방식을 사용하였다면, drf-simple-jwt, django-oauth-toolkit으로 언제든지 인증 기능 방식을 대체해버릴 수 있다.

대체해버릴때에는 인증 모듈을 수정해야한다던가, View에서 처리하는 인증 코드를 수정하지 않아도 된다. 왜나하면 장고에서 이미 잘 짜여진 코드를 제공하고 있기 때문에, 가져다가 사용하기만 하고, settings.py에서 교체해버리면 된다.

### 명시적

만약 개발자 A가 데이터베이스 commit 기능을 수행하는 `save()`함수를 Override하여 새로운 기능을 추가한 `save()`함수를 만들었다고 해보자. 이를 암시적으로 동작하는 코드라고 한다.

개발자 B가 해당 save()함수가 새로운 기능이 추가된지 모르고 단순히 commit 기능을 수행하는 것으로 착각하고 사용을 해버릴수도 있다.

이렇게 되면 `save()`함수가 이렇고 저렇게 동작할 것이라는 약속을 깨버리는 것이므로, `save()`같은 메서드를 override하는 행위는 자제해야한다.

장고는 save()를 override하는것 보다는, 명시적인 새로운 메서드를 만드는 것이 바람직하다고 본다.

### Djnago의 시그널

명시적인 함수를 생성하는것이 좋지만, 불가피하게 save()와 같은 method를 override하게 되는 상황이 존재할 수 있다. Django에서는 override보다, `django signals`를 사용하는것을 권장하고 있다.

> Django signal : 어떤 동작이 수행되었을 때 이를 신호로 받아들여서 다른 동작을 수행하도록 해주는 기능

예를 들어, 은행계좌 데이터는 항상 변경될 때 마다 변경 전 잔고와 변경 후 잔고를 이력으로 저장해놓아야 한다면, signal을 통해서 다음과 같이 구현할 수 있다.

```python
# /project/accounts/models/signals.py

from accounts.models import Account
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save, sender=Account)
def log_the_balance_after_save(sender, **kwargs):
	print("Account 모델이 save()를 호출함")
```

이 코드를 작성하면, Account 모델이 post_save()를 수행한 후, log_the_balance_after_save()함수를 실행하게 된다.

---

장고는 각 모듈마다 signal을 가지고 있는데, 3가지가 존재한다.

1. request_started : API 요청이 들어온 경우
2. request_finished : API 요청이 끝난 경우
3. got_request_exception : API 요청 처리 중 에러 발생

---

하지만 시그널 사용은 암시적인 동작을 발생시키므로, 어떤 시그널이 다른 시그널을 호출하거나 코드에 보이지 않는 동작을 마구마구 수행하는것처럼 보이게 된다.

따라서 회사에서 개발하거나, 따로 프로젝트를 할 때 시그널 사용 원칙을 정해두고 개발해야한다.

1. 시그널 로직은 반드시 models/signals.py 또는 views/signals.py에 작성하자.
2. 로그를 쌓는 경우에만 시그널을 사용하자.

### Model module이 Domain 로직을 캡슐화

Django에는 `Fat Model` 패턴이 존재한다. Fat Model 패턴은 특정 모델과 관련된 데이터 조작 메서드를 Model을 구현한 클래스 내부에 구현해서 캡슐화하도록 하는 패턴이다.

예를 들어, Fat Model을 적용하지 않는다면
```python
from django.contrib.auth.hashers import PBKDF2PasswordHasher
from django.contrib.auth.models import User

user = User.objects.get(id=1)

hasher = PBKDF2PasswordHasher()
salt = hasher.salt()

encrypted_password = PBKDF2PasswordHasher.encode(password="123123", salt=salt)
user.password = encrypted_password

user.save()
```

---

Fat Model을 적용하면
```python
class User(model.Model):
	def set_password(self, plain_password):
    	hasher = PBKDF2PasswordHasher()
        salt = hasher.salt()

        encrypted_password = PBKDF2PasswordHasher.encode(password="123123", salt=salt)
        user.password = encrypted_password

# 외부에서 다음과 같이 사용해주면 된다.
user = User.objects.get(id=1)
user.set_password(plain_password="123123")
user.save()
```

사실 set_password는 Django에서 기본적으로 제공하는 User 모델에서 기본적으로 제공하기 때문에 위처럼 구현할 필요는 없다.

장고에서 제공하는 User에 새로운 User를 생성하고 싶다면, `create_user()`를 반드시 사용해야 한다.

```python
from django.contrib.auth.models import User

# 자동으로 User생성시 단방향 암호화를 해버린다.
user: User = User.objects.create_user(username="test", password="123123")

# 패스워드 변경하고싶을 때 set_password()를 하면, password를 수정하고, 다시 단방향 암호화 기능을 제공한다.
user.set_password(password="new_password")
user.save()

# 절대 이렇게 수정하지 말아야한다! 암호화하지 않기 때문
user.password = "plain!!!!!!!!"

```

### 효율적인 SQL 수행

3장에서 소개한다고 한다.

## 1.3.6 Django Admin

필자는 Flask로 웹 개발을 처음 시작했는데, Django admin기능을 사용해보고 매우 놀라운 기능이라고 생각했다, Flask는 Admin을 자체적으로 지원해주지 않고 라이브러리를 사용해야 하는데, Django는 자체적으로 지원해주기 때문에 간편하게 Admin page를 구현할 수 있다.

Django Admin은 기본적으로 CRUD 작업을 지원해준다.

하지만, **실무에서는 Django Admin을 잘 사용하지 않고 단순한 기능만 사용하는 툴로 사용한다고 한다.**

그 이유는 프로젝트가 복잡해질수록 다양한 Admin 기능이 필요로 한데, 해당 요구를 들어주기 위해 Admin Template을 마구마구 수정하게 된다.

단순히 몇개의 기능을 추가하는 것은 괜찮지만, 계속 쌓이다보면 매우 복잡해지게 되고, 결국에는 MTV 패턴으로 UI를 새로 전부 개발해버려야 한다.

Django admin 기능은 CRUD 이외의 기능은 요구 사항으로 받지 않아야 한다. 그 이상으로 활용하려면 기초적인 Admin page 설계를 다시 해야하기 때문에, 입문자 입장에서는 다소 까다롭다.

자세한 장고 어드민 사용법은 [공식 문서](https://docs.djangoproject.com/en/5.0/ref/contrib/admin/)를 참고하도록 하자

# 1.4 이 책에서 사용하는 라이브러리

만약 프로젝트에 새로운 오픈소스를 사용하고 싶다면, Github의 `Star`가 몇개정도 있는지 확인하자. 별 개수가 많을수록 사람들로부터 많은 관심도를 받고 있으며, 상대적으로 퀄리티가 좋은 소스코드라는 의미이기 때문이다.

이 책에서는 다음과 같은 라이브러리를 사용한다

## 1.4.1 Poetry

poetry의 사용법은 [공식문서](https://python-poetry.org/docs/)를 참고하거나, [필자가 작성한 글]()을 참고하자.

## 1.4.2 Mypy

> Mypy : 선택적 정적 타입 검사기
> [공식문서]()

파이썬은 변수의 타입을 딱히 엄격하게 정하지 않기 때문에 입문자 입장에서 아주 쉽게 배울 수 있는 언어지만, 이 점이 개발자의 실수를 유발하기도 한다.

현재 Javascript도 마찬가지로 타입에 대한 엄격하지 않은 언어임에도 실수를 방지하기 위해 타입을 엄격하게 제약한 `Typescript`로 넘어가는 추세이다.

> 타입을 엄격하게 제한하는 언어로는 C, Java, Cpp, Go 등이 있다.

> Typescript : 동적 타입의 언어인 javascript에 정적 타입을 사용할 수 있도록 기능이 추가된 언어

Python도 Javascript와 마찬가지로, 타입을 엄격하게 정해주는 mypy 기능을 제공해준다.

Mypy를 사용하지 않고, 다음처럼 코드를 작성하면, 딱히 문제가 발생하지 않는다.
```python
from typing import List
def func():
    num_list: List[int] = [1, 2, 3, 4, 5]
    num_list3: List[int | float] = [1, 1.2, 3, 4.55655]
    num_list3 = [3, 3, 4, 16, 65]  # 변수 타입을 안정함


def returntype(a1: str, a2: str) -> bool:
    return a1 == a2


def 크아악():
    num_list: List[int] = [1, 2, "3", 4, "띠용"]
    num_list2: List[int] = "1, 2, 3, 4"
    str1: str = [11, 22, 33]
    str2: str = "a,b,c,d,e"
    str2: List[str] = str2.split()

```

하지만 mypy를 적용하면 다음과 같이 에러가 발생한다.

![](https://velog.velcdn.com/images/calzone0404/post/f1502c6b-95cf-4785-8ffe-11d5f7666f44/image.png)


Mypy는 주로 실무에서 사용하는 코드 수준의 제약을 가하기 위해 사용한다. 하지만, 학습할 때 사용하는 것은 방해가될 수 있다.

## 1.4.3 black

개발자의 각자 다른 코드 스타일로 인한 통일성을 해치는 상황을 피하기 위해, 프로젝트의 모든 코드를 black 방식대로 강제로 포매팅해버리는 라이브러리이다.

```bash
poetry add black
```

![](https://velog.velcdn.com/images/calzone0404/post/baf57a39-3d2a-4144-a228-ce2b3dee15bf/image.png)

## 1.4.4 Django

이 책에서 다루는 내용이 장고이므로 생략

## 1.4.5 django REST framework (DRF)

백엔드 프로그래밍은 API로 프론트엔드와 데이터를 주고받는(통신하는) 방식으로 개발하기 때문에 Template을 잘 사용하지 않는다.

DRF는 API 방식으로 개발하기 위한 다양한 View(Controller) 구현체를 제공하고 있다.

## 1.4.6 django-filter

> 장고 템플릿을 개발할 때 데이터 검색 기능을 손쉽게 개발할 수 있도록 도와주는 라이브러리

템플릿 개발 시 유용하므로 나중에 찾아보도록 하자

## 1.4.7 django-extensions

`django-extensions`는 필수적인 라이브러리는 아니지만, 개발 편의성을 크게 향상시켜주는 다양한 기능을 제공한다. 아래에서 주요 기능을 소개한다.

### 설치 방법
먼저 `django-extensions`를 설치해야 한다.
```bash
poetry add django-extensions
```

설치 후 `INSTALLED_APPS`에 추가한다.
```python
# settings.py
INSTALLED_APPS = [
    ...
    'django_extensions',
    ...
]
```

### runserver_plus
기본적으로 Django 서버를 실행할 때는 다음과 같이 사용한다.
```bash
python manage.py runserver 8000
```

`django-extensions`의 `runserver_plus`를 사용하면 개발용 서버 시작 명령어와 서버 로그가 더 깔끔하게 출력된다.
```bash
python manage.py runserver_plus 8000
```

### shell_plus
또한, SQL 관련 명령어도 지원해준다. 기존 Django shell을 사용하면 모델을 일일이 import해주어야 한다.
```bash
python manage.py shell
```

하지만 `django-extensions`의 `shell_plus`를 사용하면 실제 서버 환경과 동일한 설정이 import된 shell을 제공한다.
```bash
python manage.py shell_plus --print-sql
```

### 추가 기능들
`django-extensions`는 이외에도 여러 유용한 기능들을 제공한다. 몇 가지 주요 기능을 소개하면 다음과 같다:

- **show_urls**: 프로젝트의 모든 URL 패턴을 출력한다.
  ```bash
  python manage.py show_urls
  ```

- **clear_cache**: 프로젝트의 캐시를 모두 삭제한다.
  ```bash
  python manage.py clear_cache
  ```

- **graph_models**: 프로젝트의 모델 구조를 시각화한다.
  ```bash
  python manage.py graph_models -a -o myapp_models.png
  ```

- **runscript**: 스크립트를 실행하기 위한 명령어를 제공한다.
  ```bash
  python manage.py runscript script_name
  ```

이 외에도 다양한 기능들이 있으며, 더 많은 정보는 [django-extensions 공식 문서](https://django-extensions.readthedocs.io/)에서 확인할 수 있다.

## 1.4.8 drf-spectacular

> OAS 지원 라이브러리 (OpenAPI Specification)
> 공식문서 : [링크](https://drf-spectacular.readthedocs.io/en/latest/)

필자는 flask의 flask-restx를 사용해서 API를 만들었을 때, swagger을 사용하여 API를 문서화하는 작업을 수행하였는데, 이와 동일한 기능을 제공하는 것이 django의 drf-spectacular이다.

DRF를 사용해서 API를 하나 만들면, 자동으로 API 문서를 생성해준다.

## 1.4.9 pyenv

> 원래 1.5절의 내용이지만, 프로젝트 세팅은 필자가 할 줄 알기 때문에 따로 뺐다. 만약 모른다면 pyenv + poetry를 사용하여 프로젝트 환경을 구성하는 블로그를 참고하자!

> pyenv는 다양한 버전의 파이썬을 관리해주는 도구이다.

필자는 ubuntu에 brew를 설치해놓아서 brew로 설치해주었다.
```bash
brew install openssl readline sqlite3 xz zlib
brew install pyenv
brew install pyenv-virtualenv
```

### 현재 로컬에 설치된 파이썬 확인
```bash
pyenv version
```

### 설치 가능한 python version list
```bash
pyenv install -list
```

### 특정 버전 python 설치
```bash
pyenv install 3.11.1
```

### 로컬 컴퓨터 내에서 사용할 파이썬 버전 설정
```bash
pyenv global 3.11.1
```

### 로컬 컴퓨터 내의 특정 폴더에서만 사용할 파이썬 버전 설정
```bash
# 원하는 프로젝트 디렉토리로 이동하고
pyenv local 3.11.1
```


