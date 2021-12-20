---
title: "python 자료구조와 알고리즘(by 미아 스타인) 책 정리"
date: 2021-05-17T16:29:43+09:00
description: python 자료구조와 알고리즘(by 미아 스타인) 책 정리
menu:
  sidebar:
    name: python structures and algorithms books
    identifier: python-structures-and-algorithms-books
    parent: python
    weight: 30
tags: ["python", "learning"]
categories: ["Python", "Learning"]
---



# Part 1 헬로, 자료구조!

## Chapter 01 숫자

### 1.1 정수 int()

불변형 객체로 문자열을 정수로 변환하거나 다른 진법의 문자열을 정수로 변환

`int(문자열, 밑(n진법))`

```python
>>> s = '11'
>>> b = int(s, 2)
>>> print(b)
11
```

정수에 필요한 바이트 수(python 3.1이상)

```python
>>> (정수).bit_length()
10
```

발생가능한 Exception : ValueError


### 1.2 부동 소수점 float()

**float 32bit**
- 1bit 부호, 8bit 지수, 23bit 유효 숫자 자릿수

#### 1.2.1 부동소수점끼리 비교하기

이진수 분수로 표현으로 소수점의 정확한 비교는 어렵지만,

동등성 테스트로 사전에 정의된 정밀도 범위 내에서 비교는 가능한다.

unittest 모듈의 assertAlmostEqual() 메소드 같은 접근법 사용

```python
>>> def a(x, y, places=7):
        return rount(abs(x-y), places) == 0
```

#### 1.2.2 정수와 부동소수점 메서드

나누기 `연산자 /` 는 항상 부동소수점을 `연산자 //` 는 정수를 반환한다.

**divmod(x, y)**
-  x를 y로 나눌 때, 몫과 나머지를 반환

```python
>>> divmod(45, 6)
(7, 3)
```

**round(x, n)**
- n이 양수인 경우, x를 소수점 이하 n번째 자리로 반올림한 값을 반환한다.
- n이 음수인 경우, x를 정수 n번째 자리에서 반올림한 값을 반환한다. 

**as_interger_ratio()**
- 부동소수점을 분수로 표현

```python
>>> 2.75.as_integer_ratio()
(11, 4)
```


### 1.3 복소수


### 1.4 fraction 모듈

분수를 다루는 라이브러리

```python
from fractions import Fraction

def rounding_floats(num1, places):
    return round(num1, places)

def float_to_fractions(num):
    return Fraction(*num.as_integer_ratio())

def get_denominator(num1, num2):
    ''' 분모 반환 '''
    a = Fraction(num1, num2)
    return a.denominator

def get_numerator(num1, num2):
    ''' 분자 반환 '''
    a = Fraction(num1, num2)
    return a.numerator

def test_testing_floats():
    num1 = 1.25
    num2 = 1
    num3 = -1
    num4 = 5/4
    num6 = 6
    assert(rounding_floats(num1, num2) == 1.2)
    assert(rounding_floats(num1*10, num3) == 10)
    assert(float_to_fractions(num1) == num4)
    assert(get_denominator(num2, num6) == num6)
    assert(get_numerator(num2, num6) == num2)
    print("테스트 통과!")

if __name__ == '__main__':
    test_testing_floats()
```


```shell
테스트 통과!
```

### 1.5 decimal 모듈

정확한 10진법의 부동소수점 숫자가 필요한 경우, 부동소수점 불변 타입인 `decimal.Decimal` 사용할 수 있다.

일반적인 float 데이터는 정확한 값 비교를 할 수 없다.

```python
>>> a = sum([0.1 for _ in range(10)])
>>> b = 1.0
>>> a == b
False
```

`decimal.Decimal` 모듈을 사용한 float 데이터는 정확한 값 비교가 가능하다.

```python
>>> from decimal import Decimal
>>> a = sum(Decimal('0.1') for _ in range(10))
>>> b = Decimal('1.0')
>>> a == b
True
```


### 1.6 2진수, 8진수, 16진수

**bin(n)**
- 정수 n의 2진수 문자열 반환한다.

```python
>>> bin(30)
'0b11110'
```

**oct(n)**
- 정수 n의 8진수 문자열 반환한다.

```python
>>> oct(30)
'0o36'
```

**hex(n)**
- 정수 n의 16진수 문자열 반환한다.

```python
>>> hex(30)
'0x1e'
```

### 1.7 random 모듈

난수 생성 모듈 예제 코드

