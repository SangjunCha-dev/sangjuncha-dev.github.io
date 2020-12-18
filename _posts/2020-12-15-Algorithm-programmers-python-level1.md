---
title: Programmers Python (level 1)
author: Sang Jun
date: 2020-12-15 15:05:35 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers]
pin: false
---

# [완전탐색 - 모의고사](https://programmers.co.kr/learn/courses/30/lessons/42840)

1. 입력받은 `answers` 리스트와 수포자들의 답 리스트와 비교하여 같다면 `answer_cnt`변수에 정답 갯수 추가
2. `answer_cnt` 리스트의 최댓값과 각 `answer_cnt` 원소와 비교하여 같다면 `index+1` 값을 `answer`리스트에 추가
3. 최대정답자 `answer` 리스트 return

```python
def solution(answers):
    answer = []

    answer_cnt = [0, 0, 0]
    answer_list = [
        [1, 2, 3, 4, 5],
        [2, 1, 2, 3, 2, 4, 2, 5],
        [3, 3, 1, 1, 2, 2, 4, 4, 5, 5]
    ]
    
    # 정답 매칭
    for i in range(len(answers)):
        ans = answers[i]
        if ans == answer_list[0][i%5]: answer_cnt[0] += 1
        if ans == answer_list[1][i%8]: answer_cnt[1] += 1
        if ans == answer_list[2][i%10]: answer_cnt[2] += 1

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
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 3.12ms, 10.3MB  



# [정렬 - K번째수](https://programmers.co.kr/learn/courses/30/lessons/42748)

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
> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.01ms, 10.2MB  



# [탐욕법(Greedy) - 체육복](https://programmers.co.kr/learn/courses/30/lessons/42862)

1. `reserve` 리스트에서 `lost` 리스트와 같은 원소를 삭제
    - 먼저 여분의 체육복을 가진 사람이 도난당한 경우를 고려
2. set함수로 정렬이 되지 않았으므로 `sort()` 정렬
3. `lost[l_len]` 원소값 -1, +1 값이 `reserve[r_len]` 리스트에 있으면 두 원소를 삭제하고 `l_len`, `r_len` 값 -1
    - 도난당한 사람에 맞는 여유분 체육복 있음
4. `lost[l_len]` 원소값이 `reserve[r_len]`보다 클 경우 `l_len`값 -1
    - 도난당한 사람에 맞는 여유분 체육복 없음
5. `lost[l_len]` 원소값이 `reserve[r_len]`보다 작을 경우 `r_len`값 -1
    - 여유분 체육복에 맞는 도난당한 사람이 없음
6. 최종적으로 `lost`리스트의 길이 값을 반환
    - 체육복을 빌리지 못한 사람

```python
def solution(n, lost, reserve):
    answer = 0

    lost_2 = list(set(lost)-set(reserve))
    reserve_2 = list(set(reserve)-set(lost))
    lost, reserve = lost_2, reserve_2
    
    l_len, r_len = len(lost) - 1, len(reserve) - 1
    lost.sort()
    reserve.sort()

    while 0 <= l_len and 0 <= r_len:
        if reserve[r_len] - 1 <= lost[l_len] <= reserve[r_len] + 1:
            del lost[l_len], reserve[r_len]
            l_len -= 1
            r_len -= 1
        elif lost[l_len] > reserve[r_len] + 1:
            l_len -= 1
        elif lost[l_len] < reserve[r_len] - 1:
            r_len -= 1

    answer = n - len(lost)

    return answer
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.02ms, 10.3MB  



# [연습문제 - 2016년](https://programmers.co.kr/learn/courses/30/lessons/12901)

1. 요일 `day_list` 와, 윤년에 해당되는 달의 일수 `month_dict` 선언
2. 2016년 1월 1일 금요일에 해당하는 초기 `days`값 선언
3. 입력받은 1월부터 `a-1`달까지 일수를 `days`변수에 덧셈하고 `b`일수를 `days`변수에 덧셈한다.
4. `days`변수를 `7`로 나머지 연산한 값으로 `day_list`리스트 해당되는 요일 찾아서 반환

```python
def solution(a, b):
    day_list = ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT']
    month_dict = {1:31, 2:29, 3:31, 4:30, 5:31, 6:30, 7:31, 8:31, 9:30, 10:31, 11:30, 12:31}
    days = 4  # 2016년 1월 1일 금요일 시작
    for month in range(1, a):
        days += month_dict[month]
    days += b
    return day_list[days%7]
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.01ms, 10.3MB  



# [연습문제 - 가운데 글자 가져오기](https://programmers.co.kr/learn/courses/30/lessons/12903)

1. 문자열 `s` 길이값 나누기 연산값을 `index`변수에 저장
2. `s` 길이값이 나머지 연산으로 홀수이면 `s[index]` 반환
3. `s` 길이값이 나머지 연산으로 짝수이면 `s[index-1:index+1]` 반환

```python
def solution(s):
    index = len(s)//2
    return s[index] if (len(s)%2 == 1) else s[index-1:index+1]
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.MB  
> max TaseCase : 0.00ms, 10.3MB  



