---
title: "libmagic 라이브러리 사용법 (libmagic Library Guide)"
date: 2020-11-05T09:53:16+09:00
description: libmagic 라이브러리 사용법 (libmagic Library Guide)
menu:
  sidebar:
    name: libmagic 라이브러리 사용법
    identifier: libmagic-library-guide
    parent: python
    weight: 30
tags: ["python", "library"]
categories: ["Python", "Guide"]
---



[미디어 타입(media type), MIME 타입(MIME type)](https://ko.wikipedia.org/wiki/%EB%AF%B8%EB%94%94%EC%96%B4_%ED%83%80%EC%9E%85)

실행환경 : windows 10

## 라이브러리 설치

```bash
pip install libmagic
pip install python-magic-bin
```

libmagic : 파일타입을 MIME 타입으로 확인해주는 라이브러리
- magic은 리눅스 환경에서 실행되는 프로그램
- 윈도우즈 환경에서는 윈도우용 magic 설치가 필요함  

python-magic-bin : 윈도우용 magic 라이브러리


## 예제 코드

```python
import magic

r1 = magic.from_file("test1.txt")
r2 = magic.from_file("test1.txt", mime=True)
print(f"filetype = {r1}, \nmime = {r2}")   
# filetype = UTF-8 Unicode text, with CRLF line terminators,
# mime = text/plain

file_data = open('test1.txt', 'r').read(1024)
r3 = magic.from_buffer(file_data)
r4 = magic.from_buffer(file_data, mime=True)
print(f"filetype = {r3}, \nmime = {r4}")
```

UnicodeDecodeError 에러 발생시  
`UnicodeDecodeError: 'cp949' codec can't decode byte 0xd3 in position 23: illegal multibyte sequence`

file open encoding 지정하여 디코딩 가능하도록 코드 수정

```python
file_data = open('test1.txt', 'r', encoding='UTF-8').read(1024)
r3 = magic.from_buffer(file_data)
r4 = magic.from_buffer(file_data, mime=True)
print(f"filetype = {r3}, \nmime = {r4}")
# filetype = data,
# mime = application/octet-stream
```