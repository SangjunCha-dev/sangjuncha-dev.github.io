---
title: "Django model 참조 객체 호출하기"
date: 2021-04-07T17:34:37+09:00
description: Django model 참조 객체 호출하기
# menu:
#   sidebar:
#     name: Model 참조 객체 호출하기
#     identifier: django-model-reference-object
#     parent: django
#     weight: 30
tags: ["django", "models"]
categories: ["Django", "Models"]
---


---

## 1. 정참조와 역참조 객체 호출

데이터베이스의 임의의 테이블로 `User`, `Occupation` 생성한다.  
두 테이블은 `User` 모델의 객체가  `Occupation` 모델의 객체를 N:1 참조관계를 가진다.

`models.py` 설정

```python
class User(models.Model):
    name	= models.CharField(max_length=50)
	age		= models.IntegerField()
    job		= models.ForeignKey('Occupation', on_delete=models.CASCADE)
    	
class Occupation(models.Model):
    name = models.CharField(max_length=50)
```


---

## 2. 정참조 객체 호출하기

`user1` 객체가 `Occupation` 모델을 참조키(ForeignKey)로 정참조하여, `Occupation` 모델의 속성을 사용할 수 있다.

```bash
user1 = User.objects.get(id = 1)
user1.job.name
>>> 'Developer'
```


---

## 3. 역참조 객체 호출하기

역참조 관계에서는 정참조와 같이 바로 참조한 모델의 속성을 사용할 수 없다.

하지만 별도의 방법을 사용하면 역참조한 모델의 속성을 사용할 수 있다.

`jobs`객체를 참조키(ForeignKey)로 참조하고있는 'User' [classname(소문자)_set] 모델에 객체를 호출할 수 있다.  


```bash
jobs = Occupation.objects.get(id=1)
jobs.user_set.get().age
>>> 20
```

### 3.1. 1:N 참조 관계
- 역참조 목록 list로 반환
```
[역참조할 모델 인스턴스].[정참조한 모델 클래스]_set.all()
```
- 특정 조건을 만족하는 역참조 목록 list로 반환
```
[역참조할 모델 인스턴스].[정참조한 모델 클래스]_set.filter(참조키=조건값)
```

### 3.2. 1:1 참조 관계

- 역참조 항목 반환
```
[역참조할 모델 인스턴스].[정참조한 모델 클래스]_set.all()
```
- 특정 조건을 만족하는 역참조 항목 반환
```
[역참조할 모델 인스턴스].[정참조한 모델 클래스]_set.filter(참조키=조건값)
```