---
title: Programmers Python (level 1)
author: Sangjun Cha
date: 2020-12-15 15:05:35 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers, python]
pin: false
---

# [모의고사](https://programmers.co.kr/learn/courses/30/lessons/42840)

분류 : 완전탐색

1. 입력받은 `answers` 리스트와 수포자들의 답 리스트와 비교하여 같다면 `answer_cnt`변수에 정답 개수 추가
2. `answer_cnt` 리스트의 최댓값과 각 `answer_cnt` 원소와 비교하여 같다면 `index+1` 값을 `answer`리스트에 추가
3. 최대 정답자 `answer` 리스트 return

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



# [K번째수](https://programmers.co.kr/learn/courses/30/lessons/42748)

분류 : 정렬

1. 입력받은 `commands` 2차원 리스트에서 `command` 1차원 리스트 추출
2. `array`리스트 `command[0]-1`번째부터 `command[1]`번째까지 추출하여 `array_ext`변수에 저장
3. 저장한 `array_ext`리스트 정렬
4. 정렬한 `array_ext`리스트의 `command[2]-1`번째 원소를 `answer`리스트에 추가
5. 추출한 원소 리스트 `answer` return

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



# [체육복](https://programmers.co.kr/learn/courses/30/lessons/42862)

분류 - 탐욕법(Greedy)

1. `reserve` 리스트에서 `lost` 리스트와 같은 원소를 삭제
    - 먼저 여분의 체육복을 가진 사람이 도난당한 경우를 고려
2. set함수로 정렬이 되지 않았으므로 `sort()` 정렬
3. `lost[l_len]` 원소 값 -1, +1 값이 `reserve[r_len]` 리스트에 있으면 두 원소를 삭제하고 `l_len`, `r_len` 값 -1
    - 도난당한 사람에 맞는 여유분 체육복 있음
4. `lost[l_len]` 원소 값이 `reserve[r_len]`보다 클 경우 `l_len`값 -1
    - 도난당한 사람에 맞는 여유분 체육복 없음
5. `lost[l_len]` 원소 값이 `reserve[r_len]`보다 작을 경우 `r_len`값 -1
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



# [2016년](https://programmers.co.kr/learn/courses/30/lessons/12901)

분류 : 연습문제

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



# [가운데 글자 가져오기](https://programmers.co.kr/learn/courses/30/lessons/12903)

분류 : 연습문제

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



# [나누어 떨어지는 숫자 배열](https://programmers.co.kr/learn/courses/30/lessons/12910)

분류 : 연습문제

1. `arr` 리스트에 원소를 `divisor`나머지 연산하여 값이 없을 때 `answer`리스트에 추가
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



# [두 정수 사이의 합](https://programmers.co.kr/learn/courses/30/lessons/12912)

분류 : 연습문제

1. 입력값 `a`, `b` 중 작은 값을 `num_min`변수에 큰 값을 `num_max`변수에 대입
2. 값이 같으면 `a` 반환
3. 가우스의 덧셈 공식을 이용하여 각 `num_min-1`, `num_max` 변수까지의 합을 구한다.
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



# [문자열 내 마음대로 정렬하기](https://programmers.co.kr/learn/courses/30/lessons/12915)

분류 : 연습문제

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



# [문자열 내림차순으로 배치하기](https://programmers.co.kr/learn/courses/30/lessons/12917)

분류 : 연습문제

1. 문자열을 역순으로 정렬한 다음 `sorted`함수로 인해 list 자료형으로 수정됨
2. list형 자료형을 하나의 문자열로 바꾸기 위해 `join` 함수 이용

```python
def solution(s):
    return ''.join(sorted(s, reverse=True))
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.07ms, 10.1MB  



# [문자열 다루기 기본](https://programmers.co.kr/learn/courses/30/lessons/12918)

분류 : 연습문제

1. 조건문으로 문자열의 길이가 `4` or `6` 이면서
2. 문자열이 `int`타입으로 변환이 가능한지 확인하는 `isdecimal()`함수를 이용하여 반환

```python
def solution(s):
    return (len(s) == 4 or len(s) == 6) and (s.isdecimal())
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.01ms, 10.4MB  



