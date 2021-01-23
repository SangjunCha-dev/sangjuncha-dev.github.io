---
title: Programmers Python (level 2)
author: Sangjun Cha
date: 2020-12-22 22:38:31 +0900
categories: [Algorithm, Programmers]
tags: [algorithm, programmers, python]
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

```python
def solution(w,h):
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
> min TaseCase : 0.01ms, 10MB  
> max TaseCase : 6345.39ms, 10.1MB  

## 방법2

1. `h`값이 정수로 나오기위한 정수 최소 `w`값을 구하기 위해 최대공약수 구하기 
- 유클리드 호제법 공식으로 최대공약수 구하여 `v_gcd` 변수에 대입
2. 이때 색칠된 픽셀의 갯수는 `w+h-1`개
3. `w+h-1` 곱하기 `gcd`값이 색칠된 사각형 갯수 `line_pixel`변수에 대입
4. 반환값은 사각형의 면적인 `w*h`에서 `line_pixel`값을 뺄셈하여 반환 

```python
def solution(w,h):
    v_gcd = f_gcd(max(w,h), min(w,h))
    
    line_pixel = ((w+h)/v_gcd-1) * v_gcd
    return w*h-int(line_pixel)

def f_gcd(n_max, n_min):
    while n_min != 0:
       t = n_max % n_min
       (n_max, n_min) = (n_min, t)
    return abs(n_max)
```

**2020-12-27**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.01ms, 10.3MB  

## 방법3

1. `h`값이 정수로 나오기위한 정수 최소 `w`값을 구하기 위해 최대공약수 구하기
- math 라이브러리의 gcd 함수로 구하고 `gcd`변수에 대입
2. 이때 색칠된 픽셀의 갯수는 `w+h-1`개
3. `w+h-1` 곱하기 `gcd`값이 색칠된 사각형 갯수 `line_pixel`변수에 대입
4. 반환값은 사각형의 면적인 `w*h`에서 `line_pixel`값을 뺄셈하여 반환 

```python
import math

def solution(w,h):
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

변수 정의
- move_cnt : 조이스틱이 움직인 횟수
- text_len : 입력받은 문자열 길이
- alphabet : 조이스틱 위아래 움직인 횟수에 따른 알파벳 문자목록
- tf_list : 문자 'A'를 제외한 다른문자 여부목록
- distance_r : 기준위치 오른쪽으로 `True` 까지의 거리
- distance_l : 기준위치 왼쪽으로 `True` 까지의 거리

1. 필요한 변수 선언 및 초기화
2. 문자열 길이만큼 for 반복문을 실행
    - alphabet리스트에서 `name[i]`값의 위치를 `index`변수에 대입
    - `B~N`까지는 조이스틱 위로 이동한것으로 `index` + `move_cnt`값을 `move_cnt`변수에 대입
    - `Z~O`까지는 조이스틱 아래로 이동한것으로 `index-13` + `move_cnt`값을 `move_cnt`변수에 대입
    - 해당 문자로 변환하기까지의 조이스틱 움직인 횟수를 구함
3. `tf_list`리스트에서 `True`값이 있을때만 while 반복문 실행
    - 1부터 `text_len`까지 반복하여 `tf_list[(i+j)%text_len]`값에 `True`값 나올때까지 `distance_r`변수 1증가
        - 기준위치 오른쪽으로 `True`값 까지의 거리
    - 1부터 `text_len`까지 반복하여 `tf_list[(i-j)%text_len]`값에 `True`값 나올때까지 `distance_l`변수 1증가
        - 기준위치 왼쪽으로 `True`값 까지의 거리
    - `distance_l`값보다 `distance_r`값이 클때 `(i-1)%text_len`값을 `i`변수에 대입
        - 기준위치 왼쪽으로 1이동
    - `distance_l`값보다 `distance_r`값이 작거나 같을때 `(i+1)%text_len`값을 `i`변수에 대입
        - 기준위치 오른쪽으로 1이동
    - 거리 확인한 `tf_list[i]`값은 `False`값 대입하고 `move_cnt`변수 1증가

