---
title: "Django 동적 모델 및 테이블 생성"
date: 2021-04-07T10:51:18+09:00
description: Django 동적 모델 및 테이블 생성
menu:
  sidebar:
    name: 동적 모델 및 테이블 생성
    identifier: django-create-dynamic-model
    parent: django
    weight: 30
tags: ["django", "models"]
categories: ["Django", "Models"]
---



# Django 동적 모델 및 테이블 생성

django는 makemigrations, migrate 명령어를 통하여 테이블 생성한다.  
이때 웹서버는 실행중일 경우 중단하고 다시 재시작한다.  

운영환경에 따라서 웹서버가 중단되지 않고 운영이 필요한경우에는 위의 방법이 아닌 동적 모델 및 테이블 생성방법이 필요하다.  



# 사용 방법

## 상속받을 추상모델 작성

models.py
```python
from django.db import models

class Board(models.Model):
	title = models.CharField(max_length=100)
	contents = models.CharField(max_length=500)
	
    class Meta:
        abstract = True
```

## 동적 모델 생성 및 삭제

```python
from .models import Board

TABLE_MAP = {}

def create_model(name: str):
	'''
	동적 모델 생성
	'''
	baseclass = Board
	tablename = board2
	# attrs 를 dict 형태로 지정한다.
	class Meta:
		db_table = tablename
	attrs = {
		'__module__': baseclass.__module__,
		'Meta': Meta,
	}
	# 클래스 타입을 지정하여 객체(클래스 명, 복사 참조 클래스, 속성값)를 지정한다. 
	TABLE_MAP[name] = type(name, (baseclass,), attrs)

def delete_model(name: str):
	'''
	동적 모델 삭제
	'''
	del TABLE_MAP[name]
```

## 동적 테이블 생성 및 삭제

```python
from django.db import connection

def create_table(name: str):
	'''
	동적 테이블 생성
	'''
	with connection.schema_editor() as schema_editor:
		schema_editor.create_model(TABLE_MAP[name])

def delete_table(name: str):
	'''
	동적 테이블 삭제
	'''
	with connection.schema_editor() as schema_editor:
		schema_editor.delete_model(TABLE_MAP[name])
```



# 참고사이트

추상클래스
- 모델 생성시 사용  
- https://dojang.io/mod/page/view.php?id=2389


closure 사용하기  
- 모델 생성시 `type()` 동작 원리  
- https://dojang.io/mod/page/view.php?id=2366


django 동적 모델 생성 (type 활용)  
- https://stackoverflow.com/questions/7320705/python-missing-class-attribute-module-when-using-type/7320926  


django 동적 테이블 생성 (connection.schema_editor)  
- https://docs.djangoproject.com/en/3.1/ref/schema-editor/#create-model