# [서울에서 김서방 찾기](https://programmers.co.kr/learn/courses/30/lessons/12919)

분류 : 연습문제

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



# [수박수박수박수박수박수?](https://programmers.co.kr/learn/courses/30/lessons/12922)

분류 : 연습문제

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



# [문자열을 정수로 바꾸기](https://programmers.co.kr/learn/courses/30/lessons/12925)

분류 : 연습문제

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



# [내적](https://programmers.co.kr/learn/courses/30/lessons/70128)

분류 : 월간 코드 챌린지 시즌1

1. `a`리스트의 길이만큼 for 문 반복
2. `a`, `b`리스트의 같은 위치끼리 곱셈한 값을 `answer`변수에 덧셈
3. 반복문 종료 후 `answer` 반환

```python
def solution(a, b):
    answer = 0
    for i in range(len(a)):
        answer += a[i]*b[i]
    return answer
```

**2020-12-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.16ms, 10.2MB  



# [시저 암호](https://programmers.co.kr/learn/courses/30/lessons/12926)

분류 : 연습문제

1. 문자열 길이만큼 for문 반복 실행
2. ` `일 경우 `answer`변수에 추가하고 다음 반복문 실행
3. `str_ascii`변수에 `s[i]`문자 아스키코드값 대입
4. `str_ascii`값이 90이하(대문자)일 경우 `large`변수에 `True` 대입
5. `str_ascii`값에 `n`만큼 덧셈
6. `str_ascii`값이 90(대문자 범위)을 초과면서 `large`변수가 `True`일 경우 `str_ascii`변수에 `-26`연산
7. `str_ascii`값이 122(소문자 범위)를 초과하면 `str_ascii`변수에 `-26`연산
8. 연산된 결과를 문자열로 변환하여 `answer`변수에 추가

```python
def solution(s, n):
    answer = ''
    for i in range(len(s)):
        if s[i] == ' ':
            answer += ' '
        else:
            str_ascii = ord(s[i])
            large = True if str_ascii <= 90 else False
            str_ascii += n
            if (90 < str_ascii) and (large):
                str_ascii -= 26
            elif 122 < str_ascii:
                str_ascii -= 26
            answer += chr(str_ascii)
    return answer
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 1.61ms, 10.2MB  



# [약수의 합](https://programmers.co.kr/learn/courses/30/lessons/12928)

분류 : 연습문제

1. `i`변수 초깃값 `1`
2. `quot`변수에 `n/i`몫을 대입
3. `quot`변수가 정수형 일 때 검증 시작
4. `quot`변수가 `i`보다 작을 경우 약수 집합의 중간지점을 지났기에 while 반복문 종료
5. `quot`변수가 `i`와 같지 않을 때 (`quot` + `i`) 값을 `answer`변수에 덧셈
6. `quot`변수가 `i`와 같을 때 `i`값을 `answer`변수에 덧셈
7. `i`변수에 1 덧셈
8. `i`값이 `quot`값보다 크면 while 반복문 종료

```python
def solution(n):
    answer = 0
    i = 1
    quot = n
    while i <= quot:
        quot = n/i
        if int(quot) == quot:
            if quot < i:
                break
            elif quot != i:
                answer += quot
            answer += i 
        i += 1
    return answer
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.02ms, 10.2MB  



# [이상한 문자 만들기](https://programmers.co.kr/learn/courses/30/lessons/12930)

분류 : 연습문제

1. 문자열 길이만큼 for 반복문 실행
2. `even`변수 초깃값 `True`
3. `s[i]`번째 문자가 ` `이면
    - `answer`변수에 ` `추가하고 `even`변수 `True`값으로 수정
