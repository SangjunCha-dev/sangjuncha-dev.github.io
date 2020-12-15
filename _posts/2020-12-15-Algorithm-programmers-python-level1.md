---
title: Programmers Python (level 1)
author: Sang Jun
date: 2020-12-15 15:05:35 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers]
pin: false
---

# [완전탐색 - 모의고사](https://programmers.co.kr/learn/courses/30/lessons/42840)

1. 문제에서 요구하는 1, 2, 3 수포자 답 `person` 2차원 리스트 생성
2. 각 `person` 답 리스트와 입력받은 `answers` 리스트와 비교하여 `answer_cnt`변수에 정답 갯수 작성
3. `answer_cnt` 리스트의 최댓값과 각 `answer_cnt` 원소와 비교하여 같다면 `index+1` 값을 `answer`리스트에 추가
4. 최대정답자 `answer` 리스트 return

```python
def solution(answers):
    answer = []
    answer_cnt = [0, 0, 0]
    answers_len = len(answers)
    answer_list = [
        [1, 2, 3, 4, 5],
        [2, 1, 2, 3, 2, 4, 2, 5],
        [3, 3, 1, 1, 2, 2, 4, 4, 5, 5]
    ]
    person = [[], [], []]
    
    # 수포자 정답 작성
    for i in range(answers_len):
        person[0].append(answer_list[0][i%5])
        person[1].append(answer_list[1][i%8])
        person[2].append(answer_list[2][i%10])
            
    # 정답 매칭
    for i, ans in enumerate(answers):
        if ans == person[0][i]: answer_cnt[0] += 1
        if ans == person[1][i]: answer_cnt[1] += 1
        if ans == person[2][i]: answer_cnt[2] += 1

    # 최다 정답자 추출
    max_cnt = max(answer_cnt)
    for i, cnt in enumerate(answer_cnt):
        if max_cnt == cnt:
            answer.append(i+1)
    
    return answer
```

**2020-12-15**

> 채점 결과  
> 합계: 100.0 / 100.0  
> Worst TaseCase : 5.96ms, 10.5MB  



# (정렬 - K번째수)[https://programmers.co.kr/learn/courses/30/lessons/42748]

1. 입력받은 `commands` 2차원 리스트에서 `command` 1차원 리스트 추출
2. `array`리스트 `command[0]-1`번째부터 `command[1]`번째까지 추출하여 `array_ext`변수에 저장
3. 저장한 `array_ext`리스트 정렬
4. 정렬한 `array_ext`리스트의 `command[2]-1`번째 원소를 `answer`리스트에 추가
5. 추출한 원소리스트 `answer` return

```python
def solution(array, commands):
    answer = []
    
    for i, command in enumerate(commands):
        array_ext = array[command[0]-1:command[1]]
        array_ext.sort()
        answer.append(array_ext[command[2]-1])
    
    return answer
```

**2020-12-15**

> 채점 결과  
> 합계: 100.0 / 100.0  
> Worst TaseCase : 0.01ms, 10.2MB



# (탐욕법(Greedy) - 체육복)[https://programmers.co.kr/learn/courses/30/lessons/42862]

1. 

```python

```

**2020-12-15**

> 채점 결과  
> 


