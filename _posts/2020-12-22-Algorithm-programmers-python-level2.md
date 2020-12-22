---
title: Programmers Python (level 2)
author: Sang Jun
date: 2020-12-22 22:38:31 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers]
pin: false
---



# [프린터](https://programmers.co.kr/learn/courses/30/lessons/42587)

분류 : 스택/큐

1. 우선순위 `priorities` 리스트의 최댓값을 `num_max`변수에 대입
2. `priorities[0]` 값이 최댓값일때만 `cnt` 1증가
3. 이때 `location`값이 0이면 break
4. `priorities[0]` 값이 최댓값아닐때 리스트 맨뒤에 `priorities[0]` 값 추가
5. 이때 `location`값이 0이면 `priorities`길이-1 값을 대입
6. 조건문과 상관없이 리스트 `priorities[0]` 삭제 및 `location` 1 감소
7. 위의 순서를 `location` 0이상일때 while문 반복실행

```python
def solution(priorities, location):
    cnt = 1
    while 0 <= location:
        num_max = max(priorities)
        if priorities[0] == num_max:
            if location == 0:
                break
            cnt += 1
        else:
            priorities.append(priorities[0])
            if location == 0:
                location = len(priorities)-1
        priorities.pop(0)
        location -= 1
    
    return cnt
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.2MB  
> max TaseCase : 1.00ms, 10.2MB  


<!--
# []()

분류 : 

1. 

```python

```

**2020**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase :   
> max TaseCase :   
-->

