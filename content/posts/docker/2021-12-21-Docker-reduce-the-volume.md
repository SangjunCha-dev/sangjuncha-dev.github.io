---
title: "도커 경량화 이미지 만들기"
date: 2021-12-21T11:24:13+09:00
description: 도커 경량화 이미지 만들기
menu:
  sidebar:
    name: 도커 경량화 이미지 만들기
    identifier: docker-reduce-the-volume
    parent: docker
    weight: 30
tags: ["docker"]
categories: ["Docker"]
---



## Docker Image 경량화의 장점

- 저장공간 절약
- 이미지 빌드 및 배포시간 단축
- 클라우드 서비스를 이용한 배포의 경우 비용 절약

## 1. 가벼운 Base image 사용

Base image에는 사용하지 않은 기능들이 포함되어 있기때문에 Debian계열과 Alpine 계열등 다양한 Base image를 사용하여 용량을 줄일 수 있다.  
- 단, 필요한 패키지나 파일이 없어 별도의 설치가 필요할 수 있다.

기본 python 이미지와 slim형 이미지를 각각 빌드한다.

image-test1

```
FROM python:3.8.10
```

image-test2

```
FROM python:3.8.10-slim-buster
```

각각 빌드된 이미지 크기는 다음과 같다.

```
# docker images
REPOSITORY       TAG       IMAGE ID       CREATED         SIZE
image-test1    latest    3249573f28c9   5 months ago    883MB
image-test2    latest    004c04ba9ac3   2 months ago    114MB
```

`python:3.8.10-slim-buster`
- postgreDB 라이브러리 사용할 경우 `psycopg2`가 아닌 `psycopg2-binary`를 사용해야한다.
- libmagic 라이브러리 사용할 경우 별도로 `libmagic1`설치가 필요하다.
    ```
    RUN apt-get update && apt-get install -y --no-install-recommends libmagic1 && rm -rf /var/lib/apt/lists/*
    ```

## 2. Dockerfile 명령어 체인방식 사용

Dockerfile에서 RUN명령을 개별로 실행시 실행이 끝날때마다 중간 이미지가 생성된다.  
체인 명령으로 실행하면 중간이미지가 적게 만들어지기 때문에 크기와 실행시간을 줄일 수 있다.  
- 현재 Docker 버전에서의 영향은 미미하다.

Dockerfile 개별 실행 명령

```
ENV APP_HOME=/app
RUN mkdir $APP_HOME
RUN mkdir $APP_HOME/.static_root 
RNU mkdir $APP_HOME/media
```

Dockerfile 체인 실행 명령

```
ENV APP_HOME=/app
RUN mkdir $APP_HOME && mkdir $APP_HOME/.static_root && mkdir $APP_HOME/media
```

## 3. 패키지 관리

Package 매니저로 패키지를 설치할 경우 보통 사용하지않은 패키지가 함께 설치된다.

apt-get 사용하여 패키지 설치시 용량을 최소화 하는 방법

- --no-install-recommends 최소 설치 옵션을 사용한다.
    ```
    apt-get install -y --no-install-recommends cron
    ```
- apt-get clean 명령어 사용한다.
    - /var/cache/apt/archives 디렉터리에 패키지 다운로드한 파일 삭제
    ```
    apt-get clean
    ```
- /var/lib/pat/lists 디렉터리의 패키지 리스트 파일 삭제
    ```
    rm -rf /var/lib/apt/lists/*
    ```


## 4. Docker Layer 관리

Docker Image에는 Layer라는 Stack구조가 있다. 도커 이미지를 효과적으로 관리하기 위해 Stack형태로 이미지가 누적되며, 동일한 Stack이 동일한 Repository에 존재하면 해당 Layer를 사용하도록 구현되어 있다.
Docker Image는 Container 용량을 결정하고, 관리측면에서 Local Repository나 Docker Registry의 용량에 영향을 준다.
- Dockerfile 작성 시 라이브러리 설치 등 수정이 적은 명령어를 상단에 프로젝트 코드 등 데이터 수정이 빈번한 명령어는 하단에 작성하면 이미지 중복 생성을 최소화할 수 있다.

image-test1

```
FROM debian
RUN apt-get update && apt-get install -y cron
RUN rm -rf /var/lib/apt/lists/*
```

image-test2

```
FROM debian
RUN apt-get update && apt-get install -y cron && rm -rf /var/lib/apt/lists/*
```

각각 빌드된 이미지 크기는 다음과 같다.

