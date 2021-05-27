---
title: Python Numpy/Opencv 사용법
author: Sangjun Cha
date: 2021-04-29 16:29:43 +0900
categories: [Python, Numpy]
tags: [python, numpy]
pin: false
---



# numpy 사용법

선형대수 기반의 python 라이브러리
- 루프없이 배열 연산이 가능하여 연산속도가 빠름

설치버전 : numpy 1.20.2

## import 라이브러리

```python
import numpy as np
```

## 생성

### arange

`np.arange`([start,] stop, [step,] dtype)

지정한 숫자 범위의 array 생성함수

|매개변수 이름|설명|
|---|---|
|start  | |
|stop   | |


```python
np.arange(10)
# [0 1 2 3 4 5 6 7 8 9]

np.arange(0, 5, 0.5)
# [0. 0.5 1. 1.5 2. 2.5 3. 3.5 4. 4.5]
```

### zeros

`np.zeros`(shape, dtype)

0으로 선언된 array 생성

```python
np.zeros((2,4))
# [[0. 0. 0. 0.]
#  [0. 0. 0. 0.]]

np.zeros(shape=(5,), dtype=np.int8)
# [0 0 0 0 0]
```

### ones

`np.ones`(shape, dtype)

1로 선언된 array 생성

```python
np.ones((2,4))
# [[1. 1. 1. 1.]
#  [1. 1. 1. 1.]]

np.ones(shape=(5,), dtype=np.int8)
# [1 1 1 1 1]
```

### empty

`np.empty`(shape, dtype)

초기화되지 않은 array 생성

```python
np.empty(shape=(2,4), dtype=np.int8)
# [[   0    0 -128   63]
#  [   0    0 -128   63]]

np.empty(shape=(5,))
# [1.13941795e-311 0.00000000e+000 6.95331628e-310 1.13949703e-311 0.00000000e+000]
```

### full

`np.full`(shape, fill_value, dtype)

지정한 값으로 채워진 array 생성

```python
np.full(shape=(2,4), fill_value=5, dtype=np.int8)
# [[5 5 5 5] 
#  [5 5 5 5]]
```

### iinfo

`np.iinfo`(dtype)

지정한 타입의 최댓값, 최솟값 얻기

```python
np.iinfo(np.int8)
# Machine parameters for int8
# ---------------------------------------------------------------
# min = -128
# max = 127
# ---------------------------------------------------------------

np.iinfo(np.int8).min
# -128

np.iinfo(np.int8).max
# 127
```

### something_like

`zeros_like`, `ones_like`, `empty_like`, `full_like`(shape, dtype)

`_like`는 지정된 array의 shape 크기만큼 like로 지정된 값의 크기로 array 반환

```python
exp_array = np.arange(8).reshape(2,4)

np.zeros_like(exp_array)
# [[0 0 0 0]
#  [0 0 0 0]]

np.ones_like(exp_array)
# [[1 1 1 1]
#  [1 1 1 1]]

np.empty_like(exp_array)
# [[1 0 8 0]
#  [4 0 4 0]]
```

### random

`np.random.rand`(shape)

(0, 1) 범위의 난수 array 생성

```python
np.random.rand(5)
# [0.3966061  0.90252864 0.42164379 0.29060846 0.92335056]

np.random.rand(2, 4)
# [[0.69718482 0.87517471 0.1429788  0.11088321]
#  [0.21131956 0.00997556 0.39068644 0.74352718]]
```

`random.randint`(min, max, size)

(최소값, 최대값)의 범위에서 임의의 정수 array 생성

```python
np.random.randint(1, 5, size=5)
# [1 1 3 1 2]

np.random.randint(1, 5, size=(2, 4))
# [[2 2 4 3]
#  [4 1 3 1]]
```

`random.randn`(shape)

표준정규분포에서 샘플링된 난수로 array 생성

```python
np.random.randn(5)
# [-0.21797309  0.43488398 -2.56991621  0.62828858  0.12212911]

np.random.randn(2, 3)
# [[ 0.83103422  0.29135557 -2.41843352 -0.62250774]
#  [ 0.35476654  1.70384283  0.38937963  0.99051868]]

sigma, mu = 1.5, 2.0
sigma * np.random.randn(5) + mu
```

표준정규분포 N(1, 0)가 아닌  
평균 μ, 표준편차 σ 를 갖는 정규분포 N(μ, σ2)의 난수 생성  
`σ * np.random.randn(shape) + μ` 형태로 사용할 수 있음