```python
import random

def testing_random():
    ''' random 모듈 테스트 '''
    values = [1, 2, 3, 4]
    print(random.choice(values))
    print(random.choice(values))
    print(random.choice(values))
    print(random.sample(values, 2))
    print(random.sample(values, 3))

    ''' values 리스트를 섞는다. '''
    random.shuffle(values)
    print(values)

    ''' 0~10의 임의의 정수를 생성한다. '''
    print(random.randint(0, 10))
    print(random.randint(0, 10))

if __name__ == '__main__':
    testing_random()
```

결과는 매번 다르게 출력된다.

```shell
3
3
2
[4, 3]
[1, 3, 4]
[4, 3, 2, 1]
4
3
```

### 1.8 numpy 패키지

대규모의 다차원 배열 및 행렬을 지원하며, 배열 연산에 쓰이는 수학함수 라이브러리를 제공한다.

#### array 메서드

**np.array()**
- 다차원 배열 생성 함수
- 예시 : 시퀀스의 시퀀스(리스트 또는 튜플)를 2차원 numpy 배열로 생성할 수 있다.

```python
>>> import numpy as np
>>> np.array(((11,12,13), (21,22,23), (31,32,33)))
[[11 12 13]
 [21 22 23]
 [31 32 33]]
```

**ndim** 속성은 배열의 차원 수를 반환한다.

```python
>>> import numpy as np
>>> a = np.array(((11,12,13), (21,22,23)))
>>> a.ndim
2
```

간단한 사용예제

```python
import numpy as np

def testing_numpy():
    ''' tests many features of numpy '''
    ax = np.array([1,2,3])
    ay = np.array([3,4,5])
    print(ax)
    # [1 2 3]
    print(ax * 2)
    # [2 4 6]
    print(ax + 10)
    # [11 12 13]
    print(np.sqrt(ax))
    # [1.         1.41421356 1.73205081]
    print(np.cos(ax))
    # [ 0.54030231 -0.41614684 -0.9899925 ]
    print(ax-ay)
    # [-2 -2 -2]
    print(np.where(ax<2, ax, 10))
    # [ 1 10 10]

    m = np.matrix([ax, ay, ax])
    print(m)
    # [[1 2 3]
    #  [3 4 5]
    #  [1 2 3]]
    print(m.T)
    # [[1 3 1]
    #  [2 4 2]
    #  [3 5 3]]

    grid1 = np.zeros(shape=(3,3), dtype=float)
    grid2 = np.ones(shape=(3,3), dtype=float)
    print(grid1)
    # [[0. 0. 0.]
    #  [0. 0. 0.]
    #  [0. 0. 0.]]
    print(grid2)
    # [[1. 1. 1.]
    #  [1. 1. 1.]
    #  [1. 1. 1.]]
    print(grid1[1]+10)
    # [10. 10. 10.]
    print(grid2[:,2]*2)
    # [2. 2. 2.]

if __name__ == '__main__':
    testing_numpy()
```

numpy 배열은 파이썬 list 보다 더 효율적이다.

- numpy 연산이 약 80배 정도 빠르다.

```python
import numpy as np
import time

def trad_version():
    t1 = time.time()
    X = range(10000000)
    Y = range(10000000)
    Z = []
    for i in range(len(X)):
        Z.append(X[i] + Y[i])
    return time.time() - t1

def numpy_version():
    t1 = time.time()
    X = np.arange(10000000)
    Y = np.arange(10000000)
    Z = X + Y
    return time.time() - t1

if __name__ == '__main__':
    print(trad_version())
    # 2.2733571529388428
    print(numpy_version())
    # 0.028986215591430664
```


### 1.9 연습문제

#### 1.9.1 n진법 변환

n진법의 숫자를 10진법으로 변환하는 코드작성(2\<= n \<=10)

- 테스트 실행함수

```python

def convert_to_decimal1(n, base):
    ''' 나머지, 나누기 연산 이용한 방법 '''
    digit = 1
    result = 0
    while 0 < n:
        n, m = n//10, n%10
        result += (m * digit)
        digit *= base
    return result

def convert_to_decimal2(number, base):
    ''' 내장함수 int 이용한 방법 '''
    result = int(str(number), base)
    return result

if __name__ == '__main__':
    number, base = 1001, 2
    assert(convert_to_decimal1(number, base) == 9)
    assert(convert_to_decimal2(number, base) == 9)
    print('테스트 통과!')
```


10진법의 숫자를 n진법로 변환하는 코드작성(2\<= n \<=10)

- 테스트 실행함수

```python
def convert_from_decimal(n, base):
    digit = 1
    result = 0
    while 0 < n:
        n, m = n // base, n % base
        result += (m * digit)
        digit *= 10
    return result

if __name__ == '__main__':
    number, base = 9, 2
    assert(convert_from_decimal(number, base) == 1001)
    print('테스트 통과!')
```