4. `s[i]`번째 문자가 ` `아니면서 `even`값이 `True`경우
    - `answer`변수에 `s[i]`대문자 추가하고 `even`변수 `False`값으로 수정
5. `s[i]`번째 문자가 ` `아니면서 `even`값이 `False`경우
    - `answer`변수에 `s[i]`소문자 추가하고 `even`변수 `True`값으로 수정

```python
def solution(s):
    answer = ''
    even = True
    for i in range(len(s)):
        if s[i] == ' ':
            answer += ' '
            even = True
        elif even:
            answer += s[i].upper()
            even = False
        else:
            answer += s[i].lower()
            even = True
    return answer
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.04ms, 10.3MB  



# [자릿수 더하기](https://programmers.co.kr/learn/courses/30/lessons/12931)

분류 : 연습문제

1. `n`변수가 0 이상일 때 while 반복문 실행
2. `n`변수의 10 나머지 값을 `answer`변수에 덧셈
3. `n`변수에 `n` 나누기 10의 몫을 대입

```python
def solution(n):
    answer = 0
    while 0 < n:
        answer += n%10
        n = n//10
    return answer
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.00ms, 10.2MB  



# [자연수 뒤집어 배열로 만들기](https://programmers.co.kr/learn/courses/30/lessons/12932)

분류 : 연습문제

1. `n`값이 0 이상일 때 while 반복문 실행
2. `n`변수의 10 나머지 값을 `answer`리스트에 원소 추가
3. `n`변수에 `n` 나누기 10의 몫을 대입

```python
def solution(n):
    answer = []
    while 0 < n:
        answer.append(n%10)
        n = n//10
    return answer
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.01ms, 10.1MB  



# [정수 내림차순으로 배치하기](https://programmers.co.kr/learn/courses/30/lessons/12933)

분류 : 연습문제

1. 정수 `n`을 문자열 변환 후에 list로 내림차순 정렬한 값을 `answer`변수에 대입
2. `answer`리스트를 `join`으로 문자열 변환하고 int형 변환한 값을 반환

```python
def solution(n):
    answer = sorted(list(str(n)), reverse=True)
    return int(''.join(answer))
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.1MB  
> max TaseCase : 0.03ms, 10.4MB  



# [정수 제곱근 판별](https://programmers.co.kr/learn/courses/30/lessons/12934)

분류 : 연습문제

1. 입력값 `n` 루트 값이 정수형이면 제곱근이므로
2. (`n`제곱근+1) 제곱 값 반환
3. `n` 제곱근이 없으면 `-1` 반환

```python
def solution(n):
    n = n**0.5 
    if int(n) == n:
        return (n+1)**2
    return -1
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.02ms, 10.3MB  



# [제일 작은 수 제거하기](https://programmers.co.kr/learn/courses/30/lessons/12935)

분류 : 연습문제

1. `arr`리스트의 최소값을 찾아 삭제
2. `arr`리스트의 값이 없으면 `[-1]` 반환
3. `arr`리스트의 값이 있으면 `arr` 반환

```python
def solution(arr):
    arr.remove(min(arr)) 
    return arr if arr else [-1]
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.2MB  
> max TaseCase : 0.89ms, 16.7MB  



# [짝수와 홀수](https://programmers.co.kr/learn/courses/30/lessons/12937)

분류 : 연습문제

1. `num` 나머지 `2` 연산결과가 1일 경우 `Odd` 반환
2. 그외의 경우 `Even` 반환

```python
def solution(num):
    return "Odd" if num%2==1 else "Even"
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.00ms, 10.3MB  



# [최대공약수와 최소공배수](https://programmers.co.kr/learn/courses/30/lessons/12940)

분류 : 연습문제

1. 각 입력값 `n`, `m`에 대해서 소인수를 구한다.
2. 각 소인수의 집합에서 중복된 원소들의 곱이 `최대공약수`
3. `n * m / 최대공약수`값이 `최대공배수`

```python
def solution(n, m):
    answer = [1, 1]
    dict1 = prime(n)
    dict2 = prime(m)
    # 최대공약수
    for num in dict1:
        if num in dict2:
            answer[0] *= (num ** min(dict1[num], dict2[num]))
    # 최소공배수
    answer[1] = n * m / answer[0]
    return answer

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

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.04ms, 10.2MB  