```python
sigma, mu = 1.5, 2.0
sigma * np.random.randn(5) + mu
```

## 차원 출력 및 변경

행렬의 차원을 `shape`로 표현

사용예제

```python
exp_array = np.array([
        [1,2,3,4],
        [1,2,3,4]
    ])
```

| | | | |
|---|---|---|---|
|1|2|3|4|
|1|2|3|4|

### Shape

1차원 4, 2차원 2 -> (4,2) 표현

```python
exp_matrix = exp_array.shape

print(exp_matrix)
# (2, 4)
```

### reshape

`np.reshape`(n, ...)

배열의 차원크기 변경  
- 배열의 요소 갯수는 유지하며 차원만 조정함

```python
exp_array.reshape(8,)
# [1 2 3 4 1 2 3 4]

exp_array.reshape(2,2,2)
# [[[1 2]
#   [3 4]]

#  [[1 2]
#   [3 4]]]

# -1은 입력받은 array의 size로 자동 맞춤
exp_array.reshape(-1)
# [1 2 3 4 1 2 3 4]

exp_array.reshape(-1,2)
# [[1 2]
#  [3 4]
#  [1 2]
#  [3 4]]
```

### flatten

배열을 1차원 배열로 변환함

```python
exp_array.flatten()
# [1 2 3 4 1 2 3 4]
```


## 올림, 내림, 반올림

사용예제

```python
exp_array = np.array([-3.16, -2.58, -1.72, -0.83, 0.39, 1.26, 2.71, 3.46])
```

### 올림

`np.ceil`(array)

```python
np.ceil(exp_array)
# [-3. -2. -1. -0.  1.  2.  3.  4.]
```

### 내림

```python
np.trunc(exp_array)
# [-3. -2. -1. -0.  0.  1.  2.  3.]
```

### 반올림

`np.around`(array)

0.5 기준으로 반올림

```python
np.around(exp_array)
# [-3. -2. -2. -1.  0.  1.  3.  3.]
```

`np.round`(array, n)

n 소수정 자리까지 반올림

```python
np.around(exp_array, 1)
# [-3.1 -2.5 -1.7 -0.8  0.3  1.2  2.7  3.4]
```

`np.rint`

가장 가까운 정수로 반올림

```python
np.rint(exp_array)
# [-3. -2. -2. -1.  0.  1.  3.  3.]
```

### 0에 수렴하는 정수

`np.fix`(array)

```python
np.fix(exp_array)
# [-3. -2. -1. -0.  0.  1.  2.  3.]
```


## 조건 연산

### where

`where(조건, 참, 거짓)`

```python
np.where(0 < exp_array, True, False)
# [False False False False  True  True  True  True]

np.where(0 < exp_array, exp_array, False)
# [0.   0.   0.   0.   0.39 1.26 2.71 3.46]
```



# opencv 사용법

설치버전 : opencv-python 4.5.1.48
- [docs url](https://docs.opencv.org/4.5.1/d4/da8/group__imgcodecs.html)

import 라이브러리 

```python
import cv2 as cv
```


## cv.imread

이미지 파일 읽기

`cv.imread`(filename[, flags]) -> retval

|매개변수 이름|설명|
|---|---|
|filename   |로드할 파일 이름|
|flags      |cv 값을 사용할 수 있는 플래그|

```python
img = cv.imread(image_full_path)
```


## cv.imwrite

이미지 파일 저장

`cv.imwrite`(filename, img[, params]) -> retval

이미지 형식은 파일 이름 확장자에 따라 선택됨

```python
cv.imwrite(image_full_path, img)
```


## cv.imencode

이미지를 ext형식으로 변환하여 메모리 버퍼로 인코딩

`cv.imencode`(ext, img[, params]) -> refval, buf

|매개변수 이름|설명|
|---|---|
|ext    |출력 형식을 정의하는 파일 확장명|
|img    |변환할 이미지|
|params |이미지 형식별 매개 변수|
|buf    |이미지 형식에 맞게 크기가 조정된 출력 버퍼|

```python
retval, buffer = cv.imencode('.png', img)
```

## cv.imshow

변수에 담겨있는 이미지 보기

`cv.imshow`(winname, mat)

```python
cv.imshow('Window Name', img)
cv.waitKey()  # 키입력까지 대기
cv.destroyAllWindows()  # 윈도우창 닫기
```
