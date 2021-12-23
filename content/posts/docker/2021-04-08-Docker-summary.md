---
title: "도커의 개요 및 windows10 버전 설치 방법"
date: 2021-04-08T19:04:55+09:00
description: 도커의 개요 및 windows10 버전 설치 방법
# menu:
#   sidebar:
#     name: Summary Setup Windows
#     identifier: docker-summary-setup-windows
#     parent: docker
#     weight: 21
tags: ["docker"]
categories: ["Docker"]
---



## 개요

{{< img src="../images/docker-summary_0-1.png" align="center" >}}

도커(Docker)는 리눅스 컨테이너를 기반으로 특정 서비스를 패키징하고 배포할 수 있는 오픈소스 프로그램이다.

- **컨테이너** : 리눅스 커널 네임 스페이스, cgroup의 기능을 활용하여 호스트 시스템의 모든 프로세스와 격리된 시스템의 프로세스

## 도커를 사용하는 이유

도커는 소프트웨어 버전관리, 애플리케이션 배포, 개발환경 구성등 사전에 생성하고 도커 이미지를 배포하고 배포한 이미지를 컨테이너에 담아서 사용된다.

- **도커 이미지** : 컨테이너의 파일시스템과 애플리케이션 실행에 필요한 모든 항목(모든 종속성, 구성, 스크립트, 바이너리, 환경변수, 실행하는 기본 명령)이 포함된다.

{{< img src="../images/docker-summary_0-2.png" align="center" >}}

`도커파일(Docker File)`을 만들어서 "특정 소프트웨어를 컨테이너에 담아서 구동시킬 것이다."를 명시해주고 `빌드(Build)`하면, 도커 이미지가 생성됩니다. 그리고 해당 도커 이미지를 `구동(run)`시키면 도커 컨테이너에서 실행됩니다.

## VM(Virtual Machine)과 컨테이너(Container) 차이점

`가상 머신(Virtual Machine)` : 하드웨어의 가상화 기능으로 Guest OS를 통해 소프트웨어를 실행한다.
- 예를들면 윈도우 운영체제를 사용하는 Host OS에서 리눅스라는 Guest OS를 실행한다.
- Host OS와 Guest OS는 서로 의존적이지 않아 각 가상 머신마다 Guest OS를 실행하여야 한다.
- Guest OS의 I/O 기능을 Host OS를 통해서 실행되기 때문에 속도가 느리다. 또한 설치 용량도 크다.

|속도|메모리 사용량|설치 용량|
|:---:|:---:|:---:|
|느림|많음|많음(GB단위)|

{{< img src="../images/docker-summary_0-3.png" align="center" >}}

`컨테이너(Container)` : 별도의 Guest OS 없이 도커 엔진(Docker Engine)에서 동작하여 바로 소프트웨어를 실행한다.
- Guest OS를 사용하지 않아 실행과정이 단순화되어 성능적으로 빨라지고 메모리 용량도 적게 사용한다.
- Host OS와 도커 컨테이너 간에 의존성이 존재하며 운영체제 환경(Kernel)을 공유한다.

|속도|메모리 사용량|설치 용량|
|:---:|:---:|:---:|
|빠름|적음|적음(MB단위)|


## 설치

설치환경 : Windows 10
docker version : 3.2.2

Download URL - https://www.docker.com/get-started

다운로드 페이지에서 실행파일을 다운받아 설치한다.

`Docker Desktop` 실행한다.

설치 후 아래와 같은 에러 발생시 

![](../images/docker-summary_1-1.png?raw=true)


## 오류 해결방법

1. Hyper-V가 비활성화되었거나 설치되지 않은 경우
    - 관리자 권한으로 PowerShell 실행
    - Hyper-V 활성화 후 시스템 재시작  
    `dism.exe /Online /Enable-Feature:Microsoft-Hyper-V /All`  
    ![](../images/docker-summary_1-2.png?raw=true)

2. Hyper-V 기능이 이미 활성화되어 있지만 작동하지 않는 경우
    - 관리자 권한으로 PowerShell 실행
    - 하이퍼 바이저 활성화 후 시스템 재시작  
    `bcdedit /set hypervisorlaunchtype auto`

3. 1,2번 방법으로도 문제가 지속될 경우

    - Hyper-V 프로그램이 손상되었을 수 있어 재설치 해야한다.
    - 제어판 - 프로그램 - Windows 기능 켜기/끄기 - Hyper-V 관련요소 체크 해제 후 확인 클릭
    - 시스템 재시작 후 같은 방법으로 Hyper-V 관련요소 체크 후 확인 클릭  
    ![](../images/docker-summary_1-3.png?raw=true)

4. 3번 방법으로도 문제가 지속될 경우

    - BIOS에서 CPU 가상화 기능이 활성화 되어있는지 확인하여 비활성화인 경우 활성화 시킨다.

        |CPU 제조사|가상화 기능|
        |:---:|:---:|
        |Intel|VT-x|
        |AMD|SVM|


## 참고사항

다른 가상화 솔루션이 실행되고있는경우 실행되지 않을 수 있다.

## WSL2 설치

시스템 재시작 후 WSL2 설치 안내 메세지에서 [kernel update](https://aka.ms/wsl2kernel) 링크에서 `x64 머신용 최신 WSL2 Linux 커널 업데이트 패키지`를 다운받아 업데이트한다.
- WSL : 윈도우에서 경량 가상화 기술을 사용하여 리눅스를 구동할 수 있도록 도와주는 기능  
    ![](../images/docker-summary_1-4.png?raw=true)

그 후 Restart 버튼 클릭하여 도커를 재실행한다.

## Docker 대시보드

{{< img src="../images/docker-summary_1-5.png" align="center" >}}

도커에서 docker tutorial 실행 후 아래의 튜토리얼 진행

http://localhost/tutorial/ 