---
title: "golang 도커 이미지 만들기"
date: 2021-12-22T15:22:06+09:00
description: golang docker image build
tags: ["docker", "go"]
categories: ["Docker", "Go"]
---



간단한 golang 웹 프로그램을 Docker 이미지로 실행방법으로  
golang 코드보다는 docker 설정 위주로 설명한다.

## 예제 코드

사용예시는 아래의 주소를 git clone 받는다.

```bash
> git clone https://github.com/olliefr/docker-gs-ping
```

- [docker-gs-ping](https://github.com/olliefr/docker-gs-ping)

## 예제 프로그램 실행

git clone 받은 프로젝트 경로에서 터미널을 실행하고 진행한다.

```bash
> go run main.go
```

설정한 웹주소로 접속 요청시 간단한 response 메세지를 응답한다.

```bash
> curl localhost:8080
Hello, Docker! <3
```

## Dockerfile

dockerfile 명칭은 `Dockerfile.<something>` 또는 `<something>.Dockerfile` 형식으로 생성한다.


도커 명령어

- `FROM` : 기본 이미지 지정
- `WORKDIR` : 작업 디렉터리 지정
- `COPY` : 소스코드 복사
	- https://docs.docker.com/engine/reference/builder/#copy
- `EXPOSE` : 도커 컨테이너의 Port 설정
- `RUN` : 새로운 도커 레이어를 생성하고 명령어를 실행한다. 패키지 설치 등에 사용된다.
- `CMD` : default 명령어나 파라미터를 설정한다. docker run 실행 시 실행할 명령어를 지정하지 않으면 default 명령이 실행된다.

Dockerfile

```
FROM golang:1.17-alpine

WORKDIR /app

# Go modules 다운로드
COPY go.mod .
COPY go.sum .
RUN go mod download

COPY *.go ./

# go build
RUN go build -o /docker-gs-ping

EXPOSE 8080

# go 프로그램 실행
CMD ["/docker-gs-ping"]
```



도커 이미지 빌드

```bash
> docker build --tag docker-gs-ping .
```



`docker image ls` : 도커 로컬이미지 조회

```bash
> docker image ls
REPOSITORY       TAG      IMAGE ID       CREATED          SIZE
docker-gs-ping   latest   4f0670869ed5   12 seconds ago   540MB
```



`docker image tag` : 도커 이미지 태그 지정


```bash
# Usage:  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
> docker image tag docker-gs-ping:latest docker-gs-ping:v1.0
```

```bash
> docker image ls
REPOSITORY       TAG      IMAGE ID       CREATED         SIZE
docker-gs-ping   latest   4f0670869ed5   3 minutes ago   540MB
docker-gs-ping   v1.0     4f0670869ed5   3 minutes ago   540MB
```

## Multi-stage builds

도커 이미지 용량 줄이는 방법 중 하나로 프로그램 실행에 필요한 파일들만으로 이미지를 생성하는 방법이다.

- `FROM golang:1.16-buster AS build` : 해당 이미지 별명을 지정한다.
- `COPY --from=build [src_dir] [dst_dir]` : from으로 지정한 이미지의 디렉터리를 현재 이미지 디렉터리로 복사한다. 
- `ENTRYPOINT` : 컨테이너를 실행 파일로 사용할 때 정의되어야 한다.  
사용법은 `CMD`와 같다.
	- CMD와 ENTRYPOINT의 조합 예시  
	[ENTRYPOINT / CMD combinations](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact)
- `gcr.io/distroless/base-debian10` : go에서 공식 지원하는 도커 이미지로 bash와 같은 쉘 , linux 패키지 관리자, 기타 프로그램 등이 없는 경량화된 이미지이다. 
- `USER nonroot:nonroot` : 컨테이너 권한 설정으로 도커 컨테이너는 기본적으로 root권한으로 실행된다.  
따라서 컨테이너 안에서 실행중인 애플리케이션을 해킹하면 해커들이 도커 호스트에 대한 루트권한을 얻을 수 있기 때문에 별도의 사용자권한으로 제한하는것을 권장한다.  
하지만 컨테이너 내부에서 외부 볼륨 참조에 의한 디렉터리 및 파일 등을 조작이 필요한 경우 적절한 권한으로 설정하거나 해당 USER 옵션을 사용하지 않을 수 있다.  

Dockerfile.multistage

```
## Build
FROM golang:1.16-buster AS build

WORKDIR /app

COPY go.mod .
COPY go.sum .
RUN go mod download

COPY *.go ./

RUN go build -o /docker-gs-ping

## Deploy
FROM gcr.io/distroless/base-debian10

WORKDIR /
COPY --from=build /docker-gs-ping /docker-gs-ping

EXPOSE 8080
USER nonroot:nonroot

ENTRYPOINT ["/docker-gs-ping"]
```



도커 이미지 빌드

```bash
> docker build -t docker-gs-ping:multistage -f Dockerfile.multistage .
```

```bash
>docker image ls
REPOSITORY       TAG          IMAGE ID       CREATED          SIZE
docker-gs-ping   multistage   04b0984d8007   5 seconds ago    27.1MB      
docker-gs-ping   latest       4f0670869ed5   13 minutes ago   540MB
```

## 도커 컨테이너 실행

- `--publish` or `-p`: 외부 접속포트와 컨테이너포트를 연동시키는 명령이다.
	- -p [host_port]:[container_port]
- `-d` : detached mode로 백그라운드에 실행 컨테이너가 실행된다.

```
> docker run -d -p 8080:8080 docker-gs-ping
```



`docker ps` : 도커 컨테이너 실행 확인

```bash
> docker ps
CONTAINER ID   IMAGE            COMMAND             CREATED         STATUS         PORTS                    NAMES
4984da23dfa3   docker-gs-ping   "/docker-gs-ping"   8 minutes ago   Up 8 minutes   0.0.0.0:8080->8080/tcp   stoic_shaw
```



컨테이너가 정상적으로 실행되었다면 접속시 아래와 같은 응답 값을 받을 수 있다.

```bash
> curl localhost:8080
Hello, Docker! <3
```

## 참조 URL

- [Docker docs Build your Go image](https://docs.docker.com/language/golang/build-images/)
