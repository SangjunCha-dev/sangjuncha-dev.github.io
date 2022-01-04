---
title: "Django drf_yasg Swagger 기본 사용법"
date: 2021-09-24T09:23:07+09:00
description: Django drf_yasg Swagger 기본 사용법
tags: ["django", "swagger"]
categories: ["Django", "Swagger"]
---

---

`drf_yasg` : REST 프레임워크용 swagger/openAPI 문서 자동화 라이브러리
`swagger` : 개발자가 REST 웹 서비스를 설계, 빌드, 문서화, 테스트를 도와주는 오픈소스 소프트웨어 프레임워크

drf_yasg : 1.20.0 호환버전
- Django Rest Framework : 3.10, 3.11, 3.12
- Django : 2.2, 3.0, 3.1
- Python : 3.6, 3.7, 3.8, 3.9


라이브러리 설치

```
pip install -U drf-yasg
```

---


## 1. 프로젝트 생성

환경 : windwos 10

```bash
# 가상환경 생성 및 접속
> python -m venv venv
> venv\Scripts\activate

# 라이브러리 설치
(venv)> pip install django
(venv)> pip install djangorestframework
(venv)> pip install drf-yasg

# 프로젝트 및 앱 생성
(venv)> django-admin startproject config .
(venv)> django-admin startapp api
```

---


## 2. django 설정

`config/settings.py`

`staticfiles`앱은 swagger UI css/js 파일 배포용

```python
INSTALLED_APPS = [
    ...
    'django.contrib.staticfiles',
    'drf_yasg',
    ...
]
```

`config/urls.py`

```python
...
from rest_framework.permissions import AllowAny 
from drf_yasg.views import get_schema_view
from drf_yasg import openapi
from config import settings
...

schema_view = get_schema_view(
   openapi.Info(
      title="Snippets API",
      default_version='v1',
      description="Test description",
      terms_of_service="https://www.google.com/policies/terms/",
      contact=openapi.Contact(name="tester", email="test@test.com"),
      license=openapi.License(name="BSD License"),
   ),
   public=True,
   permission_classes=(AllowAny,),
)
...

# debug 모드일때 swagger api 실행
if settings.DEBUG:
    urlpatterns += [
        re_path(r'^swagger(?P<format>\.json|\.yaml)$', schema_view.without_ui(cache_timeout=0), name="schema-json"),
        re_path(r'^swagger/$', schema_view.with_ui('swagger', cache_timeout=0), name='schema-swagger-ui'),
        re_path(r'^redoc/$', schema_view.with_ui('redoc', cache_timeout=0), name='schema-redoc'),
        ...
    ]

```

---


## 3. 예시코드


### 3-1. models

`api/models.py`

```python
from django.db import models

# Create your models here.

class Musics(models.Model):
    ONE_STAR = 1
    TWO_STAR = 2
    THREE_STAR = 3
    STARS = (
        (ONE_STAR, '보통'),
        (TWO_STAR, '좋음'),
        (THREE_STAR, '매우 좋음'),
    )

    CATEGORY = (
        ('KPOP', '케이팝'),
        ('POP', '팝송'),
        ('CLASSIC', '클래식'),
        ('ETC', '기타 등등'),
    )

    id = models.BigAutoField(primary_key=True, verbose_name='music_id')
    created_at = models.DateTimeField(auto_now_add=True, verbose_name='생성 날짜')
    updated_at = models.DateTimeField(auto_now=True, verbose_name='수정 날짜')
    deleted_at = models.DateTimeField(blank=True, null=True, verbose_name='삭제 날짜')
    singer = models.CharField(null=False, max_length=128, verbose_name='가수명')
    title = models.CharField(null=False, max_length=128, verbose_name='곡명')
    category = models.CharField(blank=True, null=True, max_length=32, choices=CATEGORY, verbose_name='범주')
    star_rating = models.PositiveSmallIntegerField(blank=True, null=True, choices=STARS, default=ONE_STAR, verbose_name='곡 선호도')

    class Meta:
        # abstract = True  # sqlite3 사용 시 주석
        managed = True
        db_table = 'musics'
        app_label = 'api'
        ordering = ['star_rating', ]
        verbose_name_plural = '음악'
```

DB 마이그레이션

```bash
(venv)> py manage.py makemigrations
(venv)> py manage.py migrate
```

생성된 `sqlite3` 파일을 확인할려면 아래 링크에서 프로그램을 다운받아 설치한다. 개인적으로 no installer(portable) 버전을 추천한다.

