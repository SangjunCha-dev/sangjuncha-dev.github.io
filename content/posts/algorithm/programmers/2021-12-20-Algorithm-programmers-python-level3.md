---
title: "Programmers Python (level 3)"
date: 2021-12-20T16:05:36+09:00
description: Programmers Python (level 3) 문제풀이 
tags: ["algorithm", "programmers", "python"]
categories: ["Algorithm", "Programmers"]
---


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


---

## 표 편집

분류 : 2021 카카오 채용연계형 인턴십

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/81303)

1. linked list 방식으로 풀이

```python
def solution(n, k, cmd):
    # 초기 설정
    answer_dict = dict()
    for i in range(n):
        answer_dict[i] = [i-1, i+1]
    answer_dict[0][0] = None
    answer_dict[n-1][1] = None

    stack_del = list()

    for c in cmd:
        if c[0] == "U":  # Up
            move = int(c[2:])

            while move != 0:
                k = answer_dict[k][0]
                move -= 1

        elif c[0] == "D":  # Down
            move = int(c[2:])

            while move != 0:
                k = answer_dict[k][1]
                move -= 1

        elif c[0] == "C":  # 삭제
            stack_del.append([k, answer_dict[k]])

            front = answer_dict[k][0]
            back = answer_dict[k][1]

            if front != None:
                answer_dict[front][1] = back
            if back != None:
                answer_dict[back][0] = front

            k = back if back != None else front

        elif c[0] == "Z":  # 복구
            recovery = stack_del.pop()
            answer_dict[recovery[0]] = recovery[1]

            key = recovery[0]
            front = recovery[1][0]
            back = recovery[1][1]

            if front != None:
                answer_dict[front][1] = key
            if back != None:
                answer_dict[back][0] = key

    # 정답
    answer = ['O' for _ in range(n)]
    for key, _ in stack_del:
        answer[key] = 'X'
    answer = ''.join(answer)

    return answer
```

**2022-03-26**

> 정확성  
> min TaseCase : 0.03ms, 10.4MB  
> max TaseCase : 5.64ms, 10.6MB  
> 
> 효율성  
> min TaseCase : 235.29ms, 77.6MB  
> max TaseCase : 906.85ms, 232MB  


---

## 네트워크

분류 : 깊이/너비 우선 탐색(DFS/BFS)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/43162)

### 방법

1. 너비우선탐색(BFS)구조의 재귀함수로 풀이

**추가한 테스트**

|n|computers|return|
|---|---|---|
|8|[[1, 0, 0, 1, 0, 0, 0, 0], [0, 1, 0, 0, 1, 0, 0, 0], [0, 0, 1, 0, 0, 1, 0, 0], [1, 0, 0, 1, 0, 0, 1, 0], [0, 1, 0, 0, 1, 0, 0, 1], [0, 0, 1, 0, 0, 1, 0, 0], [0, 0, 0, 1, 0, 0, 1, 0], [0, 0, 0, 0, 1, 0, 0, 1]]|3|
|4|[[1, 1, 0, 1], [1, 1, 0, 0], [0, 0, 1, 1], [1, 0, 1, 1]]|1|


```python
def solution(n, computers):
    return find_node(computers)

def find_node(computers):
    for i, computer1 in enumerate(computers):
        for j in range(i+1, len(computers)):
            for k, l in zip(computer1, computers[j]):

                # and 연산으로 같은 네트워크 인지 검증
                if k and l:
                    temp_node = []
                    for val1, val2 in zip(computers[j], computer1):
                        # 같은 네트워크 이면 or 연산으로 네트워크 확장
                        temp_node.append(val1 or val2)
                    computers[i] = temp_node

                    # 병합된 연결정보 삭제
                    del computers[j]

                    # 연결정보가 갱신되었으므로 처음부터 다시 연결정보 탐색
                    return find_node(computers)
    return len(computers)
```

**2022-03-27**

> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 1.04ms, 10.4MB  


---

## 가장 먼 노드

분류 : 그래프

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/49189)

1. 우선순위 큐를 적용한 다익스트라 최단 거리 알고리즘을 사용한다.
2. 다익스트라 알고리즘은 간선의 방향성을 체크하므로 무방향성을 위해 입력받은 vertex 연결 정보를 양방향으로 만들고 진행한다.