```
유클리드 호제법을 사용하면 간결하게 작성 가능하다.
```


# [콜라츠 추측](https://programmers.co.kr/learn/courses/30/lessons/12943)

분류 : 연습문제

1. 500번 반복하여 조건만족하지 못하면 `-1` 반환
2. `num`값이 짝수면 `num/2` 연산을 홀수면 `num*3+1`연산 수행
3. `num`값이 1일경우 `cnt` 반환

```python
def solution(num):
    cnt = 0
    while cnt < 500:
        if num == 1: 
            return cnt
        num = num/2 if num%2==0 else num*3+1
        cnt += 1
    return -1
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.20ms, 10.2MB  



# [평균 구하기](https://programmers.co.kr/learn/courses/30/lessons/12944)

분류 : 연습문제

1. `arr`리스트 원소들의 합을 `arr`리스트 길이로 나눈값 반환

```python
def solution(arr):
    return sum(arr)/len(arr)
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.01ms, 10.3MB  



# [하샤드 수](https://programmers.co.kr/learn/courses/30/lessons/12947)

분류 : 연습문제

1. 각 자리수의 합으로 입력값 `x`를 나누었을때 0이 나오면 `True` 아니면 `False` 반환

```python
def solution(x):
    sum, n = 0, x
    while 0 < n:
        sum += n%10
        n //= 10
    return True if (x%sum==0) else False
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.00ms, 10.3MB  



# [핸드폰 번호 가리기](https://programmers.co.kr/learn/courses/30/lessons/12948)

분류 : 연습문제

1. 입력값 (`phone_number`길이-4)만큼 `*`문자를 추가하고
2. 뒤에 `phone_number` 뒤에서 4번째부터 마지막까지의 문자열을 추가하여 반환

```python
def solution(phone_number):
    return ('*' * (len(phone_number)-4)) + phone_number[-4:]
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.00ms, 10.3MB  



# [행렬의 덧셈](https://programmers.co.kr/learn/courses/30/lessons/12950)

분류 : 연습문제

1. `arr1`리스트 길이만큼 2차원 `answer` 리스트 선언
2. 이중 for 반복문을 사용하여 `arr1[i][j] + arr2[i][j]`연산값을 `answer[i][j]`리스트 원소에 저장

```python
def solution(arr1, arr2):
    answer = [[] for _ in range(len(arr1))]
    for i in range(len(arr1)):
        for j in range(len(arr1[i])):
            answer[i].append(arr1[i][j] + arr2[i][j])
    return answer
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 32.47ms, 22.9MB  



# [x만큼 간격이 있는 n개의 숫자](https://programmers.co.kr/learn/courses/30/lessons/12954)

분류 : 연습문제

1. i는 1부터 n까지 반복 실행
2. `answer`리스트에 x*i 원소를 추가하여 반환

```python
def solution(x, n):
    answer = []
    for i in range(1, n+1):
        answer.append(x*i)
    return answer
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.15ms, 10.4MB  



# [직사각형 별찍기](https://programmers.co.kr/learn/courses/30/lessons/12969)

분류 : 연습문제

1. 입력값 `n`가로길이, `m`세로길이만큼 `*`출력

```python
n, m = map(int, input().strip().split(' '))
print(('*' * n + '\n') * m)
```

**2020-12-22**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 12.37ms, 7.54MB  
> max TaseCase : 15.18ms, 7.6MB  



# [크레인 인형뽑기 게임](https://programmers.co.kr/learn/courses/30/lessons/64061)