```python
def solution(name):
    move_cnt = 0
    text_len = len(name)
    alphabet = ['A',\
                'B','C','D','E','F','G','H','I','J','K','L','M','N',\
                'Z','Y','X','W','V','U','T','S','R','Q','P','O']
    
    tf_list = [False] * text_len
    for i in range(text_len):
        index = alphabet.index(name[i])
        move_cnt = move_cnt+index if index <= 13 else move_cnt+(index-13)
        if index: tf_list[i] = True
    
    tf_list[0] = False
    i = 0
    while tf_list.count(True):
        distance_r = 0
        distance_l = 0

        for j in range(1, text_len):
            distance_r += 1
            if tf_list[(i+j)%text_len]:
                break
        
        for j in range(1, text_len):
            distance_l += 1
            if tf_list[(i-j)%text_len]:
                break

        i = (i-1)%text_len if distance_l < distance_r else (i+1)%text_len
        tf_list[i] = False
        
        move_cnt += 1

    return move_cnt
```

**2020-12-28**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.03ms, 10.3MB  



# [가장 큰 수](https://programmers.co.kr/learn/courses/30/lessons/42746)

분류 : 정렬

## 방법1

변수 정의
- int(math.log10(x)+1 if x else 0) : 입력값 x의 자릿수 구하기

1. sort함수를 사용하여 `numbers` 리스트 역정렬
2. 첫번째 정렬조건 원소 `x`문자열에 `x[0]` 문자를 (최대자릿수-`x`자릿수)만큼 추가하여 비교
3. 두번째 정렬조건 `x%10` 나머지 값으로 정렬 
4. 정렬된 `numbers` 첫번째 값이 0 아닐때 `numbers`리스트를 하나의 문자열로 합쳐서 반환
5. 정렬된 `numbers` 첫번째 값이 0 이면 `'0'` 문자반환

검증 테스트 케이스
- [12, 121] -> '12121'  case 1~6
- [21, 212] -> '21221'  case 1~6
- [0, 0, 0] -> '0'      case 11

```python
import math

def solution(numbers):
    numbers.sort(key=lambda x: (int(str(x)+(str(x)[0]*(4-int(math.log10(x)+1 if x else 0)))), x%10), reverse=True)
    return ''.join(map(str, numbers)) if numbers[0] != 0 else '0'
```

**2021-01-07**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.04ms, 10.3MB  
> max TaseCase : 134.49ms, 23.8MB  

## 방법2

1. `numbers`리스트 int형 원소들을 map함수로 str형으로 변환
2. 원소 x 문자길이를 증가시키기위해 문자열길이 3배 증가시켜 역정렬
- 30, 34, 3 -> 303030, 343434, 333 첫문자부터 비교
- 입력정수는 최대 1000이하 이므로 *3 부터 문제없이 실행가능
3. 정렬된 `numbers` 첫번째 값이 0 아닐때 `numbers`리스트를 하나의 문자열로 합쳐서 반환
4. 정렬된 `numbers` 첫번째 값이 0 이면 `'0'` 문자반환

```python
def solution(numbers):
    numbers = list(map(str, numbers))
    numbers.sort(key=lambda x : x*3, reverse=True)
    return ''.join(numbers) if numbers[0] != '0' else '0'
```

**2021-01-07**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 56.59ms, 27.5MB  

## 방법3

1. 방법2와 동일한 알고리즘이나 2줄로 작성하기위해 `sorted` 함수를 사용
- `sorted`와`sort`는 정렬 과정은 동일하나 `sorted`의 경우 리스트 복사과정이 추가되어 메모리를 추가로 할당받음 

```python
def solution(numbers):
    numbers = sorted(list(map(str, numbers)), key=lambda x: x*3, reverse=True)
    return ''.join(numbers) if numbers[0] != '0' else '0'
```

**2021-01-07**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 55.10ms, 28.2MB  



# [소수 찾기](https://programmers.co.kr/learn/courses/30/lessons/42839)

분류 : 완전탐색