```python
import heapq

def dijkstra(start, distance, graph):
    q = []

    heapq.heappush(q, (0, start))
    distance[start] = 0

    while q:
        dist, now = heapq.heappop(q)

        if distance[now] < dist:
            continue

        for i in graph[now]:
            cost = dist + i[1]

            if cost < distance[i[0]]:
                distance[i[0]] = cost
                heapq.heappush(q, (cost, i[0]))

def solution(n, edge):
    answer = 0
    start = 1
    INF = int(1e9)

    graph = [[] for _ in range(n+1)]
    distance = [INF for _ in range(n+1)]
    
    for i, j in edge:
        graph[i].append((j, 1))
        graph[j].append((i, 1))

    dijkstra(start, distance, graph)

    distance_list = []
    for i in range(1, n+1):
        if distance[i] == INF: continue
        distance_list.append(distance[i])

    answer = distance_list.count(max(distance_list))

    return answer
```

**2022-03-29**

> min TaseCase : 0.03ms, 10.2MB  
> max TaseCase : 120.05ms, 27.2MB  


---

## 정수 삼각형

분류 : 동적계획법(Dynamic Programming)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/43105)

1. 동적계획법 방식으로 문제 풀이한다.

```python
import copy

def solution(triangle):
    n = len(triangle)
    dp = [0 for _ in range(len(triangle[-1]))]

    for i in range(n):
        array = copy.deepcopy(dp)
        for j, k in enumerate(triangle[i]):
            if j == 0:
                dp[j] += k
                continue

            dp[j] = max(array[j], array[j-1]) + k

    return max(dp)
```

**2022-03-29**

> **정확성**  
> min TaseCase : 0.07ms, 10.2MB  
> max TaseCase : 6.33ms, 10.5MB  
>   
> **효율성**  
> min TaseCase : 76.97ms, 13.3MB  
> max TaseCase : 112.80ms, 14.9MB  


---

## 등굣길

분류 : 동적계획법(Dynamic Programming)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42898)

### 방법1

1. 동적계획법 방식으로 문제풀이
2. 아래 풀이는 문제에서 요구한 최단경로의 갯수만 연산하는 최적화가 필요함

```python
def solution(m, n, puddles):
    INF = int(1e9)

    dp = [[[INF,0] for _ in range(m)] for _ in range(n)]

    # puddles 위치 indexr에 맞도록 수정 (입력값 위치(x, y) 주의!)
    puddles = [[puddle[0]-1, puddle[1]-1] for puddle in puddles]

    for h in range(n):
        for w in range(m):           

            # 침수 지역일 경우 다음 단계로
            if [h,w] in puddles:
                continue

            if 0 <= h-1 < n:
                value_y = dp[h-1][w][0]+1
            else:
                value_y = INF

            if 0 <= w-1 < m:
                value_x = dp[h][w-1][0]+1
            else:
                value_x = INF
            
            # 최단 경로의 개수
            if value_y < value_x:
                value = value_y
                count = dp[h-1][w][1]
            elif value_x < value_y:
                value = value_x
                count = dp[h][w-1][1]
            else:
                value = value_y
                count = dp[h-1][w][1] + dp[h][w-1][1]

            # 처음위치일 경우
            if (value_x is INF) and (value_y is INF):
                value = 0
                count = 1

            dp[h][w] = [value, count]

    return dp[n-1][m-1][1] % 1000000007
```

**2022-03-30**

> **정확성**  
> min TaseCase : 0.01ms, 10.3MB  
> max TaseCase : 0.21ms, 10.2MB  
>   
> **효율성**  
> min TaseCase : 2.56ms, 10.3MB  
> max TaseCase : 7.37ms, 10.9MB  


### 방법2

1. 오른쪽, 아래로만 이동가능하므로 무조건 최단경로를 만족하게됨
2. 최단경로 계산이 아닌 갈수 있는 경우의 수 연산으로 수정하면 최적화 가능


---

## 이중우선순위큐

분류 : 힙(Heap)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42628)

1. `이중` 우선순위 큐를 사용해야 하므로 heap 자료구조를 2개 선언하여 풀이한다.
    - python은 heap 라이브러리가 최소힙으로 동작한다.

**추가 테스트 케이스**

|operations|result|
|---|---|
|["I 4", "I 3", "I 2", "I 1", "D 1", "D 1", "D -1", "D -1", "I 5", "I 6"]|[6,5]|

```python
import heapq

def solution(operations):
    answer = []
    count = 0
    values_min, values_max = [], []

    for operation in operations:
        op, value = operation.split()
        value = int(value)

        if op == 'I':
            # pyhton은 최소힙으로 기본 동작
            heapq.heappush(values_min, value)
            heapq.heappush(values_max, -value)
            count += 1

        elif op == 'D' and (0 < count):
            if value < 0:
                heapq.heappop(values_min)
            else:
                heapq.heappop(values_max)
            count -= 1

            # 모두 삭제된 경우로 초기화
            if count == 0:
                values_min, values_max = [], []

    if 0 < count:
        answer = [-heapq.heappop(values_max), heapq.heappop(values_min)]
    else:
        answer = [0, 0]

    return answer
```

