---
title: "mssql 2017 도커 이미지 설치 및 컨테이너 실행방법"
date: 2021-05-06T22:29:30+09:00
description: 도커의 개요 및 windows10 버전 설치 방법
menu:
  sidebar:
    name: Docker MSSQL Setup
    identifier: docker-mssql-setup
    parent: docker
    weight: 30
tags: ["docker", "mssql"]
categories: ["Docker", "MSSQL"]
---



# mssql docker 도커 설치 방법

## 도커 허브에서 이미지 검색

```bash
docker search <검색할 이미지 이름>
```

## pull - 도커 이미지 다운받기

```bash
docker pull mcr.microsoft.com/mssql/server:2017-latest
```

## docs 예시

```powershell
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<YourStrong@Passw0rd>"
    -p 1433:1433 --name mssql2017 -h mssql2017
    -v <host directory>/data:/var/opt/mssql/data
    -v <host directory>/log:/var/opt/mssql/log
    -v <host directory>/secrets:/var/opt/mssql/secrets
    -d mcr.microsoft.com/mssql/server:2017-latest
```

Docker on Windows 의 호스트 볼륨 매핑은 현재 `/var/opt/mssql` 디렉토리가 아닌
`/var/opt/mssql/data` 등의 하위 디렉터리를 호스트 머신에 매핑할 수 있다.

## 사용 명령어 예시

```bash
docker volume create mssqldata
```

```powershell
docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Airiss123!" -p 11433:1433 --name mssql2017_1 -h mssql2017_1 -v mssqldata:/var/opt/mssql/data -d mcr.microsoft.com/mssql/server:2017-latest
```

|변수|설명|
|---|---|
|`-p`|호스트 환경의 TCP 포트를 컨테이너의 TCP 포트로 매핑|
|`--name`|컨테이너 이름 지정|
|`-h`|컨테이너 호스트 이름을 명시적으로 설정|
|`-e "ACCEPT_EULA"`|최종 사용자 사용권 계약 수락|
|`-e "SA_PASSWORD"`|암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 특수문자를 사용하는 모든 조합을 포함해아함|
|`-d`|백그라운드에서 컨테이너 실행|


## 기본 파일 위치 변경

```powershell
-e "MSSQL_DATA_DIR=/my/file/path"
-v /my/host/path:/my/file/path
```

참고사이트
- SQL Server Docker 컨테이너 구성 및 사용자 지정
    - https://docs.microsoft.com/ko-kr/sql/linux/sql-server-linux-docker-container-configure?view=sql-server-2017&pivots=cs1-cmd
- Docker + MSSQL 개발하기
  - https://hyesunzzang.tistory.com/91
