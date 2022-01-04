---
title: "python json 파일 읽기/쓰기 성능"
date: 2021-12-06T14:44:57+09:00
description: python json 파일 읽기/쓰기 성능
tags: ["python"]
categories: ["Python"]
---


---

python 언어에서 대용량 json 읽기/쓰기 성능은 사용방법과 라이브러리에 따라서 성능 차이가 있다.  
테스트 기준은 동일한 json 파일을 100번씩 반복 실행하였다.  
json 라이브러리의 파일 읽기/쓰기 모두 byte 모드가 상대적으로 빠르다.  


---

라이브러리 버전

- python : 3.8.10
- ujson : 5.1.0
- orjson : 3.6.5


---

## 1. json

```
=========================
json_r        = 0.045 sec
json_w_dump   = 0.186 sec
json_w_dumps  = 0.065 sec
=========================
json_rb       = 0.034 sec
json_wb_dumps = 0.055 sec
```

### 1.1. read

```python
with open('github.json', "r") as json_file:
    data = json.load(json_file)
```

### 1.2. write_dump

`json.dump` 함수는 ASCII 모드에서만 동작한다.

```python
with open('github_w_dump.json', "w") as json_file:
    json.dump(data, json_file)
```

### 1.3. write_dumps

```python
with open('github_w_dumps.json', "w") as json_file:
    data = json.dumps(data)
    json_file.write(data)
```

### 1.4. read byte

```python
with open('github.json', "rb") as json_file:
    data = json.load(json_file)
```

### 1.5. write byte dumps

```python
with open('github_wb_dumps.json', "wb") as json_file:
    data = json.dumps(data).encode('utf-8')
    json_file.write(data)
```


---

## 2. ujson

UltraJSON은 파이썬 3.7버전이상을 지원하는 순수 C언어로 작성된 고속 JSON 인코더 및 디코더이다.

```
==========================
ujson_r        = 0.052 sec
ujson_w_dumps  = 0.060 sec
==========================
ujson_rb       = 0.037 sec
ujson_wb_dumps = 0.049 sec
```

### 2.1. read

```python
with open('github.json', "r") as json_file:
    data = ujson.loads(json_file.read())
```

### 2.2. write dumps

```python
with open('github_w_dumps.json', "w") as json_file:
    data = ujson.dumps(data)
    json_file.write(data)
```

### 2.3. read byte

```python
with open('github.json', "rb") as json_file:
    data = ujson.load(json_file)
```

### 2.4. write byte dumps

```python
with open('github_wb_dumps.json', "wb") as json_file:
    data = ujson.dumps(data).encode('utf-8')
    json_file.write(data)
```


---

## 3. orjson

orjson은 파이썬 3.7버전이상을 지원하는 가장 빠르고 정확한 파이썬 JSON 라이브러리이다.

```
===========================
orjson_r        = 0.038 sec
orjson_w_dumps  = 0.049 sec
===========================
orjson_rb       = 0.024 sec
orjson_wb_dumps = 0.035 sec
```

### 3.1. read

```python
with open('github.json', "r") as json_file:
    data = orjson.loads(json_file.read())
```

### 3.2. write dumps

```python
with open('github_w_dumps.json', "w") as json_file:
    data = orjson.dumps(data).decode('utf-8')
    json_file.write(data)
```

### 3.3. read byte

```python
with open('github.json', "rb") as json_file:
    data = orjson.loads(json_file.read())
```

### 3.4. write byte dumps

```python
with open('github_wb_dumps.json', "wb") as json_file:
    data = orjson.dumps(data)
    json_file.write(data)
```


---

## 4. banchmark

성능은 `orjson > ujson > json` 순으로 빠르다.  

| Library | read (ms) | write (ms) | vs. orjson |
|---------|-----------|------------|------------|
| orjson  |     0.024 |      0.035 |       1.00 |
| ujson   |     0.037 |      0.049 |       1.45 |
| json    |     0.034 |      0.055 |       1.50 |

<br><br>

아래 결과는 orjson 라이브러리에서 안내하는 banchmark로 데이터 형식에 따른 성능 차이가 있다.

This measures serializing the github.json fixture as compact (52KiB) or pretty (64KiB):

| Library    |   compact (ms) | pretty (ms)   | vs. orjson   |
|------------|----------------|---------------|--------------|
| orjson     |           0.06 | 0.07          | 1.0          |
| ujson      |           0.18 | 0.19          | 2.8          |
| rapidjson  |           0.22 |               |              |
| simplejson |           0.35 | 1.49          | 21.4         |
| json       |           0.36 | 1.19          | 17.2         |


---

## 5. 예시코드 Git

[python-json-read-write-performance](https://github.com/SangjunCha-dev/blog/tree/main/python/python-json-read-write-performance)


---

## 참고(Reference)

- [Benchmark of Python JSON libraries](https://artem.krylysov.com/blog/2015/09/29/benchmark-python-json-libraries/)
- [orjson](https://github.com/ijl/orjson)