1. 입력받은 `numbers` 문자열로 만들수 있는 모든 정수형 리스트 `numbers_list` 생성
    - permutations 라이브러리를 사용하여 조합할 수 있는 모든 값 생성
2. `numbers_list` 리스트 중복제거
3. `numbers_list` 리스트를 `prime_number`함수로 소수 판별
    - 숫자 num의 약수는 제곱근(num) 범위에 있다 원리를 이용하여 판별
4. 판별결과를 `answer`변수에 합산하여 반환

```python
from itertools import permutations

def solution(numbers):
    answer = 0
    numbers_list = []

    # 모든 정수형 리스트 만들기
    for i in range(1, len(numbers)+1):
        num_list = list(permutations(list(numbers), i))

        for num in num_list:
            numbers_list.append(int(''.join(list(num))))

    # 중복제거
    numbers_list = list(set(map(int, numbers_list)))
    
    # 소수 판별후 갯수 체크
    for n in numbers_list:
        if prime_number(n):
            answer += 1
    
    return answer

def prime_number(n):
    if n == 2:
        return True
    elif (n % 2 == 0) or (n < 2):
        return False

    sqrt = int(n**0.5)+1
    for i in range(3,sqrt+1,2):
        if n % i == 0:
            return False
    return True
```

**2021-01-09**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.04ms, 10.4MB  
> max TaseCase : 13.19ms, 10.6MB  



# [큰 수 만들기](https://programmers.co.kr/learn/courses/30/lessons/42883)

분류 : 탐욕법(Greedy)

추가 Test Case  

|number|k|return|
|------|-|------|
|"99999"|4|"9"|
|"543212345"|3|"543345"|
|"87654321"|3|"87654"|
|"54329"|4|"9"|

1. 앞숫자가 뒷숫자보다 작은 수일때 앞숫자 삭제

```python
def solution(number, k):
    answer = number[0]
    for i in number[1:]:
        answer += i
        try:
            while k and answer[-2] < answer[-1]:
                answer = answer[:-2] + answer[-1]
                k -= 1
        except IndexError: pass
    return answer[:-k] if k else answer
```

**2021-01-13**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 207.73ms, 11.6MB  



# [H-Index](https://programmers.co.kr/learn/courses/30/lessons/42747)

분류 : 정렬

추가 Test Case  

|citations(int[])           |return |
|------                     |-      |
|[12, 11, 0]                |2      |
|[6, 6, 6, 6, 6, 1]         |5      |
|[0, 1, 1, 1, 1, 3, 3, 4]   |1      |

## 방법1

```python
def solution(citations):
    citations.sort(reverse=True)

    h_index = 0
    list_len = len(citations)
    for i in range(list_len):
        if citations[i] < h_index+1:
            if h_index < list_len-(i+1):
                h_index -= 1
            else:
                break
        else:
            h_index += 1
    return h_index
```

**2021-01-14**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.2MB  
> max TaseCase : 0.25ms, 10.2MB  

## 방법2

추가 Test Case  

|citations(int[])           |return |
|------                     |-      |
|[12, 11, 0]                |2      |
|[6, 6, 6, 6, 6, 1]         |5      |

1. 방법1 3번째 Test Case 예외처리 안한 코드

```python
def solution(citations):
    citations.sort(reverse=True)
    for i, num in enumerate(citations, start=1):
        if num < i: return i-1
    return i
```

**2021-01-14**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.18ms, 10.2MB  



# [위장](https://programmers.co.kr/learn/courses/30/lessons/42578)

분류 : 해시

추가 Test Case

|clothes(string[][])|Return |
|----               |----   |
|[['a', 'a'], ['b', 'b'], ['c', 'c']]                                                       |7|
|[[1, 'a'], [2, 'a'], [3, 'a'], [1, 'b'], [2, 'b'], [3, 'b'], [1, 'c'], [2, 'c'], [3, 'c']] |63|

1. 테스트 케이스 2번째 예시로 순열 `(3C0 + 3C1) * (3C0 + 3C1) * (3C0 + 3C1) - 1` 조합의 갯수이다.

