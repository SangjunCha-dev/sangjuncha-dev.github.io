---
title: "Django crontab 라이브러리 사용법"
date: 2021-12-09T15:44:54+09:00
description: Django crontab 라이브러리 사용법
tags: ["django", "crontab"]
categories: ["Django", "crontab"]
---


---

django-crontab 라이브러리는 OS의 cron/crontab 서비스를 사용하는것으로 해당 서비스 설치가 되지 않았다면 별도로 설치를 해야함.
- windows 환경에서는 docker를 설치하여 진행


---

## 1. 라이브러리 설치

```bash
$ pip install django-crontab
```


---

## 2. django 설정

1. 임의의 app 디렉터리내 cron.py 생성
    아래의 함수를 작성한다.

    - 반복 실행할 임의의 함수 선언
        ```python
        def hello_every_minute():
            print("hello world")
        ```

2. settings.py

    - `django_crontab` 앱 추가
        ```python
        INSTALLED_APPS = [
            'django_crontab',
            ...
        ]
        ```
    - `CRONJOBS` 변수 선언
        - 첫번째 매개변수 : 실행주기 설정으로 기존 cron 사용법(분,시,일,월,요일)과 동일하다.
            - `* * * * *` : 매분마다 실행
            - `*/10 * * * *` : 10분마다 실행
            - `0 * * * *` : 매시간마다 실행
            - `0 0 * * *` : 자정마다 실행
        - 두번째 매개변수 : 반복 실행할 함수
        - 세번째 매개변수 : cron 실행로그 저장 경로 (선택사항)
        ```python
        ...
        CRONJOBS = [
            ('* * * * *', 'app.cron.hello_every_minute', '>> /app/log/cron.log'),
        ]
        ```
    - crontab 프로젝트의 settings 파일 경로 지정
        ```python
        ...
        CRONTAB_DJANGO_SETTINGS_MODULE = 'project.settings'
        ```


---

## 3. crontab 명령어

- cron 작업스케줄 추가
    ```bash
    $ python manage.py crontab add
    ```

    > Starting periodic command scheduler: cron.
    > adding cronjob: (2d7f1e3059afc75b51927955cb7ccb92) -> ('* * * * *', 'app.cron.hello_every_minute', '>> /app/log/cron.log')

- cron 작업스케줄 조회
    ```bash
    $ python manage.py crontab show
    ```

    > Currently active jobs in crontab:  
    > 2d7f1e3059afc75b51927955cb7ccb92 -> ('* * * * *', 'app.cron.hello_every_minute', '>> /app/log/cron.log')  

- cron 작업스케줄 삭제
    ```bash
    $ python manage.py crontab remove
    ```

    > removing cronjob: (2d7f1e3059afc75b51927955cb7ccb92) -> ('* * * * *', 'app.cron.hello_every_minute', '>> /app/log/cron.log')


---

## 4. 실행 결과

- crontab 실행 후 해당 경로의 log 파일 확인

    ```bash
    $ cat cron.log
    ```

    > hello


---

## 5. Docker-compose 사용시 설정

- dockerfile 설정
    ```
    ...
    RUN apt-get update && apt-get -y install --no-install-recommends cron
    ...
    ```

- docker-compose 설정
    ```
    services:
      django:
        ...
        command: bash -c "
            service cron start &&
            python3 manage.py crontab add"
    ```


---

## 6. Docker-compose 실행

- 이미지 빌드 및 컨테이너 백그라운드 실행

    ```
    $ docker-compose -f docker-compose.yml up -d --build
    ```


---

## 7. 예시코드 Git

[django-crontab](https://github.com/SangjunCha-dev/blog/tree/main/django/django-crontab)



## 참고(Reference)

- [Docs](https://github.com/kraiz/django-crontab)
- [Django-Crontab 활용방법](https://systemtrade.tistory.com/477)