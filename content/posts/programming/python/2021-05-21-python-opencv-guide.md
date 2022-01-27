---
title: "python opencv 사용법"
date: 2021-05-21T17:43:21+09:00
description: python opencv 사용법
# menu:
#   sidebar:
#     name: opencv 사용법
#     identifier: python-opencv-guide
#     parent: python
#     weight: 30
tags: ["python", "opencv"]
categories: ["Python", "Opencv"]
---


---

설치버전 : opencv-python 4.5.1.48
- [docs url](https://docs.opencv.org/4.5.1/d4/da8/group__imgcodecs.html)

라이브러리 설치

```python
pip install opencv-python
```

라이브러리 import

```python
import cv2
```


---

## 이미지 파일 읽기

- `cv2.imread`(filename[, flags]) -> retval

```python
img = cv2.imread(image_full_path)
```

|매개변수 이름|설명|
|---|---|
|filename   |로드할 파일 이름|
|flags      |cv2 값을 사용할 수 있는 플래그|


---

## 이미지 파일 저장

- `cv2.imwrite`(filename, img[, params]) -> retval
- 이미지 형식은 파일 이름 확장자에 따라 선택됨

```python
cv2.imwrite(image_full_path, img)
```


---

## 메모리 버퍼로 인코딩

- 이미지를 ext형식으로 변환하여 메모리 버퍼로 인코딩
- `cv2.imencode`(ext, img[, params]) -> refval, buf

```python
retval, buffer = cv2.imencode('.png', img)
```

|매개변수 이름|설명|
|---|---|
|ext    |출력 형식을 정의하는 파일 확장명|
|img    |변환할 이미지|
|params |이미지 형식별 매개 변수|
|buf    |이미지 형식에 맞게 크기가 조정된 출력 버퍼|


---

## 이미지 보기

- 변수에 담겨있는 이미지 보기
- `cv2.imshow`(winname, mat)

```python
cv2.imshow('Window Name', img)
cv2.waitKey()  # 키입력까지 대기
cv2.destroyAllWindows()  # 윈도우창 닫기
```


---

## 이미지 정보 조회

- 이미지 파일 세로,가로,채널 조회
- `img.shape`

```python
img = cv2.imread(image_full_path)
h, w, c = img.shape
print('height: {}, width: {}, channel: {}'.format(h, w, c))
```