10진법의 숫자를 20이하 진법으로 변환하는 코드작성(2\<= n \<=20)

- 테스트 실행함수

```python
def convert_from_decimal_larger_bases(n, base):
    digit = '0123456789ABCDEFGHIJ'
    result = ''
    m = 1
    while 0 < n:
        n, m = n // base, n % base
        result = digit[m] + result
    return result

if __name__ == '__main__':
    number, base = 31, 16
    assert(convert_from_decimal_larger_bases(number, base) == '1F')
    print('테스트 통과!')
```


#### 1.9.2 최대 공약수

두 정수의 최대 공약수를 계산하는 코드작성

- 테스트 실행함수

```python
def find_gcd(a, b):
    ''' a가 b보다 큰 값일때 '''
    result = 0
    while b != 0:
        result = b
        a, b = b, a % b
    return result

if __name__ == '__main__':
    number1 = 21
    number2 = 12
    assert(find_gcd(number1, number2) == 3)
    print('테스트 통과!')
```


### 1.9.3 피보나치 수열

**피보나치 수열** : 첫째 및 둘째 항이 1이며, 그 이후 모든 항은 바로 앞 두항의 합인 수열

피보나치 수열의 특정 인덱스 값을 계산하는 코드작성

- 테스트 실행함수 (시간 복잡도 O(n))

- [ ] a 
- [x] a 

```python
def find_fibonacci_seq_iter(n):
    if n < 2: return n
    
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a+b
    return a

if __name__ == '__main__':
    n = 10
    assert(find_fibonacci_seq_iter(n) == 55)
    print('테스트 통과!')
```

**제너레이터(generator)** 이용한 방법

- `yield`문을 사용
- 전체 시퀀스를 한 번에 메모리에 생성하고 정렬할 필요 없이, 시퀀스를 순회할 수 있다.
- 제너레이터를 순회할 때마다 마지막으로 호출된 요소를 기억하고 다음 값을 반환한다.

```python
def fib_generator():
    a, b = 0, 1
    while True:
        yield b
        a, b = b, a+b

if __name__ == '__main__':
    fg = fib_generator()
    for _ in range(10):
        print(next(fg), end=' ')
```


### 1.9.4 소수

**소수** : 자신보다 작은 두 개의 자연수를 곱하여 만들 수 없는 1보다 큰 자연수

소수를 판단하는 코드작성

- 테스트 실행 함수

```python
import math
import random

def finding_prime(num):
    ''' 무차별 대입 방법 '''
    num = abs(num)
    if num < 4: return True
    for i in range(2, num):
        if num % i == 0:
            return False
    return True

def finding_prime_sqrt(num):
    ''' 제곱근 활용한 방법 '''
    num = abs(num)
    if num < 4: return True

    # 직접 연산한것 보다 math 라이브러리가 약 10% 정도 빠르다.
    sqrt = int(math.sqrt(num)) + 1
    # sqrt = int(num ** 0.5) + 1

    for i in range(2, sqrt):
        if num % i == 0:
            return False
    return True

def finding_prime_fermat(num):
    ''' 확률론적 테스트와 페르마의 소정리를 활용한 방법 '''
    if num <= 102:
        for a in range(2, num):
            if pow(a, num-1, num) != 1:
                return False
        return True
    else:
        for _ in range(100):
            a = random.randint(2, num-1)
            if pow(a, num-1, num) != 1:
                return False
        return True

if __name__ == '__main__':
    num1 = 17
    num2 = 20
    assert(finding_prime(num1) is True)
    assert(finding_prime(num2) is False)
    assert(finding_prime_sqrt(num1) is True)
    assert(finding_prime_sqrt(num2) is False)
    assert(finding_prime_fermat(num1) is True)
    assert(finding_prime_fermat(num2) is False)
    print('테스트 통과!')
```

random 모듈을 이용한 n비트 소수 생성하는 코드작성

```python
import math
import random
import sys

def finding_prime_sqrt(num):
    num = abs(num)
    if num < 4: return True

    sqrt = int(math.sqrt(num)) + 1
    for i in range(2, sqrt):
        if num % i == 0:
            return False
    return True

def generate_prime(num):
    while 1:
        p = random.randint(pow(2, num-2), pow(2, num-1)-1)
        p = 2 * p + 1
        if finding_prime_sqrt(p):
            return p

if __name__ == '__main__':
    if len(sys.argv) < 2:
        print("Usage: generate_prime.py number")
        sys.exit()
    else:
        num = int(sys.argv[1])
        print(generate_prime(num))
```

```shell
$ generate_prime.py 3
5 (또는 7)
```



