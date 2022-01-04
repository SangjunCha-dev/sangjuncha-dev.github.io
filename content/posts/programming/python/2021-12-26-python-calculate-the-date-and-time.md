---
title: "python 날짜와 시간 계산"
date: 2021-12-26T22:02:26+09:00
description: python 날짜와 시간 계산
tags: ["python"]
categories: ["Python"]
---


---

python 날짜 및 시간 계산은 python 표준 라이브러리 `datetime`, 확장 라이브러리 `dateutil` 2개의 라이브러리가 있으며 단위의 차이가 있다.
- datetime : 마이크로초, `밀리초`, 초, 분, 시, 일, 주 단위 사용
- dateutil : 마이크로초, 초, 분, 시, 일, 주, `월`, `년` 단위 사용


---

## 1. datetime 라이브러리

날짜와 시간을 조작하는 클래스를 제공하는 python 표준 라이브러리

### 1.1 사용예시

- now : 현재 날짜 및 시간

    ```python
    import datetime as dt

    now = dt.datetime.now()

    print(f"now = {now}")
    ```

    > now = 2021-12-27 11:24:27.172476  

- microsecond

    ```python
    print(f"microsecond before = {now - timedelta(microseconds=1)}")
    print(f"microsecond next   = {now + timedelta(microseconds=1)}")
    ```

    > microsecond before = 2021-12-26 22:08:42.685501  
    > microsecond next   = 2021-12-26 22:08:42.685503  

- millisecond

    ```python
    print(f"millisecond before = {now - timedelta(milliseconds=1)}")
    print(f"millisecond next   = {now + timedelta(milliseconds=1)}")
    ```

    > millisecond before = 2021-12-26 22:08:42.684502  
    > millisecond next   = 2021-12-26 22:08:42.686502  

- second

    ```python
    print(f"second before = {now - timedelta(seconds=1)}")
    print(f"second next   = {now + timedelta(seconds=1)}")
    ```

    > second before = 2021-12-26 22:08:41.685502  
    > second next   = 2021-12-26 22:08:43.685502  

- minute

    ```python
    print(f"minute before = {now - timedelta(minutes=1)}")
    print(f"minute next   = {now + timedelta(minutes=1)}")
    ```

    > minute before = 2021-12-26 22:07:42.685502  
    > minute next   = 2021-12-26 22:09:42.685502  

- hour

    ```python
    print(f"hour before = {now - timedelta(hours=1)}")
    print(f"hour next   = {now + timedelta(hours=1)}")
    ```

    > hour before = 2021-12-26 21:08:42.685502  
    > hour next   = 2021-12-26 23:08:42.685502  

- day

    ```python
    print(f"day before = {now - timedelta(days=1)}")
    print(f"day next   = {now + timedelta(days=1)}")
    ```

    > day before = 2021-12-25 22:08:42.685502  
    > day next   = 2021-12-27 22:08:42.685502  

- week

    ```python
    print(f"week before = {now - timedelta(weeks=1)}")
    print(f"week next   = {now + timedelta(weeks=1)}")
    ```

    > week before = 2021-12-19 22:08:42.685502  
    > week next   = 2022-01-02 22:08:42.685502  

- 복합사용

    ```python
    print(f"week before = {now - timedelta(days=1, hours=1, minutes=1)}")
    print(f"week next   = {now + timedelta(days=1, hours=1, minutes=1)}")
    ```

    > week before = 2021-12-25 21:07:42.685502  
    > week next   = 2021-12-27 23:09:42.685502  



## 2. dateutil 라이브러리

datetime 보다 확장된 기능을 가진 라이브러리

### 2.1. 라이브러리 설치

```bash
> pip install dateutil
```

### 2.2. 사용예시

- now : 현재 날짜 및 시간

    ```python
    from datetime import datetime
    from dateutil.relativedelta import relativedelta

    now = datetime.now()

    print(f"now = {now}")
    ```

    > now = 2021-12-26 22:23:22.916446  

- microsecond

    ```python
    print(f"microsecond before = {now - relativedelta(microseconds=1)}")
    print(f"microsecond next   = {now + relativedelta(microseconds=1)}")
    ```

    > microsecond before = 2021-12-26 22:23:22.916445  
    > microsecond next   = 2021-12-26 22:23:22.916447  

- second

    ```python
    print(f"second before = {now - relativedelta(seconds=1)}")
    print(f"second next   = {now + relativedelta(seconds=1)}")
    ```

    > second before = 2021-12-26 22:23:21.916446  
    > second next   = 2021-12-26 22:23:23.916446  

- minute

    ```python
    print(f"minute before = {now - relativedelta(minutes=1)}")
    print(f"minute next   = {now + relativedelta(minutes=1)}")
    ```

    > minute before = 2021-12-26 22:22:22.916446  
    > minute next   = 2021-12-26 22:24:22.916446  

- hour

    ```python
    print(f"hour before = {now - relativedelta(hours=1)}")
    print(f"hour next   = {now + relativedelta(hours=1)}")
    ```

    > hour before = 2021-12-26 21:23:22.916446  
    > hour next   = 2021-12-26 23:23:22.916446  

- day

    ```python
    print(f"day before = {now - relativedelta(days=1)}")
    print(f"day next   = {now + relativedelta(days=1)}")
    ```

    > day before = 2021-12-25 22:23:22.916446  
    > day next   = 2021-12-27 22:23:22.916446  

- week

    ```python
    print(f"week before = {now - relativedelta(weeks=1)}")
    print(f"week next   = {now + relativedelta(weeks=1)}")
    ```

    > week before = 2021-12-19 22:23:22.916446  
    > week next   = 2022-01-02 22:23:22.916446  

- month

    ```python
    print(f"month before = {now - relativedelta(months=1)}")
    print(f"month next   = {now + relativedelta(months=1)}")
    ```

    > month before = 2021-11-26 22:23:22.916446  
    > month next   = 2022-01-26 22:23:22.916446  

- year

    ```python
    print(f"year before = {now - relativedelta(years=1)}")
    print(f"year next   = {now + relativedelta(years=1)}")
    ```

    > year before = 2020-12-26 22:23:22.916446  
    > year next   = 2022-12-26 22:23:22.916446  


---

## 3. 예시코드 Git

[python-calculate-the-date-and-time](https://github.com/SangjunCha-dev/blog/tree/main/python/python-calculate-the-date-and-time)


---

## 참고(Reference)

- [datetime.timedelta - 날짜의 차이](https://wikidocs.net/104836)
- [Python 날짜 계산 방법](https://jsikim1.tistory.com/143)
- [dateutil docs](https://dateutil.readthedocs.io/en/stable/)