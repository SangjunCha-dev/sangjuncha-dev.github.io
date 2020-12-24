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
2. `cursor`변수에 `priorities[0]`값 추출하여 대입
3. `cursor` 값이 최댓값 일때 `cnt` 1증가
4. 이때 `location`값이 0이면 break
5. `cursor` 값이 최댓값 아닐때 리스트 맨뒤에 `cursor` 값 추가
6. 이때 `location`값이 0이면 `location`변수에 `priorities`길이값 대입
7. `location` 1 감소
8. 위의 순서를 `location` 0 이상일때 while문 반복실행

```python
def solution(priorities, location):
    cnt = 1
    while True:
        num_max = max(priorities)
        cursor = priorities.pop(0)
        if cursor == num_max:
            if location == 0:
                return cnt
            cnt += 1
        else:
            priorities.append(cursor)
            if location == 0:
                location = len(priorities)
        location -= 1
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.99ms, 10.1MB  



# [기능개발](https://programmers.co.kr/learn/courses/30/lessons/42586)

분류 : 스택/큐

1. `progresses[0]+(speeds[0]*i)` 값이 100 이상 될 수 있는 `i`값을 `num`변수에 대입 후 for 문 break
2. `progresses`리스트 원소마다 `speeds[i]`*`num` 값 덧셈
3. 최대 `progresses`리스트 길이만큼 반복하여 `progresses[0]` 원소를 비교하는 for 문 실행
    - 해당 원소가 100 이상일 때 `cnt` 1증가 및 `progresses[0]`, `speeds[0]` 삭제,
    - 해당 원소가 100미만일 때 break
4. `cnt`변수값을 `answer`리스트에 추가
5. 위의 과정을 `progresses`리스트 값이 있다면 while 문 반복 실행

```python
def solution(progresses, speeds):
    answer = []
    
    while progresses:
        num = 0
        for i in range(1, 100):
            total = progresses[0]+(speeds[0]*i)
            if 100 <= total:
                num = i
                break
        
        for i in range(len(progresses)):
            progresses[i] += (speeds[i]*num)
        cnt = 0
        for _ in range(len(progresses)):
            if 100 <= progresses[0]:
                progresses.pop(0)
                speeds.pop(0)
                cnt += 1
            else:
                break
        answer.append(cnt)
            
    return answer
```

**2020-12-23**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.12ms, 10.1MB  



# [다리를 지나는 트럭](https://programmers.co.kr/learn/courses/30/lessons/42583)

분류 : 스택/큐

변수 정의
- bridge_length : 다리길이
- weight : 다리 설계하중
- truck_weights : 다리를 건널 트럭의 무게(값)와 순서(index)
- answer : 경과한 시간
- bredge_list : 다리를 건너는 중인 트럭의 목록과 위치 ex) [[트럭 무게, 트럭 위치], [트럭 무게, 트럭 위치], ...]
- cnt : 한 번에 이동할 거리

1. `bredge_list`리스트 값이 있을 때
    - `bredge_list[0][1]`값이 `bridge_length`보다 크다면
        - `weight`값에 `bredge_list[0][0]`값 추가
        - 첫 번째 트럭이 다리를 다 지나갔다면 다리가 견딜 수 있는 하중을 복구
        - `bredge_list[0]`삭제
        - 리스트에서 첫 번째 트럭 정보 삭제
2. `truck_weights`리스트 값이 있을 때
    - `truck_weights[0]`값(첫 번째 트럭)을 `truck`변수에 저장
    - `weight` - `truck`값이 0 이상 일 때
        - `weight`값에서 `truck`만큼 뺄셈
        - 견딜 수 있는 다리 하중 줄어듦
        - `bredge_list`리스트에 `[truck, 1]`값 추가
        - 다리를 건너는 중인 트럭 정보 추가 `무게`와 `위치`
        - `truck_weights[0]` 삭제
        - 진입 대기 중인 트럭 목록에서 첫 번째 트럭 정보 삭제
    - 다리에 트럭이 추가로 진입할 수 없을 때
        - `bredge_list[0][1]`값이 `bridge_length`보다 크기 위한 `cnt`값 구하기
        - 다리에 진입한 맨 앞의 트럭이 다리를 건너기 위해 덧셈할 값
3. `bredge_list`리스트의 트럭 위치값에 `cnt`값 덧셈
    - 다리에 진입한 트럭들의 위치 이동
4. `answer`값에 `cnt`값 덧셈
    - 다리에서 이동한 거리만큼 시간이 경과하였기에 `cnt`값으로 덧셈
5. `bredge_list`, `truck_weights` 둘 중 하나라도 값이 있으면 while 반복 실행

```python
def solution(bridge_length, weight, truck_weights):
    answer = 0
    bredge_list = []
    while bredge_list or truck_weights:
        # 트럭이 다리를 건넜을때
        if bredge_list:
            if bridge_length < bredge_list[0][1]:
                weight += bredge_list[0][0]
                del bredge_list[0]

        cnt = 1  # 한번에 이동할 거리
        # 트럭 진입
        if truck_weights:
            truck = truck_weights[0]
            # 진입할 트럭의 무게를 다리가 버틸수 있을때
            if 0 <= weight-truck:
                weight -= truck
                bredge_list.append([truck, 1])
                del truck_weights[0]
            else:
                # 트럭 추가 진입이 불가능할때 한번에 계산
                for i in range(1, bridge_length+1):
                    if bridge_length < (bredge_list[0][1] + i):
                        cnt = i
                        break

        # 트럭이 다리를 지나간 정도
        for i in range(len(bredge_list)):
            bredge_list[i][1] += cnt
        answer += cnt
    return answer
