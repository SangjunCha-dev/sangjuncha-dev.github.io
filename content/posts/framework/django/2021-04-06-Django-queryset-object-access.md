---
title: "Django QuerySet 객체 접근방식 및 조회"
date: 2021-04-06T15:58:51+09:00
description: Django QuerySet 객체 접근방식 및 조회
# menu:
#   sidebar:
#     name: QuerySet 객체 접근방식 및 조회
#     identifier: django-queryset-object-access
#     parent: django
#     weight: 30
tags: ["django", "models"]
categories: ["Django", "Models"]
---


---

## 1. QuerySet 이란?

Database에서 응답받은 결과 목록(list)으로 Python 코드가 SQL 구문으로 맵핑(mapping)되고 DB로 전달하여 받은 응답값을 QuerySet 자료형으로 반환한다.


---

## 2. 사전설정

django version : 3.2

models.py
```python
from django.db import models

class User():
    name = models.CharField(max_length=20)
    age = models.models.PositiveSmallIntegerField()
```

```bash
>>> python manage.py shell #shell 실행
>>> from user.models import User #모델클래스 임포트
>>> user1 = User(name='Foo', age=15) # 데이터추가
>>> user1.save() # 데이터 저장
>>> user2 = User(name='Bar', age=20) # 데이터추가
>>> user2.save() # 데이터 저장
```


---

## 3. 객체별 접근방식
Database Table구조는 column(세로)과 row(가로)로 구분된다.

|id|name|age|
|---|---|---|
|1|Foo|15|
|2|Bar|20|

- column : 모델(model)클래스에서 지정한 속성
- row : 각 속성에 부여된 값

### 3.1. 단일 객체

```
<모델클래스>.objects.get(id=1) or <모델클래스>.objects.latest('id')
```

- DB테이블 데이터의 행 하나를 dictionary 자료형으로 반환한다.
- 해당 조건의 요소가 존재하지 않을때는 `DoesNotExist` 에러가 발생한다. 
- `.get()` 조건으로 여러개 존재할때는 `MultipleObjectsReturned` 에러가 발생한다.
- 객체 하나로 반환되기 때문에, `.`(dot notation)으로 접근할 수 있다. `<variable name>.name`

```python
>>> user = User.objects.get(id=1)
>>> user.name
'Foo'
```

### 3.2. 다중 객체

#### all

```
<모델클래스>.objects.all()
```

- `all()`은 QuerySet안의 모든 객체를 list 자료형으로 반환한다.
- 따라서, `<variable name>[index]` 형식으로 접근할 수 있다.

```python
>>> user = User.objects.all()
>>> user
<QuerySet [<User: User object (1)>, <User: User object (2)>]>
>>> user[1]
<User: User object (2)>
```

#### filter

```
<모델클래스>.objects.filter()
```

- `filter()`는 조건식에 따라 필터링하여 query의 결과를 list 자료형으로 반환한다.
- `QuerySet()`은 list 형태이고, list 요소는 dictionary 자료형이다.
- `<variable name>[index][key]` 형식이로 key를 통해 value에 접근할 수 있다.

```python
>>> user = User.objects.filter(name__startswith='F')
>>> user
<QuerySet [<User: User object (1)>]>
>>> user[0].name
'Foo'
```

### 3.3. 결과 조회

입력한 컬럼에 대한 값만 조회한다. 

#### values

```
<모델클래스>.objects.values()
```

객체정보가 아닌 dictionary와 list의 자료형의 값으로 반환한다.

```python
>>> User.objects.values('name', 'age')
<QuerySet [{'name': 'Foo', 'age': 15}, {'name': 'Bar', 'age': 20}]>
```

#### valies_list

```
<모델클래스>.objects.values_list()
```

- 각 ID값이 분리된 2차원 list 자료형으로 반환한다.

```python
>>> User.objects.values_list('name', 'age')
<QuerySet [('Foo', 15), ('Bar', 20)]>
```

- flat 옵션 사용시 필드 조회 결과값을 1차원 list 자료형으로 반환한다.
- 2개이상의 필드 입력시 `TypeError` 에러가 발생한다.

```python
>>> User.objects.values_list('name', flat=True)
<QuerySet ['Foo', 'Bar']>
```

#### order_by

```
<모델클래스>.objects.values().order_by()
```

조회된 결과값을 특정 필드명을 기준으로 정렬하여 반환한다.

- order_by 에서 지정한 필드명 앞에 `-`붙이면 `내림차순 정렬`된다.

```python
>>> User.objects.values('id', 'name').order_by('id')
<QuerySet [{'id': 1, 'name': 'Foo'}, {'id': 2, 'name': 'Bar'}]>

>>> User.objects.values('id', 'name').order_by('-id')
<QuerySet [{'id': 2, 'name': 'Bar'}, {'id': 1, 'name': 'Foo'}]>
```

#### 특정 갯수 조회

```
<모델클래스>.objects.values()[검색시작 index : 검색종료 index]
```

검색 쿼리의 범위 지정할 수 있다.

- list index 형식과 동일하다.
- queryset 구문을 sql 질의문으로 변환 `str(queryset.query)` 하여 확인하면 SQL 쿼리문 끝에 limit 사용한 것을 볼 수 있다.

```python
>>> User.objects.values('name', 'age')[0:1]
<QuerySet [{'id': 1, 'name': 'Foo'}]>

>>> User.objects.values('name', 'age')[:1]
<QuerySet [{'id': 1, 'name': 'Foo'}]>

>>> User.objects.values('name', 'age')[1:2]
<QuerySet [{'id': 2, 'name': 'Bar'}]>
```
