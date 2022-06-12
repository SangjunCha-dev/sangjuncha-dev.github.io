---
title: "Docker failed to copy files 에러 해결"
date: 2022-06-12T16:36:38+09:00
description: docker failed to copy files error
tags: ["docker"]
categories: ["Docker"]
---


---

## 1. 문제 증상

- Docker 빌드를 수정하면서 자주 하게 되면 아래와 같은 에러가 발생한다.
    ```bash
    failed to copy files: copy file range failed: no space left on device
    ```


---

## 2. 에러 원인

- `Windows Docker`에서 사용하는 `WSL2 가상디스크` 용량이 자동으로 확장되다가 더 이상 확장할 수 없을때 발생하는 에러이다.
- 파일경로
    ```
    C:\Users\[username]\AppData\Local\Docker\wsl\data\ext4.vhdx
    ```
- 하지만 더 이상 사용하지 않은 데이터가 있더라도 가상디스크 용량은 자동으로 축소되지 않는다.


---

## 3. 해결

- Windows 관리자 권한으로 `diskpart`도구를 사용하면 해결할 수 있다. 
    ```
    > diskpart

    Microsoft DiskPart 버전 10.0.19041.964
    Copyright (C) Microsoft Corporation.

    DISKPART> select vdisk file="[ext4.vhdx 절대경로]"

    DiskPart가 가상 디스크 파일을 선택했습니다.

    DISKPART> compact vdisk
    ```
- `ext4.vhdx` 가상디스크를 사용하지 않은 상태에서만 용량 압축이 가능하다.
    - wsl2를 사용하는 모든 프로세스 종료(Docker 등등)