**2022-03-30**

> min TaseCase : 0.02ms, 10.4MB  
> max TaseCase : 0.03ms, 10.4MB  


---

## 디스크 컨트롤러

분류 : 힙(Heap)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42627)

1. 힙구조를 이용한 풀이
2. 디스크요청의 정렬이 필요하고 요청사이의 idle 시간도 주의한다.

**추가 테스트 케이스**

|jobs|result|
|---|---|
|[[0, 1], [1, 1], [5, 7]]|3|

```python
import heapq

def solution(jobs):
    answer = 0
    sequences = {}
    heap = []

    for job in jobs:
        if job[0] in sequences:
            sequences[job[0]].append(job[1])
        else:
            sequences[job[0]] = [job[1]]

    now_time, process_time = 0, 0
    while sequences or heap:
        # 디스크 작업 요청
        if now_time in sequences:
            for sequence in sequences[now_time]:
                heapq.heappush(heap, (sequence, now_time))
            del sequences[now_time]

        # 처리중인 작업이 없을 경우
        if (not process_time) and heap:
            process_time, request_time = heapq.heappop(heap)
            # 처리된 시간 = 현재시간 - 요청시간 + 소요시간
            answer += now_time - request_time + process_time

        if 0 < process_time:
            process_time -= 1
        now_time += 1

    answer = answer // len(jobs)
    return answer
```

**2022-03-30**

> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 24.18ms, 10.3MB  


---

## 단어 변환

분류 : 깊이/너비 우선 탐색(DFS/BFS)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/43163)

1. DFS 문제로 재귀함수를 사용하여 풀이한다.

```python
def word_conversion(begin, target, nodes, stack):
    count = 0
    stack.append(begin)

    # target과 동일한 경우
    if target == begin:
        return len(stack)-1

    path_count = []
    for i in range(len(target)):
        w = f"{begin[:i]}{begin[i+1:]}"

        if w in nodes[i]:
            for word in nodes[i][w]:
                if word not in stack:
                    conversion_result = word_conversion(word, target, nodes, stack)
                    if conversion_result != 0:
                        path_count.append(conversion_result)
                    stack.pop()

    if path_count:
        count = min(path_count)

    return count

def solution(begin, target, words):
    answer = 0
    length = len(target)
    nodes = {i: {} for i in range(length)}

    # 연결관계 정립
    for word in words:
        for i in range(length):
            w = f"{word[:i]}{word[i+1:]}"
            if w in nodes[i]:
                nodes[i][w].append(word)
            else:
                nodes[i][w] = [word]
    
    # DFS -> 재귀
    stack = []
    answer = word_conversion(begin, target, nodes, stack)

    return answer
```

**2022-03-30**

> min TaseCase : 0.02ms, 10.2MB  
> max TaseCase : 0.23ms, 10.3MB  


---

## 단속카메라

분류 : 탐욕법(Greedy)

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42884)

1. routes를 `진입지점 <= 진출지점` 되도록 정렬한다.
2. 저장된 진출지점보다 현재 진입지점이 뒤에 위치할 경우 새로운 단속 카메라 설치한다.
3. 이때 현재 진출지점이 저장된 진출지점보다 앞에 위치할 경우 저장된 진출지점을 재설정한다.

**추가한 테스트**

|routes|return|
|---|---|---|
|[[10,50],[15,20],[40,60]]|2|


```python
def solution(routes):
    # 진입 지점과 진출 위치 정렬   
    sort_routes = sorted(routes, key=lambda x: x[1])

    # 나갈 지점 설정
    exit_point = sort_routes[0][1]
    answer = 1

    for route in sort_routes[1:]:
        # 저장된 진출지점보다 현재 진입지점이 뒤에 위치할 경우 새로운 단속 카메라 설치
        if exit_point < route[0]:
            answer += 1
            exit_point = route[1]
        # 현재 진출지점이 저장된 진출지점보다 앞에 위치할 경우 저장된 진출지점을 재설정
        elif route[1] < exit_point:
            exit_point = route[1]

    return answer
```

**2022-04-03**

> 정확성  
> min TaseCase : 0.01ms, 10.3MB  
> max TaseCase : 0.03ms, 10.1MB  
> 효율성  
> min TaseCase : 0.48ms, 10.2MB  
> max TaseCase : 1.07ms, 10.6MB  


---

<!--
## 

분류 : 

[문제 링크]()

1. 

```python

```

**2022**

> min TaseCase :   
> max TaseCase :   
-->