분류 :2019 카카오 개발자 겨울 인턴십

```python
def solution(board, moves):
    answer = 0
    queue = []

    for i in moves:
        i -= 1
        for row in board:
            if row[i]:
                queue.append(row[i])
                row[i] = 0
                break
        if (1 < len(queue)) and (queue[-2] == queue[-1]):
            answer += 2
            queue.pop()
            queue.pop()
    
    return answer
```

**2021-01-26**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.3MB  
> max TaseCase : 1.02ms, 10.2MB  



# [두 개 뽑아서 더하기](https://programmers.co.kr/learn/courses/30/lessons/68644)

분류 : 월간 코드 챌린지 시즌1

## 방법1

```python
def solution(numbers):
    answer = []
    for i, val1 in enumerate(numbers):
        for val2 in numbers[i+1:]:
            answer.append(val1+val2)
    return sorted(set(answer))
```

**2021-01-27**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.58ms, 10.3MB  

## 방법2

1. 중복없는 조합 생성 함수 combinations 사용

```python
from itertools import combinations

def solution(numbers):
    answer = []
    for val in set(combinations(numbers, 2)):
        answer.append(sum(val))
    return sorted(set(answer))
```

**2021-01-27**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 9.98MB  
> max TaseCase : 1.38ms, 10.3MB  



# [완주하지 못한 선수](https://programmers.co.kr/learn/courses/30/lessons/42576)

분류 : 해시

## 방법1

1. 두개의 리스트 정렬 후 순서대로 비교하여 다른 문자열일때 해당 participant 리스트의 문자열 반환

```python
def solution(participant, completion):
    participant.sort()
    completion.sort()
    for i, v in enumerate(completion):
        if v != participant[i]:
            return participant[i]
    return participant[-1]
```

**2021-01-29**

> 채점 결과  
> 합계: 100.0 / 100.0  

정확성  테스트
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.65ms, 10.3MB  

효율성  테스트
> min TaseCase : 36.78ms, 18MB  
> max TaseCase : 86.29ms, 26.3MB  

## 방법2

zip(*iterable) : 두개이상의 자료형을 묶어주는 함수

```python
def solution(participant, completion):
    participant.sort()
    completion.sort()
    for p, c in zip(participant, completion):
        if p != c:
            return p
    return participant[-1]
```

**2021-01-29**

> 채점 결과  
> 합계: 100.0 / 100.0  

정확성  테스트
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.63ms, 10.4MB  

효율성  테스트
> min TaseCase : 36.12ms, 18.1MB  
> max TaseCase : 82.59ms, 26.3MB  



# [신규 아이디 추천](https://programmers.co.kr/learn/courses/30/lessons/72410)

분류 : 2021 KAKAO BLIND RECRUITMENT

1. 정규식 사용

## 방법1

```python
import re
p1 = re.compile(r'[^a-z0-9_.-]')
p2 = re.compile(r'[.]+')
p3 = re.compile(r'^[.]|[.]$')
def solution(new_id):
    new_id = new_id.lower()
    new_id = re.sub(p1, '', new_id)
    new_id = re.sub(p2, '.', new_id)

    while True:
        new_id = re.sub(p3, '', new_id)
        if not new_id:
            new_id = 'a'
        elif 15 < len(new_id):
            new_id = new_id[:15]
        elif len(new_id) < 3:
            new_id += new_id[-1]
        else:
            break
    return new_id
```

**2021-01-30**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.2MB  
> max TaseCase : 0.21ms, 10.2MB  

## 방법2

```python
import re
p1 = re.compile(r'[^a-z0-9_.-]')
p2 = re.compile(r'[.]+')
p3 = re.compile(r'^[.]|[.]$')
def solution(new_id):
    new_id = new_id.lower()
    new_id = re.sub(p1, '', new_id)
    new_id = re.sub(p2, '.', new_id)
    new_id = re.sub(p3, '', new_id)
    new_id = new_id if new_id else 'a'
    new_id = new_id[:15] if 15 < len(new_id) else new_id
    new_id = re.sub(p3, '', new_id)
    new_id = new_id if 2 < len(new_id) else (new_id + new_id[-1] if 1 < len(new_id) else new_id*3)
    return new_id
```