```
# docker images
REPOSITORY    TAG       IMAGE ID       CREATED            SIZE
image-test1   latest    211a762c5a58   2 minutes ago      230MB
image-test2   latest    297340498b12   1 minutes ago      213MB
```

동일한 명령을 수행하여도 차이가 나는 이유는 `Layer별 참조 관계` 때문이다.
따라서 사용하지 않고 삭제가 필요한 경우에는 반드시 같은 Layer에서 처리해야 한다.

Docker Layer는 Union Mount라는 Linux Mount 기술을 활용하여 여러 Layer를 통합하여 하나의 이미지로 관리한다.

#### Docker Layer 별 용량 확인

`docker history [IMAGE]` : 해당 Docker 이미지의 각 layer마다 사용된 용량을 확인할 수 있다.

```
# docker history [IMAGE]
...
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
148db3ce1bdc   16 minutes ago   RUN /bin/sh -c apt-get update && apt-get ins…   2.24MB    buildkit.dockerfile.v0
<missing>      2 weeks ago      /bin/sh -c #(nop)  CMD ["python3"]              0B
<missing>      2 weeks ago      /bin/sh -c set -ex;   wget -O get-pip.py "$P…   8.31MB
<missing>      2 weeks ago      /bin/sh -c #(nop)  ENV PYTHON_GET_PIP_SHA256…   0B
<missing>      2 weeks ago      /bin/sh -c #(nop)  ENV PYTHON_GET_PIP_URL=ht…   0B
<missing>      2 weeks ago      /bin/sh -c #(nop)  ENV PYTHON_SETUPTOOLS_VER…   0B
<missing>      2 weeks ago      /bin/sh -c #(nop)  ENV PYTHON_PIP_VERSION=21…   0B
...
```

`--no-trunc`옵션 : CREATED BY 설명의 생략된 부분까지 확인 할 수 있다.

```
# docker history --no-trunc [IMAGE]
```

## 5. 배포 시 불필요한 빌드 도구를 설치하지 않기

소스코드를 빌드해서 이미지에 포함시키면, 불필요한 빌드 도구가 차지하는 공간을 줄일 수 있다.

## 6. .dockerignore 활용하기

Docker build 시 명령어 COPY 등을 통해서 프로젝트 파일을 컨테이너로 복사할 때 지정한 디렉터리, 파일을 자동으로 제외된다.
- Docker는 Go 언어기반으로 파일 매칭도 Go 언어규칙을 적용한다.
- 임시파일, Git, Docker 관련, 비공개 정보 파일 등 예외 처리한다.
- docker volumes 참조와 겹치지 않도록 한다.(volumes 우선적용)

## 7. multi-stage 빌드

multi-stage 빌드는 Dockerfile 1개에 FROM 구문을 여러 개 두는 방식이다.  
각 FROM 명령문 기준으로 스테이지를 구분하여 특정 스테이지 빌드 과정에서 생성된 것 중 사용되지 않거나 불필요한 것을 무시하고, 필요한 부분만 가져와서 새로운 베이스 이미지에서 빌드할 수 있다.

- `COPY --from=builder` : 전 단계 스테이지 빌드에서 생성된 특정 결과물을 새로운 베이스 이미지로 복사하는 옵션

image-test1

```
FROM python:3.8.10-slim-buster
RUN pip install django
```

image-test2

```
FROM python:3.8.10-slim-buster AS builder
RUN pip install django

FROM python:3.8.10-slim-buster
COPY --from=builder /usr/local/lib/python3.8/site-packages /usr/local/lib/python3.8/site-packages
```

각각 빌드된 이미지 크기는 다음과 같다.

```
# docker images
REPOSITORY     TAG       IMAGE ID       CREATED         SIZE
image-test1    latest    62fad51305c5   2 minutes ago   155MB
image-test2    latest    004c04ba9ac3   1 minutes ago   151MB
```

- 사용하는 라이브러리에 따라 용량 차이가 더 많이 나올 수 있다.
- 외부 설정이 까다로운 경우에는 multi-stage를 사용하지 않을 수 있다.

## 참조 URL
- [컨테이너 이미지 생성시 고려사항](https://waspro.tistory.com/692)
- [Docker - image 크기 줄이기](https://velog.io/@idnnbi/Docker-image-%ED%81%AC%EA%B8%B0-%EC%A4%84%EC%9D%B4%EA%B8%B0)
- [Alpine을 사용하면 Python Docker를 50배 더 느리게 만들 수 있다.](https://pythonspeed.com/articles/alpine-docker-python/)
- [도커 이미지 잘 만드는 방법](https://jonnung.dev/docker/2020/04/08/optimizing-docker-images/)
