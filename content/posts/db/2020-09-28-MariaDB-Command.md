---
title: "MariaDB SQL 명령어 (MariaDB SQL Command)"
date: 2020-09-30T14:41:18+09:00
description: MariaDB SQL 명령어 예시
# menu:
#   sidebar:
#     name: mariadb-sql-command
#     identifier: mariadb-sql-command
#     parent: db
#     weight: 30
tags: ["db", "mariadb", "sql"]
categories: ["DB", "mariaDB"]
---



# 명령어

### 1. database 확인

    ```sql
    SHOW DATABASES;
    ```

### 2. database 생성

    ```sql
    CREATE DATABASE database_name;
    ```

### 3. 특정 database 접속

    ```sql
    USE database_name;
    ```

### 3-1. mysql database 접속

    ```sql
    USE mysql;
    ```

### 4. 사용자 확인
    - (MariaDB[mysql])

    ```sql
    SELECT HOST, USER, PASSWORD FROM USER;
    ```

### 5. 사용자 계정 생성
    -  'id'@'localhost' 이면 로컬에서만 접속 가능

    ```sql
    CREATE USER 'user_id'@'%' IDENTIFIED BY 'user_password';
    ```

    ```sql
    CREATE USER 'user_id' IDENTIFIED BY 'user_password';
    ```

### 6. 사용자 권한 부여

    ```sql
    GRANT ALL PRIVILEGES ON database_name.* TO 'user_id'@'%';
    ```

    ```sql
    GRANT ALL PRIVILEGES ON database_name.* TO 'user_id';
    ```

### 7. 새로고침

    ```sql
    FLUSH PRIVILEGES;
    ```

### 8. 사용자 계정 삭제
    - '사용자'@'접속위치'

    ```sql
    DROP USER [사용자]@[서버];
    ```