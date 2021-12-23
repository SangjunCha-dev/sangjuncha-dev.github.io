---
title: "Windows10 WSL2(Windows Subsystem for Linux) 사용법"
date: 2021-05-07T13:36:31+09:00
description: Windows 10 WSL2(Windows Subsystem for Linux) 사용법
# menu:
#   sidebar:
#     name: Windows10 WSL2
#     identifier: windows10-wsl2
#     parent: windows
#     weight: 30
tags: ["windows10", "wsl"]
categories: ["Windows10", "WSL"]
---



## WSL(Windows Subsystem for Linux)2

리눅스용 윈도우 하위 시스템(Windows Subsystem for Linux)은 Windows 10에서 네이티브로 리눅스 실행 파일을 실행하기 위한 호환성 계층 프로그램이다.

## 사전 설정

- microsoft store에서 `Windows Terminal` 설치한다.
- `Windows Terminal` 프로그램을 관리자 권한으로 실행한다.

### WSL2 활성화를 위한 DISM 명령어 실행후 재부팅

```powershell
> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

### Windows Store 에서 설치하는 모든 Linux 배포판 포맷을 WSL 2로 설정

```powershell
> wsl --set-default-version 2
```

## 실습 및 팁

### 기존에 설치한 WSL 배포 목록확인

microsoft store에서 `Ubuntu 18.04 LTS` 설치

```powershell
> wsl -l -v
  NAME                   STATE           VERSION
* docker-desktop-data    Running         2
  docker-desktop         Running         2
  Ubuntu-18.04           Running         2
```

윈도우 탐색기에서 `\\wsl$` 경로로 접속하면 wsl 리눅스 배포판 접속가능하다.

### wsl 터미널에서 현재 작업 위치 Windows 탐색기로 열기

```bash
explorer.exe .
```

### wsl에서 vscode  실행하기 

vscode 확장 프로그램으로 `Remote-WSL` 설치

wsl 터미널에서 `code .` 명령어 실행하면 리눅스 배포환경에서 vscode로 작업 가능하다.

```bash
$ code .
```

참고사이트  
- WSL2 설치 및 사용 방법
  - https://www.44bits.io/ko/post/wsl2-install-and-basic-usage
- 도커 데스크톱 WSL 2 백엔드
  - https://docs.docker.com/docker-for-windows/wsl/
- WSL 1과 WSL 2 비교
  - https://docs.microsoft.com/en-us/windows/wsl/compare-versions