# [연습문제 - 나누어 떨어지는 숫자 배열](https://programmers.co.kr/learn/courses/30/lessons/12910)

1. `arr` 리스트에 원소를 `divisor`나머지 연산하여 값이 없을때 `answer`리스트에 추가
    - 조건문에 `==` 사용할 경우 `3.44ms`
    - 조건문에 `not` 사용할 경우 `2.64ms`
2. `answer`리스트에 값이 있으면 `정렬`하여 반환하고 없으면 `[-1]` 반환

```python
def solution(arr, divisor):
    answer = []
    
    for e in arr:
        if not e%divisor:
            answer.append(e)
        
    return sorted(answer) if answer else [-1]
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 2.64ms, 13.3MB  



# [연습문제 - 두 정수 사이의 합](https://programmers.co.kr/learn/courses/30/lessons/12912)

1. 입력값 `a`, `b` 중 작은 값을 `num_min`변수에 큰 값을 `num_max`변수에 대입
2. 값이 같으면 `a` 반환
3. 가우스의 덧셈공식을 이용하여 각 `num_min-1`, `num_max` 변수까지의 합을 구한다.
    - 자연수 1부터 N까지의 합 = N(N+1)/2 
4. (큰 값 - 작은 값) 값 반환

```python
def solution(a, b):
    if a < b:
        num_min, num_max = a, b
    elif b < a:
        num_min, num_max = b, a
    else:
        return a
    return (num_max*(num_max+1) // 2) - (num_min*(num_min-1) // 2)
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.00ms, 10.3MB  

```
(abs(a-b)+1)*(a+b)//2
```
위의 공식을 사용하면 한줄로 코드 작성 가능하다.



# [연습문제 - 문자열 내 마음대로 정렬하기](https://programmers.co.kr/learn/courses/30/lessons/12915)

1. `strings`문자열 리스트 정렬 후
2. n번째 글자를 기준으로 다시 정렬하여 반환

```python
def solution(strings, n):
    return sorted(sorted(strings), key=lambda x:(x[n]))
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.04ms, 10.2MB  



# [연습문제 - 문자열 내림차순으로 배치하기](https://programmers.co.kr/learn/courses/30/lessons/12917)

1. 문자열을 역순으로 정렬한다음 `sorted`함수로 인해 list 자료형으로 수정됨 
2. list형 자료형을 하나의 문자열로 바꾸기위해 `join` 함수 이용   

```python
def solution(s):
    return ''.join(sorted(s, reverse=True))
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.07ms, 10.1MB  



# [연습문제 - 문자열 다루기 기본](https://programmers.co.kr/learn/courses/30/lessons/12918)

1. 문자열의 길이가 `4` or `6` 이면서
2. 문자열이 `int`타입으로 변환이 가능한지 확인하는 `isdecimal()`함수를 이용하여 반환

```python
def solution(s):
    return (len(s) == 4 or len(s) == 6) and (s.isdecimal())
```

**2020-12-18**

> 채점 결과  
> 합계: 87.5 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.01ms, 10.4MB  



# [연습문제 - 서울에서 김서방 찾기](https://programmers.co.kr/learn/courses/30/lessons/12919)

1. `index`함수로 찾는 문자열의 위치값 반환

```python
def solution(seoul):
    return f"김서방은 {seoul.index('Kim')}에 있다"
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.2MB  
> max TaseCase : 0.01ms, 10.2MB  



# [연습문제 - 수박수박수박수박수박수?](https://programmers.co.kr/learn/courses/30/lessons/12922)

1. `n` 나누기 2 만큼의 `수박` 문자열 선언
2. `n`값이 홀수이면 `수`문자 추가하여 `answer` 반환
3. `n`값이 짝수이면 `answer` 반환

```python
def solution(n):
    answer = '수박' * (n//2)
    return answer+'수' if n%2 == 1 else answer
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.01ms, 10.3MB  



# [연습문제 - 문자열을 정수로 바꾸기](https://programmers.co.kr/learn/courses/30/lessons/12925)

1. int형으로 변환하여 반환

```python
def solution(s):
    return int(s)
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.3MB  
> max TaseCase : 0.02ms, 10.4MB  



# [월간 코드 챌린지 시즌1 - 내적](https://programmers.co.kr/learn/courses/30/lessons/70128)

1. `a`리스트의 길이만큼 for문 반복
2. `a`, `b`리스트의 i번째끼리 곱셈한 값을 `answer`변수에 덧셈
3. 반복문 종료 후 `answer` 반환

```python
def solution(a, b):
    answer = 0
    for i in range(len(a)):
        answer += a[i]*b[i]
    return answer
```

**2020**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.16ms, 10.2MB  



# [연습문제 - 시저 암호](https://programmers.co.kr/learn/courses/30/lessons/12926)

1. 

```python
def solution(s, n):
    answer = ''
    for i in range(len(s)):
        if s[i] == ' ':
            answer += ' '
            continue
        num = ord(s[i])+n
        if not ((97 <= num <=122) or (65 <= num <= 90)):
            num -= 26
        answer += chr(num)
    return answer
```

**2020**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase :   
> max TaseCase :   

<!--
# []()

1. 

```python

```

**2020**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase :   
> max TaseCase :   
-->