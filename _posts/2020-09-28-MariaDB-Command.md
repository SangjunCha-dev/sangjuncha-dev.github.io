---
title: MariaDB SQL 명령어 (MariaDB SQL Command)
author: Sangjun Cha
date: 2020-09-30 14:41:18 +0900
categories: [DB, mariaDB]
tags: [db, mariadb, sql]
pin: false
---



# 1. 명령어

- database 확인

    ```sql
    SHOW DATABASES;
    ```

- database 생성

    ```sql
    CREATE DATABASE database_name;
    ```

- 해당 database 사용

    ```sql
    USE database_name;
    ```

- mysql database 접속

    ```sql
    USE mysql;
    ```

- 사용자 확인(MariaDB[mysql])

    ```sql
    SELECT HOST, USER, PASSWORD FROM USER;
    ```

- 사용자 계정 생성 'id'@'localhost' 이면 로컬에서만 접속 가능

    ```sql
    CREATE USER 'user_id'@'%' IDENTIFIED BY 'user_password';
    ```

    ```sql
    CREATE USER 'user_id' IDENTIFIED BY 'user_password';
    ```

- 사용자 권한 주기

    ```sql
    GRANT ALL PRIVILEGES ON database_name.* TO 'user_id'@'%';
    ```

    ```sql
    GRANT ALL PRIVILEGES ON database_name.* TO 'user_id';
    ```

- 새로고침

    ```sql
    FLUSH PRIVILEGES;
    ```

- 사용자 계정 삭제 '사용자'@'접속위치'

    ```sql
    DROP USER [사용자]@[서버];
    ```