**2021-01-30**
> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.1MB  
> max TaseCase : 0.23ms, 10.2MB  



# [3진법 뒤집기](https://programmers.co.kr/learn/courses/30/lessons/68935)

분류 : 월간 코드 챌린지 시즌1

## 방법1

```python
def solution(n):
    m = ''
    while 2 < n:
        n, m = n//3, m + str(n%3)
    n, m = 0, int(m + str(n))
    c = 0
    while m:
        n, m, c = n + (m%10 * 3**c), m//10, c+1
    return n
```

**2021-01-30**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.4MB  
> max TaseCase : 0.04ms, 10.4MB  

## 방법2

1. int함수로 문자열을 10진법으로 변환할 수 있음
    - (문자열, 문자열의 x진법)

```python
def solution(n):
    m = ''
    while 2 < n:
        n, m = n//3, m + str(n%3)
    return int(m + str(n), 3)
```

**2021-01-30**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.3MB  
> max TaseCase : 0.04ms, 10.3MB  



# [같은 숫자는 싫어](https://programmers.co.kr/learn/courses/30/lessons/12906)

분류 : 연습문제

```python
def solution(arr):
    answer = [arr[0]]
    for n in arr[1:]:
        if answer[-1] != n:
            answer.append(n)
    return answer
```

**2021-01-30**

> 채점 결과  
> 합계: 100.0 / 100.0  

정확성 테스트
> min TaseCase : 0.00ms, 10.2MB  
> max TaseCase : 0.02ms, 10.2MB  

효율성 테스트
> min TaseCase : 60.33ms, 32.5MB  
> max TaseCase : 61.85ms, 32.4MB  



# [문자열 내 p와 y의 개수](https://programmers.co.kr/learn/courses/30/lessons/12916)

분류 : 연습문제

```python
def solution(s):
    s = s.lower()
    return True if s.count('p') == s.count('y') else False
```

**2021-01-30**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.01ms, 10.3MB  



# [소수 찾기](https://programmers.co.kr/learn/courses/30/lessons/12921)

분류 : 연습문제

1. 에라토스테네스의 체 원리로 소수 리스트 작성
2. 2를 제외한 다른 짝수는 변수 선언단계에서 False 처리

```python
def solution(n):
    p_lst = [False, False, True] + [True, False] * (n//2-1)
    p_lst = p_lst if n%2 == 0 else p_lst + [True]
    for i in range(3, int(n**0.5)+1, 2):
        if not p_lst[i]:
            continue
        for j in range(i*2, n+1, i):
            p_lst[j] = False
    return p_lst.count(True)
```

**2021-01-30**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.3MB  
> max TaseCase : 89.90ms, 24MB  



# [키패드 누르기](https://programmers.co.kr/learn/courses/30/lessons/67256)

분류 : 2020 카카오 인턴십

```python
def solution(numbers, hand):
    answer = ''
    l_pos, r_pos, n_pos = [0, 0], [0, 0], [0, 0]
    for n in numbers:
        if n == 1 or n == 4 or n == 7:
            answer += 'L'
            l_pos = [3-n//3, 0]
        elif n == 3 or n == 6 or n == 9:
            answer += 'R'
            r_pos = [4-n//3, 0]
        else:
            n_pos = [3-n//3, 1] if n != 0 else [0, 1]
            
            l_val = abs(n_pos[0] - l_pos[0]) + abs(n_pos[1] - l_pos[1])
            r_val = abs(n_pos[0] - r_pos[0]) + abs(n_pos[1] - r_pos[1])
            
            if l_val < r_val:
                l_pos = n_pos
                answer += 'L'
            elif r_val < l_val:
                r_pos = n_pos
                answer += 'R'
            elif hand == 'left':
                l_pos = n_pos
                answer += 'L'
            else:
                r_pos = n_pos
                answer += 'R'
    return answer
```