```python
def solution(clothes):
    clothes_dict = {}
    # 카테고리명과 해당 카테고리 갯수 추출
    for cloth in clothes:
        if cloth[1] in clothes_dict:
            clothes_dict[cloth[1]] += 1
        else:
            clothes_dict[cloth[1]] = 1
    # 조합 연산
    answer = 1
    for cloth in clothes_dict.values():
        answer *= cloth+1
    return answer-1
```

**2021-01-14**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.2MB  
> max TaseCase : 0.02ms, 10.2MB  



# [카펫](https://programmers.co.kr/learn/courses/30/lessons/42842)

분류 : 완전탐색

추가 Test Case  

|brown(int) |yellow(int)|return |
|-          |-          |-      |
|18         |6          |6      |

1. (산술기하평균 공식)과 (약수 중간값은 수의 제곱근 이하) 2가지 공식 이용

```python
def solution(brown, yellow):
    sqrt = int(yellow**0.5)
    while 0 < sqrt:
        if (yellow%sqrt == 0) and ((yellow/sqrt+2)*(sqrt+2) == brown+yellow):
            return [yellow/sqrt+2, sqrt+2]
        sqrt -= 1
```

**2021-01-14**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10MB  
> max TaseCase : 0.06ms, 10.3MB  



# [타겟 넘버](https://programmers.co.kr/learn/courses/30/lessons/43165)

분류 : 깊이/너비 우선 탐색(DFS/BFS)

1. 

```python

```

**2021**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase :   
> max TaseCase :  



# [이진 변환 반복하기](https://programmers.co.kr/learn/courses/30/lessons/70129)

분류 : 월간 코드 챌린지 시즌1

```python
def solution(s):
    bin_cnt, zero_cnt = 0, 0
    while s != '1':
        cnt = s.count('0')
        s = str(bin(len(s)-cnt))[2:]
        bin_cnt += 1
        zero_cnt += cnt
    return [bin_cnt, zero_cnt]
```

**2021-01-14**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.64ms, 10.3MB  



# [최댓값과 최솟값](https://programmers.co.kr/learn/courses/30/lessons/12939)

분류 : 연습문제

```python
def solution(s):
    s = list(map(int, s.split(' ')))
    return f"{min(s)} {max(s)}"
```

**2021-01-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.02ms, 10.3MB  
> max TaseCase : 0.05ms, 10.3MB  



# [피보나치 수](https://programmers.co.kr/learn/courses/30/lessons/12945)

분류 : 연습문제

## 방법1

```python
def solution(n):
    num_list = [0, 1] + [0] * (n-1)
    for i in range(2, len(num_list[2:])+2):
        num_list[i] = num_list[i-2] + num_list[i-1]
    return num_list[-1]%1234567 if n else 0
```

**2021-01-18**

> 채점 결과  
> 합계: 0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 376.82ms, 456MB  

## 방법2

```python
def solution(num):
    n, m = 0, 1
    for _ in range(1, num):
        n, m = m, n+m
    return m % 1234567 if n else 0
```

**2021-01-18**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 10.1MB  
> max TaseCase : 147.18ms, 10.1MB  



# [행렬의 곱셈](https://programmers.co.kr/learn/courses/30/lessons/12949)

분류 : 연습문제

|변수명|형식|크기|
|-|-|-|
|arr1|2차원리스트|m * k|
|arr2|2차원리스트|k * n|
|answer|2차원리스트|m * n|

## 방법1

```python
def solution(arr1, arr2):
    answer = []
    for m, val1 in enumerate(arr1):
        arr_list = []
        for k in range(len(arr2[0])):
            total = 0
            for n, val2 in enumerate(arr2):
                total += val1[n] * val2[k]
            arr_list.append(total)
        answer.append(arr_list)
    return answer
```

**2021-01-21**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.56ms, 10.3MB  
> max TaseCase : 52.38ms, 11MB  

## 방법2

```python
def solution(arr1, arr2):
    answer = [[0 for _ in range(len(arr2[0]))] for _ in range(len(arr1))]
    for m, val1 in enumerate(arr1):
        for k in range(len(arr2[0])):
            for n, val2 in enumerate(arr2):
                answer[m][k] += val1[n] * val2[k]
    return answer
```

