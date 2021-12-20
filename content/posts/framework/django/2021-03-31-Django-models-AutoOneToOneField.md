---
title: "Django models.AutoOneToOneField 사용법"
date: 2021-03-31T12:55:35+09:00
description: Django models.AutoOneToOneField 사용법
menu:
  sidebar:
    name: Models AutoOneToOneField 사용법
    identifier: django-models-autoOneToOneField
    parent: django
    weight: 30
tags: ["django", "models"]
categories: ["Django", "Models"]
---



model 상속할때 테이블 컬럼 데이터 자동 생성방법

# 라이브러리 설치

```bash
pip install django-annoying
```

# models 설정

django의 `models.OneToOneField`는 데이터 변경없이(default 설정) 저장하면 해당 데이터(row)가 생성되지않음

`models.AutoOneToOneField`는 데이터가 default값이어도 데이터(row) 생성됨

```python
from annoying.fields import AutoOneToOneField

# User모델 Save에서 제어
class User(models.Model):
    name = models.CharField(max_length=30)
    def save(self):
        is_new = False
        if self.pk is None:
            is_new = True

        if is_new:
            Profile.objects.create(user=self)

class Profile(models.Model):
    user = AutoOneToOneField(User, primary_key=True)
	home_page = models.URLField(max_length=255, blank=True)
    icq = models.IntegerField(blank=True, null=True)
```

# 참고사항

문제점 : `TypeError: __init__() missing 1 required positional argument: 'on_delete'` 에러 발생
- Django model에서 `ForeignKey` 사용하는 경우 발생하는 에러로 Django 2.0이상 버전에서는 파라미터 2개를 입력받음
- AutoOneToOneField > OneToOneField > ForeignKey 클래스 순으로 상속함
- Docs : [ForeignKey](https://docs.djangoproject.com/en/3.1/ref/models/fields/#foreignkey)

해결방법 : `on_delete` 파라미터 추가작성
```python
class Profile(models.Model):
    user = AutoOneToOneField(User, primary_key=True, on_delete=models.CASCADE)
```
- `models.CASCADE` 옵션 : RestrictedError(django.db의 하위클래스) 발생시켜 참조된 객체의 삭제를 방지함(무결성 오류 방지)
- `models.RESTRICT` 옵션 : CASCADE 관계를 통해 참조된 객체도 같이 삭제함
- Docs : [on_delete](https://docs.djangoproject.com/en/3.1/ref/models/fields/#django.db.models.ForeignKey.on_delete)
