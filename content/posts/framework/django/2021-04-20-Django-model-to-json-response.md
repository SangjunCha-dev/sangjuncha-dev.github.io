---
title: "Django json 데이터 반환하기"
date: 2021-04-20T09:30:40+09:00
description: Django json 데이터 반환하기
menu:
  sidebar:
    name: Django Return json Data
    identifier: django-return-json-data
    parent: django
    weight: 30
tags: ["django", "models"]
categories: ["Django", "Models"]
---



api 서버 등 json 데이터로 통신하는 서버에서 사용할 수 있는 예제이다.

# 사전 설정

프로젝트 생성 후 user 앱의 User 모델을 생성한다.  
임의의 user 데이터를 2개 등록한다.

models.py
```python
class User(models.Model):
    id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=20, unique=True)
```

# 사용 예시

## QuerySet(Model Instance)을 json형태로 변환할 경우 `.all()`

serializers.serialize('json', value_name) 함수를 사용하여 json 형태로 변환시킨다.

views.py
```python
from django.core import serializers

res_data = User.objects.all()
# <QuerySet [<User: User object (1)>, <User: User object (2)>]>

res_data = serializers.serialize('json', res_data)
return HttpResponse(res_data, content_type="text/json-comment-filtered")
```

`출력결과`

```text
[
    {
        "model": "user.user",
        "pk": 1,
        "fields": {
            "name": "KimTester",
        }
    },
    {
        "model": "user.user",
        "pk": 2,
        "fields": {
            "name": "LeeTester",
        }
    }
]
```

## QuerySet(dict)을  json형태로 변환할 경우 `.values()`

QuerySet 자료형은 json 내장함수를 사용할 수 없어 QuerySet를 list형으로 변환시킨후 

json.dumps() 함수를 통해 json형태로 변환시킨다.

views.py
```python
import json

res_data = Template.objects.values('name')
# res_data = <QuerySet [{'name': 'KimTester'}, {'name': 'LeeTester'}]>

res_data = json.dumps(list(res_data))
return HttpResponse(res_data, content_type="text/json-comment-filtered")
```

`출력결과`

```text
[
    {
        "name": "KimTester"
    },
    {
        "name": "LeeTester"
    }
]
```