**2021-01-21**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.76ms, 10.3MB  
> max TaseCase : 78.02ms, 11.2MB  


# [JadenCase 문자열 만들기](https://programmers.co.kr/learn/courses/30/lessons/12951)

분류 : 연습문제

추가 Test Case

|s(string)|Return|
|-|-|
|"  a b  c   d  "|"  A B  C   D  "|

## 방법1

```python
def solution(s):
    return ' '.join([text[0].upper() + text[1:].lower() if text else '' for text in s.split(' ')])
```

**2021-01-21**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 9.98MB  
> max TaseCase : 0.01ms, 10.3MB  

## 방법2

1. 내장함수 `capitalize()` : 문자열 첫 글자를 대문자로 변환

```python
def solution(s):
    return ' '.join([text.capitalize() if text else '' for text in s.split(' ')])
```

**2021-01-21**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.00ms, 9.98MB  
> max TaseCase : 0.01ms, 10.2MB  



# [N개의 최소공배수](https://programmers.co.kr/learn/courses/30/lessons/12953)

분류 : 연습문제

1. 유클리드 호제법 사용

## 방법1

```python
def solution(arr):
    n_dict = {}
    for n in arr:
        for i, val in euclidean(n).items():
            if (not i in n_dict) or (n_dict[i] < val):
                n_dict[i] = val
    answer = 1
    for i, val in n_dict.items():
        answer *= (i**val)
    return answer

def euclidean(n):
    n_dict = {}
    i = 2
    while 1 < n:
        if not n % i:
            n //= i
            n_dict[i] = n_dict[i] + 1 if i in n_dict else 1
        else:
            i += 1
    return n_dict
```

**2021-01-21**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 0.03ms, 10.2MB  

## 방법2

```python
def solution(arr):
    answer = arr[0]
    for i in arr[1:]:
        answer = lcm(answer, i)
    return answer

def lcm(m, n):
    return m * n // gcd(m, n)

def gcd(m, n):
    while n != 0:
       t = m % n
       (m,n) = (n,t)
    return m
```

**2021-01-21**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.1MB  
> max TaseCase : 0.01ms, 10.3MB  



# [소수 만들기](https://programmers.co.kr/learn/courses/30/lessons/12977)

분류 : Summer/Winter Coding(~2018)

2부터 제곱근까지 나머지 연산하여 0이 안 나올 때 소수로 판정

## 방법1

1. 순열로 조합 생성

```python
def solution(nums):
    cnt = 0
    nums_list = []

    for i, val1 in enumerate(nums[:-2]):
        for j, val2 in enumerate(nums[i+1:-1], start=i+1):
            for val3 in nums[j+1:]:
                if prime(sum([val1, val2, val3])):
                    cnt += 1
    return cnt

def prime(n):
    if n == 2:
        return True
    elif (n % 2 == 0) or (n < 2):
        return False

    sqrt = int(n**0.5)+1
    for i in range(3,sqrt+1,2):
        if n % i == 0:
            return False
    return True
```

**2021-01-23**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 12.70ms, 10.3MB  

## 방법2

1. 중복되지 않은 원소를 가진 순열조합 combinations 함수

```python
from itertools import combinations
def solution(nums):
    cnt = 0
    for n_list in combinations(nums, 3):
        if prime(sum(n_list)):
            cnt += 1
    return cnt

def prime(n):
    if n == 2:
        return True
    elif (n % 2 == 0) or (n < 2):
        return False

    sqrt = int(n**0.5)+1
    for i in range(3,sqrt+1,2):
        if n % i == 0:
            return False
    return True
```

**2021-01-23**

> 채점 결과  
> 합계: 100.0 / 100.0  
> min TaseCase : 0.01ms, 10.2MB  
> max TaseCase : 12.11ms, 10.3MB  



# [예상 대진표](https://programmers.co.kr/learn/courses/30/lessons/12985)

분류 : 2017 팁스타운

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