**2021-01-31**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 0.81ms, 10.3MB  



# [예산](https://programmers.co.kr/learn/courses/30/lessons/12982)

분류 : Summer/Winter Coding(~2018)

## 방법1

```python
def solution(d, budget):
    cnt = 0
    if sum(d) == budget:
        return len(d)
    
    while d and 0 < budget:
        n = min(d)
        d.remove(n)
        budget -= n
        if budget < 0:
            break 
        cnt += 1
    return cnt
```

**2021-01-31**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.2MB  
> max TaseCase : 0.19ms, 10.2MB  

## 방법2

```python
def solution(d, budget):
    d.sort()
    while budget < sum(d):
        d.pop()
    return len(d)
```

> min TaseCase : 0.00ms, 10MB  
> max TaseCase : 0.05ms, 10.2MB  



# [1차 비밀지도](https://programmers.co.kr/learn/courses/30/lessons/17681)

분류 : 2018 KAKAO BLIND RECRUITMENT

## 방법1

```python
def solution(n, arr1, arr2):
    answer = [[' ' for _ in range(n)] for _ in range(n)]

    for i in range(n):
        val1 = str(bin(arr1[i]))[2:]
        val2 = str(bin(arr2[i]))[2:]
        while len(val1) < n:
            val1 = '0' + val1
        while len(val2) < n:
            val2 = '0' + val2

        for j in range(n):
            if val1[j] == '1' or val2[j] == '1':
                answer[i][j] = '#'
    
    return [''.join(answer[i]) for i in range(n)]
```

**2021-01-31**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.2MB  
> max TaseCase : 0.09ms, 10.2MB  

## 방법2

zip : 동일한 개수로 이루어진 자료형을 묶어 주는 함수
bin : 비트연산 함수
rjust : 오른쪽 정렬 문자열에 공백을 두번째 인자로 패딩
 - 첫번째 인자 길이가 문자열의 길이보다 작다면 원래의 문자열을 반환

```python
def solution(n, arr1, arr2):
    answer = []
    for i,j in zip(arr1,arr2):
        a12 = str(bin(i|j)[2:])
        a12=a12.rjust(n,'0')
        a12=a12.replace('1','#')
        a12=a12.replace('0',' ')
        answer.append(a12)
    return answer
```

> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.02ms, 10.2MB  



# [1차 다트 게임](https://programmers.co.kr/learn/courses/30/lessons/17682)

분류 : 2018 KAKAO BLIND RECRUITMENT

1. 정규식 사용하여 리스트 분할

```python
import re
p1 = re.compile(r'\d{1,2}')
p2 = re.compile(r'[SDT][*|#]?')
def solution(dartResult):
    answer = []
    arr1, arr2 = re.findall(p1, dartResult), re.findall(p2, dartResult)
    for i in range(len(arr1)):
        squ = 2 if arr2[i][0] == 'D' else 3 if arr2[i][0] == 'T' else 1
        mul = 2 if arr2[i][-1] == '*' else -1 if arr2[i][-1] == '#' else 1
        answer.append((int(arr1[i]) ** squ) * mul)
        if 1 < len(answer) and arr2[i][-1] == '*':
            answer[i-1] *= 2
    return sum(answer)
```

**2021-02-01**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.3MB  
> max TaseCase : 0.05ms, 10.3MB  



# [실패율](https://programmers.co.kr/learn/courses/30/lessons/42889)

분류 : 2019 KAKAO BLIND RECRUITMENT

1. 

```python

```

**2021**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase :   
> max TaseCase :   



<!--
# []()

분류 : 

1. 

```python

```

**2021**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase :   
> max TaseCase :   
-->