## Chapter 02 내장 시퀀스 타입

시퀀스 타입은 다음과 같은 속성을 가진다.

- 멤버십(membership) 연산 : in 키워드 사용
- 크기(size) 함수 : len(seq)
- 슬라이싱(slicing) 속성 : seq[:-1]
- 반복성(iterability) : 반복문에 있는 데이터를 순회할 수 있음

Python에는 문자열, 튜플, 리스트, 바이트 배열, 바이트 등 5개의 내장 시퀀스 타입이 있다.

```python
l = []
print(type(l))
# <class 'list'>

s = ''
print(type(s))
# <class 'str'>

t = ()
print(type(t))
# <class 'tuple'>

ba = bytearray(b'')
print(type(ba))
# <class 'bytearray'>

b = bytes([])
print(type(b))
# <class 'bytes'>
```

### 2.1 깊은 복사와 슬라이싱 연산

python 불변 객체 타입

- 숫자
- 튜플
- 문자열
- 바이트

python 가변 객체 타입

- 리스트
- 바이트

#### 2.1.1 가변성

일반적으로 불변 객체 타입은 가변 객체 타입보다 효율적이다.

파이썬의 모든 변수는 객체 참조(reference)이므로 가변 객체 복사할 때는 주의해야 한다.

**리스트(list)의 깊은 복사 예제**

```python
my_list = [1, 2, 3, 4]
new_list = my_list[:]
new_list2 = list(my_list)
```

**셋(set)의 깊은 복사 예제**

```python
people = {'버피', '에인절', '자일스'}
slayers = people.copy()
slayers.discard('자일스')
slayers.remove('에인절')
print(slayers)
# {'버피'}

print(people)
# {'버피', '에인절', '자일스'}
```

**딕셔너리(dict)의 깊은 복사 예제**

```python
my_dict = {'hello': 'world'}
new_dict = my_dict.copy()
```

기타 객체의 깊은 복사는 copy 모듈을 사용한다.

```python
import copy

my_obj = 'other something object'
new_obj = copy.copy(my_obj)  # 얕은 복사(shallow copy)
new_obj2 = copy.deepcopy(my_obj)  # 깊은 복사(deep copy)
```

#### 2.1.2 슬라이싱 연산자

파이썬 시퀀스 타입의 슬라이싱 연산자 구문

```python
seq[시작]
seq[시작:끝]
seq[시작:끝:스텝]
```

슬라이싱 예제

```python
word = 'abcde'
print(word[0])    # a
print(word[1])    # b
print(word[-1])   # e
print(word[-2:])  # de
print(word[-0])   # a
print(word[::2])  # ace
```

### 2.2 문자열

#### 2.2.1 유니코드 문자열

`유니코드` : 전 세계 언어의 문자를 정의하기 위한 국제 표준 코드

```python
print(u'Hello\u0020World !')
# Hello World !
```

문자열 코드 크기

- 아스키코드 7bit
- ANSI코드 8ibt
- 유니코드 16bit

#### 2.2.2 문자열 메서드

`join()`

A.join(B) : 리스트 B에 있는 모든 문자열을 하나의 단일 문자열 A로 결합한다.

```python
slayer = ['버피', '앤', '아스틴']
print(' '.join(slayer))
# 버피 앤 아스틴

print('-'.join(slayer))
# 버피-앤-아스틴

print(''.join(slayer))
# 버피앤아스틴

print(''.join(reversed(slayer)))
# 아스틴앤버피
```

`ljust()`, `rjust()`

A.ljust(width, fillchar) : 문자열 A `맨 처음`부터 문자열을 포함한 길이 width 만큼 문자 fillchar로 채운다.  
A.rjust(width, fillchar) : 문자열 A `맨 끝`부터 문자열을 포함한 길이 width 만큼 문자 fillchar로 채운다.

```python
name = '스칼렛'
print(name.ljust(20, '-'))
# 스칼렛-----------------
print(name.rjust(20, '-'))
# -----------------스칼렛
```

`format()`

A.format() : 문자열 A에 변수를 추가하거나 형식화하는데 사용한다.

```python
print("{0} {1}".format('Hello,', 'Python!'))
# Hello, Python!
print("이름 : {who}, 나이 : {age}".format(who='제임스', age=20))
# 이름 : 제임스, 나이 : 20
print("이름 : {who}, 나이 : {0}".format(21, who='에이미'))
# 이름 : 에이미, 나이 : 21

# python 3.1이상
print("{} {} {}".format('파이썬', '자료구조', '알고리즘'))
# 파이썬 자료구조 알고리즘

import decimal
# !s : 문자열 형식
# !r : 표현 형식
# !a : 아스키코드 형식 
print("{0} {0!s} {0!r} {0!a}".format(decimal.Decimal('99.9')))
# 99.9 99.9 Decimal('99.9') Decimal('99.9')
```

