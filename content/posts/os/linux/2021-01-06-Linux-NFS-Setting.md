---
title: "리눅스 NFS 설정 (Linux NFS Setting)"
date: 2021-01-06T13:36:31+09:00
description: 리눅스 NFS 설정 (Linux NFS Setting)
menu:
  sidebar:
    name: Linux NFS Setting
    identifier: linux-nfs-setting
    parent: linux
    weight: 30
tags: ["linux", "nfs"]
categories: ["Linux", "NFS"]
---



설치환경 : 레드햇8 (RHEL8)


# 1. NFS 서버 설정 (Server Setting)

## 1.1 NFS 패키지 설치

`yum`패키지 설치 도구로 `nfs-utils` 패키지 설치

```bash
yum install nfs-utils
```

## 1.2 NFS서버 서비스 실행 및 OS재부팅시 활성화

nfs서버 데몬 실행하고, OS 재부팅시 자동실행되도록 활성화

```bash
systemctl start nfs-server
systemctl enable nfs-server
```

## 1.3 외부에서 접근할 디렉토리 설정

공유할 디렉토리 생성

```bash
mkdir /NFSVOL01
```

외부에서 접근할 디렉토리, 외부 접근IP, 접근권한을 exports 파일로 설정

```bash
vi /etc/exports

/NFSVOL01 192.168.0.0/255.255.255.0(rw)
```
> /NFSVOL01 : 공유할 디렉토리  
> 192.168.0.0/255.255.255.0 : 접속허용 IP 대역  
> rw : 디렉토리 권한  

디렉토리 권한 옵션

- ro : 읽기 (default)
- rw : 읽기 쓰기 
- root_squash : 클라이언트 root 권한 접속불가 (default), 루트권한요청(uid/gid -> 0)을 익명계정으로 맵핑
- no_root_squash : 클라이언트 root 권한 접속가능
- all_squash : 모든 접속권한(uid/gid)을 익명계정으로 맵핑 (default)
- no_all_squash : no_root_squash 옵션과 동일
- sync : 변경 사항이 커밋된 후에만 요청에 응답하여 안정적인 저장가능 (default)
- async : 요청에 의해 변경되기 전에 요청에 응답, 일반적으로 성능은 향상되지만 비용이 많이 소요.  
부정한 서버 재시작(충돌 등)으로 인해 데이터가 손상 될 수 있음 

## 1.4 exports 파일적용

exports 작성한 내용 적용

```bash
exportfs -ra
```

## 1.5 NFS 서비스 방화벽 허용

방화벽이 실행중인 경우 nfs서비스를 방화벽에 허용 정책등록

```bash
firewall-cmd --permanent --add-service=nfs
firewall-cmd --reload
firewall-cmd --list-all
```
> --permanent : 영구등록  
> --add-service=nfs : 허용할 서비스  
> --reload : 방화벽 재시작  
> --list-all : 등록된 방화벽 정책목록 보기  

## 1.6 NFS 서비스 동작확인

```bash
showmount -e
exportfs -s
```
> showmount -e : export된 디렉토리의 목록보기  
> exportfs -s : 현재 NFS 설정 확인  

## 1.7 클라이언트 권한설정

클라이언트에서 파일업로드등 쓰기 권한이 필요한 경우 설정

```bash
chmod 777 /NFSVOL01
```



# 2. NFS 클라이언트 설정 (Clinet Setting)

## 2.1 NFS 패키지 설치

`yum`패키지 설치 도구로 `nfs-utils` 패키지 설치

```bash
yum install nfs-utils
```

## 2.2 mount 서버 확인

NFS 서버 동작 확인
```bash
showmount -e 192.168.0.100
```

> 192.168.0.100 : nfs 서버 주소

## 2.3 mount 등록할 디렉토리 생성

NFS 클라이언트에서 서버로 마운트 등록할 디렉토리 생성

```bash
mkdir /NFSVOL01
```

## 2.4 디렉토리 mount

생성된 디렉토리에 NFS디렉토리 마운트

```bash
mount -t nfs 192.168.0.100:/NFSVOL01 /NFSVOL01
```

## 2.5 mount 확인

```bash
df -h | grep NFSVOL01
```

## 2.6 OS 부팅시 자동 mount

mount 영구설정(/etc/fstab) 파일에 등록

```bash
vi /etc/fstab

192.168.0.100:/NFSVOL01 /NFSVOL01   nfs hard    0   0
```

- 해설

    > 192.168.0.100:/NFSVOL01 : NFS 서버주소 및 서버 디렉토리  
    > /NFSVOL01 : Mount Point로 클라이언트 디렉토리 지정  
    > nfs : 파일시스템 종류로 NFS사용시 nfs로 지정  
    > hard : 타임아웃이 발생되면 server not responding을 출력하고 무한정 재시도  
    > soft : 타임아웃이 발생되면 프로그램에게 I/O 에러 보고  
    > 0 : 덤프 설정(0-덤프불가 / 1-덤프가능)  
    > 0 : 무결성 검사설정(0-검사하지않음 / 1-우선순위1로 대부분 루트설정 / 2-우선순위2)  
