---
title: "클래스 인스턴스 변수(self)와 함수 로컬변수 처리속도차이"
date: 2021-05-24T10:34:10+09:00
description: 클래스 인스턴스 변수(self)와 함수 로컬변수 처리속도차이
# menu:
#   sidebar:
#     name: 변수 class vs local 처리속도차이
#     identifier: python-variable-class-vs-local
#     parent: python
#     weight: 30
tags: ["python"]
categories: ["Python"]
---


---

설치버전 : python 3.8.8

## 1. 클래스 인스턴스 변수(self)와 함수 매개변수 처리속도 차이

### 1.1. 예제 코드

```python
import timeit

class CheckTime:
    def __init__(self):
        self.a = 0
        self.b = 0
        self.c = 0

    def process_self(self):
        self.a + self.b + self.c

    def process_argument(self, a, b, c):
        a + b + c

    def get_time(self):
        self.a = 1
        self.b = 2
        self.c = 3

        def test1():
            self.process_self()
        process_self_time = timeit.Timer(stmt=test1).repeat(number=1000000)

        a = self.a
        b = self.b
        c = self.c
        def test2():
            self.process_argument(a, b, c)
        process_argument_time = timeit.Timer(stmt=test2).repeat(number=1000000)

        print(f"Self value process = {process_self_time} sec")
        print(f"Argument value process = {process_argument_time} sec")

checkTime = CheckTime()
checkTime.get_time()
```

### 1.2. 결과

```bash
Self value process = [0.1872074, 0.1858419, 0.1853262, 0.1846126, 0.1845041] sec
Argument value process = [0.147073, 0.1466343, 0.1458009, 0.1452553, 0.1468781] sec
```

결론 : 함수 매개변수는 클래스 인스턴스 변수를 사용하는 것보다 약 35 % 빠름

이유  
- 사전 조회없이 위치 인수를 전달하고 액세스 할 수 있기 때문이다. 
- 클래스 인스턴스 변수에는 할당과 액세스 모두에 대한 사전 검색이 필요하다. 
- 함수 매개변수는 이름으로 색인이 지정된 사전이 아니라 배열에 저장되고 색인으로 액세스된다.



## 2. 클래스 인스턴스 변수(self)와 함수 로컬변수 처리속도 차이

### 2.1. 예제 코드

```python
import timeit

class CheckTime:
    def __init__(self):
        self.a = 0
        self.b = 0
        self.c = 0

    def get_time(self):
        self.a = 1
        self.b = 2
        self.c = 3

        def test1():
            for _ in range(1000000):
                self.a + self.b + self.c
        process_self_time = timeit.Timer(stmt=test1).repeat(number=1)

        a = self.a
        b = self.b
        c = self.c
        def test2():
            for _ in range(1000000):
                a + b + c
        process_argument_time = timeit.Timer(stmt=test2).repeat(number=1)

        print(f"Self value process = {process_self_time} sec")
        print(f"Argument value process = {process_argument_time} sec")

checkTime = CheckTime()
checkTime.get_time()
```

### 2.2. 결과

```bash
Self value process = [0.0758095, 0.0757394, 0.0756023, 0.0746107, 0.0749142] sec
Argument value process = [0.0332198, 0.0332008, 0.0332592, 0.0331674, 0.0332719] sec
```

결론 : 함수 로컬변수는 클래스 인스턴스 변수를 사용하는 것보다 약 120 % 빠름  

이유 : 클래스 인스턴스 변수에는 할당과 액세스 모두에 대한 사전 검색이 필요하다. 


## 참고(Reference)

- [python - 인스턴스 변수 및 함수 인수의 처리 속도](https://www.python2.net/questions-819739.htm)