`문자열 언패킹`

연산자 ** : 함수로 전달하기 적합한 키-값 딕셔너리가 생성된다.

locals() 메서드는 현재 스코프에 있는 지역변수를 딕셔너리로 반환한다.

```python
hero = '버피'
num = 999
print("{number}: {hero}".format(**locals()))
# 999: 버피
```

`splitlines()`

A.splitlines() : 문자열 A에 대해 줄 바꿈 문자를 기준으로 분리한 결과를 문자열 리스트로 반환한다.

```python
slayers = "로미오\n줄리엣"
print(slayers.splitlines())
# ['로미오', '줄리엣']
```

`split()`

A.split(t, n) : 문자열 A의 왼쪽부터 문자열 t를 기준으로 정수 n번만큼 분리한 문자열 리스트를 반환한다. t의 기본값은 공백(' ')이다.

```python
slayers = "버피 크리스-메리 16"
print(slayers.split())
# ['버피', '크리스-메리', '16']
print(slayers.split('-'))
# ['버피 크리스', '메리 16']
```

 A.rsplit(t, n) : 문자열 A의 오른쪽부터 문자열 t를 기준으로 정수 n번만큼 분리한 문자열 리스트를 반환한다.

 `strip(s)`, `lstrip(s)`, `rstrip(s)`

각각 양쪽, 왼쪽, 오른쪽 문자열 s를 삭제한다. s의 기본값은 공백(' ')이다.

`swapcase()`

A.swapcase() : 문자열 A에서 대소문자를 반전한 문자열의 복사본을 반환한다.

`index(), find(), rindex(), rfind()`

A.index(sub, start, end) : 문자열A 에서 부분 문자열 sub의 인덱스 위치를 반환하며, 실패하면 ValueError 예외를 발생시킨다.

A.find(sub, start, end) : 문자열 A에서 부분 문자열 sub의 인덱스 위치를 반환하며, 실패하면 -1을 반환한다. 

인덱스 start와 end는 문자열 범위이며, 생략할 경우 전체 문자열에서 부분 문자열 sub를 찾는다.

`count()`

A.count(sub, start, end) : 문자열 A에서 인덱스 start, end 범위 내의 부분 문자열 sub가 나온 횟수를 반환한다.

`replace()`

A.replace(old, new, max_replace) : 문자열 A에서 문자열 old를 대체 문자열 new로 max_replace만큼 변경한 문자열을 복사본을 반환한다.

`f-strings`

f-strings은 파이썬 3.6부터 사용가능하다.

```python
name = 'abcde'
print(f"이름은 {name} 입니다.")
# 이름은 abcde 입니다.
print(f"이름은 {name!r} 입니다.")
# 이름은 'abcde' 입니다.
```


### 2.3 튜플

`튜플` : 쉼표로 구분된 값으로 이루어지는 불변 시퀀스 타입

튜플은 값과 쉼표를 사용해 생성한다.

괄호안에 쉼표없이 값 하나만 넣으면 튜플이 생성되지 않는다.

```python
t1 = 1234, 'Hello!'
print(t1[0])
# 1234
print(t1)
# (1234, 'Hello!')

t2 = t1, (1,2,3)  # 중첩가능
print(t2)
# ((1234, 'Hello!'), (1, 2, 3))
```


#### 2.3.1 튜플 메서드

A.count(x) : 튜플 A에 담긴 항목 x의 개수를 반환한다.

A.index(x) : 튜플 A에 담긴 항목 x의 인덱스 위치를 반환한다.

#### 2.3.2 튜플 언패킹

파이썬에서 모든 반복 가능한(iterable) 객체는 `시퀀스 언패킹 연산자 *`를 사용하여 언패킹 할 수 있다.

```python
x, *y = (1, 2, 3, 4)
print(x)  # 1
print(y)  # [2, 3, 4]

*x, y = (1, 2, 3, 4)
print(x)  # [1, 2, 3]
print(y)  # 4
```

#### 2.3.3 네임드 튜플

파이썬 표준 모듈 collections에는 `네임드 튜플` 시퀀스 타입이 있다.

- 네임드 튜플은 일반 튜플과 비슷한 성능과 특성을 가진다.
- 튜플 항목을 인덱스 위치, 이름으로 참조할 수 있다.

collections.namedtuple() 메서드의 첫번째 인수는 만들고자 하는 사용자 정의 튜플 데이터 타입의 이름이다.