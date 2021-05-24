---
title: 클래스 인스턴스 변수(self)와 함수 로컬변수 처리속도차이
author: Sangjun Cha
date: 2021-05-24 10:34:10 +0900
categories: [Python]
tags: [python]
pin: false
---



설치버전 : python 3.8.8

# 클래스 인스턴스 변수(self)와 함수 매개변수 처리속도 차이

## 예제 코드 1

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

## 결과

```bash
Self value process = [0.1872074, 0.18584199999999998, 0.1853262, 0.18461269999999996, 0.18450410000000006] sec
Argument value process = [0.147073, 0.14663439999999994, 0.1458009, 0.1452553000000001, 0.14687819999999996] sec
```

결론 : 함수 매개변수는 클래스 인스턴스 변수를 사용하는 것보다 약 35 % 빠름

이유  
- 사전 조회없이 위치 인수를 전달하고 액세스 할 수 있기 때문이다. 
- 클래스 인스턴스 변수에는 할당과 액세스 모두에 대한 사전 검색이 필요합니다. 
- 함수 매개변수는 이름으로 색인이 지정된 사전이 아니라 배열에 저장되고 색인으로 액세스된다.



# 클래스 인스턴스 변수(self)와 함수 로컬변수 처리속도 차이

## 예제 코드

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

## 결과

```bash
Self value process = [0.0758095, 0.07573940000000001, 0.07560230000000001, 0.0746107, 0.07491429999999999] sec
Argument value process = [0.03321980000000002, 0.03320089999999998, 0.03325929999999999, 0.033167400000000014, 0.03327199999999997] sec
```

결론 : 함수 로컬변수는 클래스 인스턴스 변수를 사용하는 것보다 약 120 % 빠름  

이유 : 예제 1번과 비슷한 이유


# 참고 사이트
- [python - 인스턴스 변수 및 함수 인수의 처리 속도](https://www.python2.net/questions-819739.htm)