---
title: Python Opencv 사용 방법
author: Sangjun Cha
date: 2021-04-29 16:29:43 +0900
categories: [Python, Opencv]
tags: [python, opencv]
pin: false
---



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
