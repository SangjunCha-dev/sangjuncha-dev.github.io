---
title: Python 자료구조와 알고리즘 by 미아 스타인
author: Sangjun Cha
date: 2021-04-29 16:29:43 +0900
categories: [Docker]
tags: [docker]
pin: false
---

# Part 1 헬로, 자료구조!

## Chapter 01 숫자

1.1 정수 int()

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


1.2 부동 소수점 float()

1bit 부호, 8bit 지수, 23bit 유효 숫자 자릿수

1.2.1 부동소수점끼리 비교하기

이진수 분수로 표현으로 소수점의 정확한 비교는 어렵지만,

동등성 테스트로 사전에 정의된 정밀도 범위 내에서 비교는 가능한다.

unittest 모듈의 assertAlmostEqual() 메소드 같은 접근법 사용

```python
>>> def a(x, y, places=7):
        return rount(abs(x-y), places) == 0
```

1.2.2 정수와 부동소수점 메서드

나누기 `연산자 /` 는 항상 부동소수점을 `연산자 //` 는 정수를 반환한다.

`divmod(x, y)` : x를 y로 나눌 때, 몫과 나머지를 반환

```python
>>> divmod(45, 6)
(7, 3)
```

`round(x, n)`
- n이 양수인 경우, x를 소수점 이하 n번째 자리로 반올림한 값을 반환한다.
- n이 음수인 경우, x를 정수 n번째 자리에서 반올림한 값을 반환한다. 

`as_interger_ratio()` : 부동소수점을 분수로 표현

```python
>>> 2.75.as_integer_ratio()
(11, 4)
```


1.3 복소수

1.4 fraction 모듈

분수를 다룰 수 있다.

1장_숫자/1_testing_floats.py
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

```bash
테스트 통과!
```

1.5 decimal 모듈

정확한 10진법의 부동소수점 숫자가 필요한 경우, 부동소수점 불변 타입인 `decimal.Decimal` 사용할 수 있다.