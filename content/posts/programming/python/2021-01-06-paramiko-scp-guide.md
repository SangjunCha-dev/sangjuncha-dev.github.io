---
title: "paramiko-scp 라이브러리 사용법 (paramiko-scp Library Guide)"
date: 2021-01-06T17:36:17+09:00
description: paramiko-scp 라이브러리 사용법 (paramiko-scp Library Guide)
menu:
  sidebar:
    name: paramiko-scp Library Guide
    identifier: paramiko-scp-library-guide
    parent: python
    weight: 30
tags: ["python", "library"]
categories: ["Python", "Guide"]
---



paramiko, scp 라이브러리 연계 사용한 ssh 파일 전송

|라이브러리|암호화 여부|속도|  
|---|---|---|
|paramiko|로그인:암호화 / 파일전송:암호화|느림|
|paramiko+scp|로그인:암호화 / 파일전송:평문|보통|

<br>

실습환경 : windows10, 
원격서버환경 : linux(RHEL8)

사전에 ssh 접속가능한 linux를 구축한 후 실습 진행

# 라이브러리 설치

```bash
pip install paramiko
pip install scp
```

paramiko : SSH 접속 및 종료 기능
- Docs URL - http://docs.paramiko.org/en/stable/

scp : 파일 전송 및 다운로드 기능
- Docs URL - https://pypi.org/project/scp/

# 사용예제 코드

## SSHManager

SSHManager.py

```python
import paramiko
from scp import SCPClient, SCPException

class SSHManager:
    def __init__(self):
        self.ssh_client = None

    def create_ssh_client(self, hostname, port, username, password):
        try:
            if self.ssh_client is None:
                self.ssh_client = paramiko.SSHClient()
                self.ssh_client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
                self.ssh_client.connect(hostname, port, username=username, password=password)
                print(f"[create_ssh_client] host = {hostname}:{port}/{username}")
            else:
                print(f"[create_ssh_client] SSH client session exist. {hostname}")

        except Exception as ex:
            print(f"[create_ssh_client] Error : {ex}")

    def close_ssh_client(self):
        try:
            self.ssh_client.close()
            print(f"[close_ssh_client] Disconnect")
        except Exception as ex:
            print(f"[close_ssh_client] Error : {ex}")

    def send_file(self, local_path, remote_path):
        try:
            with SCPClient(self.ssh_client.get_transport()) as scp:
                scp.put(local_path, remote_path, preserve_times=True)
                print(f"[send_file] local_path = {local_path} | remote_path = {remote_path}")
        except SCPException:
            print(f"[send_file] SCP Error : {SCPException.message}")
            raise SCPException.message
        except Exception as ex:
            print(f"[send_file] Error : {ex}")

    def get_file(self, remote_path, local_path):
        try:
            with SCPClient(self.ssh_client.get_transport()) as scp:
                scp.get(remote_path, local_path)
        except SCPException:
            print(f"[send_file] SCP Error : {SCPException.message}")
            raise SCPException.message
        except Exception as ex:
            print(f"[get_file] Error : {ex}")

    def send_command(self, cmd):
        try:
            print(f"[send_command] cmd = {cmd}")
            stdin, stdout, stderr = self.ssh_client.exec_command(cmd)
            return stdout.readlines()
        except Exception as ex:
            print(f"[send_command] Error : {ex} | cmd = {cmd}")
```

## client

client.py

```python
from SSHManager import SSHManager

class Client:
    def run(self):
        self.create_dir("server_dir")

        # 원격서버 SSH 접속
        self.client = SSHManager()
        self.client.create_ssh_client("hostname", "port", "username", "password")

        # 파일 업로드
        self.client.send_file('local_path', 'remote_path')
        # 파일 다운로드
        self.client.get_file('remote_path', 'local_path')

        # 압축파일 해제
        self.decompress('server_dir', 'filename')
        # 압축파일 삭제
        self.remove_file('server_dir', 'filename')

        # 원격서버 SSH 접속종료
        self.client.close_ssh_client()

    def create_dir(self, server_dir):
        try:
            cmd = f'mkdir -m 775 -p {server_dir}'
            self.client.send_command(cmd)
        except Exception as ex:
            print(f"[create_dir] Error = {ex}")

    def decompress(self, server_dir, filename):
        try:
            cmd = f"unzip {server_dir}/{filename} -d {server_dir}/{filename.split('.')[0]}/"
            result = self.client.send_command(cmd)
            if result:
                print(f"[decompress] 압축해제 성공 | filename = {filename}")
            else:
                print(f"[decompress] 압축해제 실패 | filename = {filename}")
        except Exception as ex:
            print(f"[decompress] Error = {ex} | filename = {filename}")

    def remove_file(self, server_dir, filename):
        try:
            cmd = f'rm -f {server_dir}/{filename}'
            self.client.send_command(cmd)
        except Exception as ex:
            print(f"[remove_file] Error = {ex} | filename = {filename}")

client = Client()
client.run()
```

참고 URL - [SSH & SCP in Python with Paramiko](https://hackersandslackers.com/automate-ssh-scp-python-paramiko/)
