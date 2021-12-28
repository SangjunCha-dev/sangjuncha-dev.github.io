---
title: "Programmers Python (level 3)"
date: 2021-12-20T16:05:36+09:00
description: Programmers Python (level 3) 문제풀이 
tags: ["algorithm", "programmers", "python"]
categories: ["Algorithm", "Programmers"]
---



## 다단계 칫솔 판매

분류 : 2021 Dev-Matching: 웹 백엔드 개발자(상반기)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/77486)

### 방법1

```python
def solution(enroll, referral, seller, amount):
    answer = []

    data = {'center': {'amount': 0,'parent': None}}
    for i in range(len(enroll)):
        data[enroll[i]] = {
            'amount': 0,
            'parent': referral[i] if referral[i] != '-' else 'center'
        }

    for i in range(len(seller)):
        sell = seller[i]
        price = amount[i] * 100
        data[sell]['amount'] += price-price//10
        price = price//10

        while data[sell]['parent'] and price:
            sell = data[sell]['parent']
            if sell == 'center':
                data[sell]['amount'] += price
                break
            data[sell]['amount'] += price-price//10
            price = price//10

    del data['center']
    for value in data.values():
        answer.append(value['amount'])

    return answer
```

**2021-12-20**

> min TaseCase : 0.08ms, 10.2MB  
> max TaseCase : 167.55ms, 22.2MB  


### 방법2

1. 동일한 index에 접근하는 두개의 리스트는 zip함수로 묶어서 동시에 처리한다.
2. 원하는 값을 바로 반환할수 있게 dict자료형을 2개로 분할한다.
3. while문 동작을 do-while문처럼 동작시킨다.

```python
def solution(enroll, referral, seller, amount):
    money, parent = {}, {}
    for e, r in zip(enroll, referral):
        money[e] = 0
        parent[e] = r

    for s, a in zip(seller, amount):
        price = a * 100
        while True:
            total_price = price
            price = total_price//10
            money[s] += total_price-price
            s = parent[s]
            if (s == '-') or (not price):
                break

    return list(money.values())
```

**2021-12-27**

> min TaseCase : 0.01ms, 10.3MB  
> max TaseCase : 92.37ms, 21.2MB  



<!--
## 

분류 : 

[문제 링크]()

1. 

```python

```

**2021**

> min TaseCase :   
> max TaseCase :   
-->