- [DB Browser for SQLite](https://sqlitebrowser.org/dl/)

`db.sqlite3`

![](../images/django-swagger/django-swagger-1.png?raw=true)

musics 테이블 생성된 것을 확인할 수 있다.


### 3-2. seializers

`api/serializers.py`

```python
from rest_framework import serializers

from .models import Musics


class MusicSerializer(serializers.ModelSerializer):
    def validate(self, data: dict):
        return data

    class Meta:
        model = Musics
        fields = '__all__'  # 모든 필드 사용
        # fields = ('id', 'created_at', 'title', 'category', 'star_rating',)  # 특정 필드 지정

class MusicBodySerializer(serializers.Serializer):
    singer = serializers.CharField(help_text="가수명")
    title = serializers.CharField(help_text="곡 제목")
    category = serializers.ChoiceField(help_text="곡 범주", choices=('KPOP', 'POP', 'CLASSIC', 'ETC'))
    star_rating = serializers.ChoiceField(help_text="1~3 이내 지정 가능. 숫자가 클수록 좋아하는 곡", choices=(1, 2, 3), required=False)

class MusicQuerySerializer(serializers.Serializer):
    title = serializers.CharField(help_text="곡 제목으로 검색", required=False)
    star_rating = serializers.ChoiceField(help_text="곡 선호도로 검색", choices=(1, 2, 3), required=False)
    singer = serializers.CharField(help_text="가수명으로 검색", required=True)
    category = serializers.ChoiceField(help_text="카테고리로 검색", choices=('KPOP', 'POP', 'CLASSIC', 'ETC'), required=False)
    created_at = serializers.DateTimeField(help_text="입력한 날짜를 기준으로 그 이전에 추가된 곡들을 검색", required=False)
```


### 3-3. class views

`api/views.py`

```python
from django.shortcuts import get_object_or_404

from rest_framework import status
from rest_framework.views import APIView
from rest_framework.response import Response 
from drf_yasg import openapi
from drf_yasg.utils import swagger_auto_schema

from .serializers import *

class MusicListView(APIView):
    def get(self, request):
        serializer = MusicSerializer(Musics.objects.all(), many=True)

        response = Response(data=serializer.data)
        return response

    @swagger_auto_schema(request_body=MusicBodySerializer)  # Request Serializer 지정
    def post(self, request):
        # 중복 검사 없이 추가
        # serializer = MusicSerializer(data=request.data)
        
        # if not serializer.is_valid(raise_exception=False):
        #     return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)  

        # serializer.save()
        # response = Response(data=serializer.validated_data, status=status.HTTP_201_CREATED)
        # return response
        
        # 중복 검사 후 추가
        musics = Musics.objects.filter(**request.data)
        if musics.exists():
            return Response(status=status.HTTP_406_NOT_ACCEPTABLE)

        serializer = MusicSerializer(data=request.data, partial=True)
        if not serializer.is_valid():
            return Response(status=status.HTTP_406_NOT_ACCEPTABLE)

        music = serializer.save()

        response = Response(data=MusicSerializer(music).data, status=status.HTTP_201_CREATED)
        return response


class SearchMusicListView(APIView):
    @swagger_auto_schema(query_serializer=MusicQuerySerializer)
    def get(self, request):
        # filter 조건문 생성
        conditions = {
            'title__contains': request.GET.get('title', None),
            'star_rating': request.GET.get('star_rating', None),
            'singer__contains': request.GET.get('singer', None),
            'category': request.GET.get('category', None),
            'created_at__lte': request.GET.get('created_at', None),
        }
        conditions = {
            key: val for key, val in conditions.items() if val is not None
        }

        musics = Musics.objects.filter(**conditions)
        serializer = MusicSerializer(musics, many=True)
        response = Response(data=serializer.data, status=status.HTTP_200_OK)
        return response


class MusicView(APIView): 
    def get_object(self, pk):
        return get_object_or_404(Musics, pk=pk)

    def get(self, request, pk):
        serializer = MusicSerializer(Musics.objects.filter(id=pk), many=True)

        response = Response(data=serializer.data, status=status.HTTP_200_OK)
        return response

    @swagger_auto_schema(
        request_body=MusicBodySerializer,  # Request Serializer 지정
        manual_parameters=[openapi.Parameter(
            name='header_test',  # api 파라미터 이름
            in_=openapi.IN_HEADER,  # 파라미터 종류 (header = openapi.IN_HEADER)
            description="a header for test",  # 파라미터 설명
            type=openapi.TYPE_STRING  # header 지정시 필수 선언
        )],
    )
    def put(self, request, pk):
        object = self.get_object(pk=pk)
        serializer = MusicSerializer(object, data=request.data)

        if not serializer.is_valid(raise_exception=False):
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST) 

        serializer.save()
        response = Response(data=serializer.validated_data, status=status.HTTP_200_OK)
        return response

    def delete(self, request, pk):
        object = self.get_object(pk=pk)
        object.delete()

        response = Response(status=status.HTTP_204_NO_CONTENT)
        return response
```


### 3-4. urls

관리의 편의성을 위해 각 앱별로 urls.py 설정한다. 
앱 내부에서 urls 설정한 경우 프로젝트 urls 설정도 같이 해줘야 한다. 

`api/urls.py`

```
from django.urls import path 
from .views import MusicListView, SearchMusicListView, MusicView, PlayListView, SearchPlayListView

urlpatterns = [ 
    path("music/", MusicListView.as_view(), name="musics"),
    path("music/search/", SearchMusicListView.as_view(), name="music_search"),
    path("music/<int:pk>/", MusicView.as_view(), name="music"),
]
```

`config/urls.py`

```python
...
urlpatterns = [
    ...
    path("api/", include("api.urls")),
]
...
```

swagger 실행후 웹브라우저에서 `localhost:8000/swagger/` 주소로 접속한다.

```bash
(venv)> py manage.py runserver
```

![](../images/django-swagger/django-swagger-2.png?raw=true)

각 url class view에서 지정한 메소드들이 표시된다.

---


## 4. Swagger API 입력값 지정

swagger api 메소드에서 특정 입력값을 받기 위해서는 `@swagger_auto_schema` 데코레이터를 지정해야한다.


### 4-1. query_serializer

- query string 지정
- GET, DELETE Method 용도
- 메소드 입력값을 `request.query_params`, `request.GET`로 받는다.
- request 데이터를 변경하거나 추가할 경우 `copy()` 함수로 복사해서 사용해야 한다.
```python
# api/views.py 코드
@swagger_auto_schema(query_serializer=MusicQuerySerializer)
def get(self, request):
    data = request.query_params.copy()
    ...
```

- `Serializer`는 변수 선언을 통해서 다양한 타입의 변수를 선언할 수 있다.
- required 설정을 통해 필수입력, 선택입력을 지정한다.(기본값 True)
```python
# api/serializers.py 코드
class MusicQuerySerializer(serializers.Serializer):
    title = serializers.CharField(help_text="곡 제목으로 검색", required=False)
    star_rating = serializers.ChoiceField(help_text="곡 선호도로 검색", choices=(1, 2, 3), required=False)
    singer = serializers.CharField(help_text="가수명으로 검색", required=True)
    category = serializers.ChoiceField(help_text="카테고리로 검색", choices=('KPOP', 'POP', 'CLASSIC', 'ETC'), required=False)
    created_at = serializers.DateTimeField(help_text="입력한 날짜를 기준으로 그 이전에 추가된 곡들을 검색", required=False)
```

- swagger GET 메소드 예시

![](../images/django-swagger/django-swagger-3.png?raw=true)


### 4-2. request_body

- body 지정
- POST, PUT Method 용도
- 메소드 입력값을 `request.data`, `request.POST`로 받는다.

```python
# api/views.py 코드
@swagger_auto_schema(request_body=MusicBodySerializer)
def post(self, request):
    data = request.data.copy()
    ...
```

- `ModelSerializer`는 models.py 에서 지정한 모델의 column을 그대로 사용할 수 있다.
- Meta 클래스의 `fields` 설정에 따라 모든 column을 사용하거나 특정 column만 사용할 수 있다.

```python
# api/serializers.py 코드
class MusicSerializer(serializers.ModelSerializer):
    class Meta:
        model = Musics
        fields = '__all__'  # 모든 필드 사용
```

- Model 기본키인 id는 id serializer 변수를 직접 지정해야만 사용 가능하다.

```python
# api/serializers.py 코드
class MusicSerializer(serializers.ModelSerializer):
    id = serializers.IntegerField(min_value=1, help_text="ID")

    class Meta:
        model = Musics
        fields = ('id', 'created_at', 'title', 'category', 'star_rating',)  # 응답 필드 지정
```

- swagger POST 메소드 예시

![](../images/django-swagger/django-swagger-4.png?raw=true)


### 4-3. path value

- path value 지정
- 모든 메소드에서 사용가능하다.
- urls에서 지정한 변수명으로 입력값을 받는다.

```python
# api/urls.py
urlpatterns = [ 
    ...
    path("music/<int:pk>/", MusicView.as_view(), name="music"),
```

```python

class MusicView(APIView): 
    def delete(self, request, pk):
        ...
```

- swagger DELETE 메소드 예시

![](../images/django-swagger/django-swagger-5.png?raw=true)

---


## 5. 예시 코드 Git 

- [django-swagger](https://github.com/SangjunCha-dev/django_swagger)

---


## 참고(Reference)

- [drf-yasg - Yet another Swagger generator](https://drf-yasg.readthedocs.io/en/stable/readme.html)
- [drf-yasg-demo](https://drf-yasg-demo.herokuapp.com/cached/swagger/)
- [drf-yasg를 이용한 Swagger 문서 자동화](https://velog.io/@lu_at_log/drf-yasg-and-swagger)