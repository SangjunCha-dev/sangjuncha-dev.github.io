---
title: [django] QuerySet 객체 접근방식
author: Sangjun Cha
date: 2021-04-06 15:58:51 +0900
categories: [Django, Models]
tags: [django, models]
pin: false
---

queryset 단일객체와 다중객체 접근방법

# QuerySet 이란?

Database에서 전달받은 결과 목록(list)

Python 코드가 SQL 구문으로 맵핑(mapping)되고 DB로 전달하여 QuerySet 자료형으로 값을 응답받는다.

# 사전설정

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

# 객체별 접근방식
Database Table구조는 column(세로)과 row(가로)로 구분된다.

|id|name|age|
|---|---|---|
|1|Foo|15|
|2|Bar|20|

- column : 모델(model)클래스에서 지정한 속성
- row : 각 속성에 부여된 값

## 단일 객체

<클래스이름>.objects.get(id=1) or <클래스이름>.objects.latest('id')

- dictionary의 요소 하나를 반환한다.
- 해당 조건의 요소가 존재하지 않을때는 `DoesNotExist` 에러가 발생한다. 
- `.get()` 조건으로 여러개 존재할때는 `MultipleObjectsReturned` 에러가 발생한다.
- 객체 하나로 반환되기 때문에, `.`(dot notation)으로 접근할 수 있다. `<variable name>.name`

```python
>>> user = User.objects.get(id=1)
>>> user.name
'Foo'
```

## 다중 객체

<클래스이름>.objects.all() or <클래스이름>.objects.values() or <클래스이름>.objects.filter()

- dictionary 자료형의 key와 value로 접근가능
- `QuerySet()`은 리스트 형태이고, 리스트 요소는 dictionary 자료형이므로 `<variable name>[index]['key']` 형식으로 value에 접근할 수 있다.

```python
>>> user = User.objects.filter(name__startswith='C')
>>> <QuerySet [<User: User object (1)>, <User: User object (2)>]>
>>> user[0].name
'Foo'
```

- `filter()`는 조건식에 따라 필터링하여 query의 결과를 리스트로 반환한다.
- `all()`은 QuerySet안의 모든 객체를 리스트 형태로 반환한다.
- 따라서, `<variable name>[index]` 형식으로 접근할 수 있다.

```python
>>> user = User.objects.all()
<QuerySet [<User: User object (1)>, <User: User object (2)>]>
>>> user[1]
<User: User object (2)>
```