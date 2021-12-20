---
title: "PostgreDB, pgadmin4 도커 설치 방법"
date: 2021-04-29T16:29:43+09:00
description: 도커의 개요 및 windows10 버전 설치 방법
menu:
  sidebar:
    name: Docker PostgreDB Pgadmin4 Setup
    identifier: docker-postgredb-pgadmin4-setup
    parent: docker
    weight: 30
tags: ["docker", "postgredb"]
categories: ["Docker", "PostgreDB"]
---



## Postgres Docker 이미지 설치

Postgres Version : 13.2

### 다운로드 및 설정

아래 명령어를 실행한다.

```bash
$ docker run -p 15432:5432 --name postgres -e POSTGRES_PASSWORD=password1! -d postgres
```

- `-d` : 백그라운드에서 컨테이너 실행
- `-p 15432:5432` : 호스트와 컨테이너 간의 배포(publish)포트/바인드(bind)포트
    - 호스트 15432번 포트를 컨테이너 5432번 포트에 매핑
- `--name` : 생성할 컨테이너 이름
- `-e` : 도커 컨테이너의 환경변수 설정
    - `POSTGRES_PASSWORD` : PostgresDB 관리자 비밀번호

실행 결과

```bash
Unable to find image 'postgres:latest' locally
latest: Pulling from library/postgres
75646c2fb410: Pull complete
2355d0ffeb55: Pull complete
7e98825f6d67: Pull complete
cfd3ce06be45: Pull complete
c7b7bb83e8f7: Pull complete
c67869305108: Pull complete
19614baa7ddd: Pull complete
af508737d813: Pull complete
b60c3437a436: Pull complete
424da1ff3ea9: Pull complete
076f107f1898: Pull complete
7b398ea488bf: Pull complete
e0fcc114ae29: Pull complete
67d927dd9b8a: Pull complete
Digest: sha256:b25265ac1dfa19224fd47dd9f5744aa177248fd64e89f407446559cc7dbc7a23
Status: Downloaded newer image for postgres:latest
47d090f5c4a593b833c237cef181f960571e71004ace5ebc804907f4feae1433
```

### 도커 이미지 확인

도커에서 실행중인 이미지 정보확인

```bash
$ docker ps -a
CONTAINER ID   IMAGE               COMMAND                  CREATED          STATUS                    PORTS                     NAMES
47d090f5c4a5   postgres            "docker-entrypoint.s…"   16 seconds ago   Up 14 seconds             0.0.0.0:15432->5432/tcp   postgres
```

### 도커 이미지 접속

postgres 이미지 bash쉘 접속

```bash
$ docker exec -it postgres /bin/bash
```

- `-it` : 컨테이너를 종료하지 않고, 터미널의 입력을 컨테이너로 전달하기 위해서 사용

### Postgres DB 및 User 생성

1. 데이터베이스 접속

    ```bash
    psql -U postgres
    ```

2. 데이터베이스 생성

    ```sql
    CREATE DATABASE "{database_name}";
    ```

3. User 생성

    ```sql
    CREATE USER {user_name} WITH PASSWORD '{user_password}';
    ```

4. 권한부여

    ```sql
    GRANT {permissions} ON DATABASE {db_name} TO {user_name};
    ```

    - 사용예시

    ```sql
    GRANT ALL ON DATABASE "ABCD_DB" TO abcd_user;
    ```

5. 계정 권한 확인

    ```sql
    \du
    ```

### 도커 컨테이너 삭제

도커 이미지가 실행중일 경우 두 가지 방법으로 삭제할 수 있다.

- 컨테이너 종료 후 삭제

    ```bash
    $ docker stop {CONTAINER ID}
    $ docker rm {CONTAINER ID}
    ```

- 컨테이너 강제 삭제

    ```bash
    $ docker rm -f {CONTAINER ID}
    ```

### 모든 컨테이너 종료 및 삭제

```bash
$ docker stop $(docker ps -a -q)
$ docker rm $(docker ps -a -q)
```

### 볼륨 컨테이너

컨테이너를 삭제하면 데이터도 같이 삭제되므로 데이터 저장용으로 사용하는 `볼륨 컨테이너`를 같이 사용한다.

1. 볼륨 컨테이너 생성

    ```bash
    docker volume create pgdata
    ```

2. 볼륨 컨테이너 연동하여 postgresDB 이미지 생성

    ```bash
    docker run -p 15432:5432 --name postgres -e POSTGRES_PASSWORD=password1! -d -v pgdata:/var/lib/postgresql/data postgres
    ```

    - `-v` : 생성된 볼륨 컨테이너 지정하는 것으로 호스트(host) 컴퓨터의 파일 시스템의 특정 경로를 컨테이너의 파일 시스템의 특정 경로로 마운트(mount)한다.

    위에서 설정한 DB설정을 그대로 반복하여 실행한다.

3. 볼륨 정보확인

    - 볼륨 리스트 확인

    ```bash
    $ docker volume list
    DRIVER    VOLUME NAME
    local     pgdata
    ```

    - 볼륨 위치 및 상세정보 확인

    ```bash
    $ docker volume inspect pgdata
    [
        {
            "CreatedAt": "2021-04-01T07:43:03Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "/var/lib/docker/volumes/pgdata/_data",
            "Name": "pgdata",
            "Options": {},
            "Scope": "local"
        }
    ]
    ```

## pgadmin4 이미지 설치

pgadmin4 Version : 13.2

### 설치

이미지를 다운받는다.

```bash
docker pull dpage/pgadmin4
```

### 도커 이미지 실행

아래의 설정으로 다운받은 이미지 사용하여 도커컨테이너를 실행한다.

```bash
docker run -p 80:80 --name pgadmin4 -e PGADMIN_DEFAULT_EMAIL="account@site.com" -e PGADMIN_DEFAULT_PASSWORD="password1!" -d dpage/pgadmin4
```

- `-p 80:80` : 호스트와 컨테이너 간의 배포(publish)포트/바인드(bind)포트
    - 호스트 80번 포트를 컨테이너 80번 포트에 매핑
- `--name` : 생성할 컨테이너 이름
- `-e` : 도커 컨테이너의 환경변수 설정
    - `PGADMIN_DEFAULT_EMAIL` : pgadmin4 로그인계정 (필수설정)
    - `PGADMIN_DEFAULT_PASSWORD` : 계정 비밀번호 (필수설정)
- `-d` : 백그라운드에서 컨테이너 실행
- `dpage/pgadmin4` : 사용할 이미지 이름

### 실행

웹 브라우저에서 `localhost:80` 주소로 접속하여 설정한 계정과 비밀번호로 접속하면된다.

![](../images/docker-postgresdb-pgadmin4-setup-guide_1-1.png?raw=true)