```

**2020-12-23**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.3MB  
> max TaseCase : 27.21ms, 10.3MB  



# [멀쩡한 사각형](https://programmers.co.kr/learn/courses/30/lessons/62048)

분류 : Summer/Winter Coding(2019)

## 방법1

1. `h`값이 정수로 나오기위한 최소 `w`값을 구하기 위해 최대공약수를 `gcd`변수에 대입
2. 이때 색칠된 픽셀의 갯수는 `w+h-1`개
3. `w+h-1` 곱하기 `gcd`값이 색칠된 사각형 갯수 `line_pixel`변수에 대입
4. 반환값은 사각형의 면적인 `w*h`에서 `line_pixel`값을 뺄셈하여 반환 

소인수를 소수로 구하는 방법으로 변경필요(속도문제)

```python
def solution(w,h):
    angle = w/h
    gcd = 1
    
    dict1 = prime(w)
    dict2 = prime(h)
    # 최대공약수
    for num in dict1:
        if num in dict2:
            gcd *= (num ** min(dict1[num], dict2[num]))
    
    line_pixel = ((w+h)/gcd-1) * gcd
    return w*h-int(line_pixel)

def prime(num):
    num_dict = {}
    i = 2
    while i <= num:
        if num%i == 0:
            if i in num_dict:
                num_dict[i] += 1
            else:
                num_dict[i] = 1
            num //= i
        else:
            i += 1
    return num_dict
```

**2020-12-24**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 6878.89ms, 10.1MB  

## 방법2

1. `h`값이 정수로 나오기위한 최소 `w`값을 구하기 위해 최대공약수를 math 라이브러리의 gcd 함수로 구하고 `gcd`변수에 대입
2. 이때 색칠된 픽셀의 갯수는 `w+h-1`개
3. `w+h-1` 곱하기 `gcd`값이 색칠된 사각형 갯수 `line_pixel`변수에 대입
4. 반환값은 사각형의 면적인 `w*h`에서 `line_pixel`값을 뺄셈하여 반환 

```python
import math

def solution(w,h):
    angle = w/h
    gcd = math.gcd(w,h)
    
    line_pixel = ((w+h)/gcd-1) * gcd
    return w*h-int(line_pixel)
```

**2020-12-24**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.3MB  
> max TaseCase : 0.01ms, 10.3MB  



# [조이스틱](https://programmers.co.kr/learn/courses/30/lessons/42860)

분류 : 탐욕법(Greedy)

1. 

```python
def solution(name):
    alphabet = ['A',\
                'B','C','D','E','F','G','H','I','J','K','L','M','N',\
                'Z','Y','X','W','V','U','T','S','R','Q','P','O']
    
    cnt_list = []
    cnt_a2, cnt_true = 0, True
    for i in range(len(name)):
        index = alphabet.index(name[i])
        cnt_list.append(index if index <= 13 else index-13)
        if i == 0:
            pass
        elif cnt_true and not index and (0 != i):
            cnt_a2 += 1
        else:
            cnt_true = False
    cnt_list.append(len(name)-1)
    
    cnt_a1, cnt_true = 0, True
    for i in range(len(name)-1, 0, -1):
        index = alphabet.index(name[i])
        if cnt_true and not index:
            cnt_a1 += 1
        else:
            break
    
    cnt1 = sum(cnt_list)-cnt_a1
    cnt2 = sum(cnt_list)-cnt_a2
    min_cnt = cnt1 if cnt1 < cnt2 else cnt2
    return min_cnt
```

**2020-12-24**

> 채점 결과  
> 합계: 90.9 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.02ms, 10.3MB  



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

