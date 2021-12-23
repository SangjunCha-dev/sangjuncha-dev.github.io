---
title: "PostgreDB 설치 (PostgreDB Setup)"
date: 2020-09-29T15:07:59+09:00
description: PostgreDB 설치 방법
# menu:
#   sidebar:
#     name: postgredb-setup
#     identifier: postgredb-setup
#     parent: db
#     weight: 30
tags: ["db", "postgredb"]
categories: ["DB", "PostgreDB"]
---



설치환경 : Windows 10

# 1. PostgreDB 설치

## 1.1 설치파일 다운로드

윈도우즈용 설치 파일은 현재 EnterpriseDB사가 배포

> Download URL  
> [https://www.enterprisedb.com/downloads/postgres-postgresql-downloads](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)  

## 1.2 PostgreSQL 설치

- postgresql-[버전]-windows-x64.exe 파일을 실행
- Next 클릭
- 설치할 소프트웨어를 선택하는 대화창(Select Components)에서 아래 2개만 설치
    > [ ]  `PostgreSQL Server`  
    > [ ]  `Command Line Tools ` 
- Next 클릭 후, PostgreSQL 서버를 사용하면서 자료저장 위치 지정(default)
- 기본 데이터베이스 관리자 postgres계정 `비밀번호` 지정
- 포트 지정 > `15432`(기본포트가 아닌 다른포트번호) 으로 수정후 [Next] 클릭



# 2. PostgreDB 서버 사용하기

## 2.1 PGADMIN 설치파일 다운로드

클라이언트 - 데이터베이스 조작 GUI 도구

> Download URL  
> [https://www.pgadmin.org/download/](https://www.pgadmin.org/download/)

## 2.2 PGADMIN 설치

pgadmin4-[버전]-x64.exe 실행

기본 설정대로 [Next] 클릭하여 설치

## 2.3 PGADMIN 실행

특징  
- 자체 웹서버 실행  
- `웹 브라우저`로 접속하여 데이터베이스 조작가능
- pgAdmin 응용프로그램을 실행시 OS의 기본 웹브라우저가 실행되면서 pgAdmin 웹프로그램을 실행하는 url을 호출
- 첫 접속시 최상위 관리용 비밀번호 지정
- 왼쪽에 있는 Servers 글자 클릭시 `기본 PostgreSQL 서버` 접속됨

## 2.4 PGADMIN 사용

- 화면 왼쪽 서버 트리 영역(객체 트리 영역)에서 데이터베이스 - 스키마까지 선택
- 상단의 (Tools)도구들 -> (`Query Tools`)쿼리 도구 선택하면, SQL명령 입력 및 실행 가능

    ```sql
    select version()
    ```

## 2.5 Windows PostgreSQL 외부접속 허용설정

- `pg_hba.conf` 접속 허용 IP 설정

    ```
    C:\Program Files\PostgreSQL\{Version}\data\**pg_hba.conf**
    ```
    ```
    C:\Program Files\PostgreSQL\13\data\**pg_hba.conf**
    ```

    pg_hba.conf

    ```
    # TYPE  DATABASE        USER            ADDRESS                 METHOD

    # 특정 IP 허용
    host 	all				all				192.168.0.X/32		scram-sha-256

    # 특정 IP대역 허용
    host 	all				all				192.168.0.X/24		scram-sha-256
    ```
