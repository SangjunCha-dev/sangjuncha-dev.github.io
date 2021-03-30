---
title: pyftpdlib 라이브러리 사용법 (pyftpdlib Library Guide)
author: Sangjun Cha
date: 2020-11-16 13:30:41 +0900
categories: [Python, Guide]
tags: [python, library]
pin: false
---

Docs URL - https://pyftpdlib.readthedocs.io/en/latest/

# FTP Server 라이브러리 설치

```bash
pip install pyftpdlib
```

실습 버전 : pyftpdlib 1.5.6

# 사용예제 코드

## FTP Server 예제

ftp server 실행 후 client 테스트 가능

```python
from pyftpdlib.authorizers import DummyAuthorizer  # 사용자 인증을 생성하는 모듈
from pyftpdlib.handlers import FTPHandler  # 사용자 인증, 파일 전송, 로깅 등 FTP서버를 조작하는 모듈
# from pyftpdlib.handlers import TLS_FTPHandler
from pyftpdlib.servers import FTPServer  # FTP서버를 실행하는 모듈
# from pyftpdlib.servers import ThreadedFTPServer

import os

class FileServer:
    def __init__(self):
        self.ftpServerIP = "127.0.0.1"
        self.ftpServerPort = 21

        self.userId = "ftpuser"
        self.userPassword = "password"
        self.userDir = "D:/ftp_share/"

    def start(self):
        # 계정별 디렉토리 생성
        if not (os.path.exists(self.userDir)):
            os.makedirs(self.userDir, exist_ok=True)
        # FTP Server 계정 추가
        authorizer = DummyAuthorizer()
        authorizer.add_user(self.userId, self.userPassword, self.userDir, perm="elradfmwMT")  # 모든 권한(elradfmw)을 부여
        # authorizer.add_anonymous(self.userDir, perm='elr')  # 탐색(읽기) 권한만 부여

        handler = FTPHandler
        handler.banner = "pyftpdlib based ftpd ready."  # 배너 설정

        handler.authorizer = authorizer
        handler.passive_ports = range(60000, 65535)  # 패시브통신 포트지정

        address = (self.ftpServerIP, self.ftpServerPort)  # FTP 서버주소 및 포트설정
        server = FTPServer(address, handler)
        # server = ThreadedFTPServer(address, handler)

        server.max_cons = 50  # 최대 연결 개수
        server.max_cons_per_ip = 5  # IP당 최대 연결 개수
        print(f'[FileServer] Share Dir = {self.userDir}')
        server.serve_forever()

file_server = FileServer()
file_server.start()
```


## FTP Client ftplib 예제

FTPClient.py
```python
import ftplib
import os

class FTPClient:
    def __init__(self):
        self.ftp = ftplib.FTP()
        self.ftp_server_ip = "127.0.0.1"
        self.ftp_server_port = 21

        self.user_id = "ftpuser"
        self.user_password = "password"

    def connect_setting(self):
        try:
            ftp = self.ftp
            ftp.connect(host=self.ftp_server_ip, port=self.ftp_server_port, timeout=10)
            ftp.login(user=self.user_id, passwd=self.user_password)
            ftp.encoding = 'utf-8'

            # 디버그 모드 설정 (설정하면 FTP 서버와 통신하는 내용이 콘솔에 표시됨)
            # ftp.set_debuglevel(1)

            return ftp

        except ftplib.all_errors as ex:
            print(f"[connect_setting] FTP Error = {ex}")
        except Exception as ex:
            print(f"[connect_setting] Error = {ex}")

    # Welcome Test message
    def get_msg(self):
        try:
            ftp = self.connect_setting()
            print(ftp.getwelcome())
            ftp.close()

        except ftplib.all_errors as ex:
            print(f"[get_msg] FTP Error = {ex}")
        except Exception as ex:
            print(f"[get_msg] Error = {ex}")

    # 파일 다운로드
    def download(self, server_dir_path: str, client_dir_path: str, filename: str):
        try:
            # 다운받을 디렉토리 없으면 디렉토리 생성
            os.makedirs(client_dir_path, exist_ok=True)

            ftp = self.connect_setting()
            ftp.cwd(dirname=server_dir_path)
            filePath = f"{client_dir_path}/{filename}"
            with open(filePath, 'wb') as fd:
                res = ftp.retrbinary("RETR " + filename, fd.write)

                if not res.startswith('226 Transfer complete'):  # 다운로드 실패시 다운받은 파일 삭제
                    print(f"[download] FTP Failed FileName = {fd}")
                    if os.path.isfile(filename):
                        os.remove(filename)

        except ftplib.all_errors as ex:
            print(f"[download] FTP Error = {ex}")
        except Exception as ex:
            print(f"[download] Error = {ex}")

    # 파일 업로드
    def upload(self, server_dir_path: str, client_dir_path: str, filename: str):
        try:
            ftp = self.connect_setting()
            ftp.cwd(dirname=server_dir_path)

            with open(f"{client_dir_path}/{filename}", 'rb') as fd:
                res = ftp.storbinary('STOR ' + filename, fd)
                if not res.startswith('226 Transfer complete'):
                    print(f"[upload] FTP Failed FileName = {fd}")
            print(f"\x1b[1;36m [upload] FTP Success FileName = {filename}")

        except ftplib.error_perm as ex:
            print(f"[upload] FTP Error_perm = {ex}")
            # FTP Server 해당 디렉토리가 없을시 디렉토리 생성후 다시 업로드
            if str(ex) == '550 No such file or directory.':
                self.create_dir(server_dir_path)
                self.upload(server_dir_path, client_dir_path, filename)
        except ftplib.all_errors as ex:
            print(f"[upload] FTP Error = {ex}")
        except Exception as ex:
            print(f"[upload] Error = {ex}")

    # 파일 삭제
    def delete_file(self, server_dir_path: str, filename: str):
        try:
            ftp = self.connect_setting()
            ftp.cwd(dirname=server_dir_path)
            ftp.delete(filename)
            ftp.close()

        except ftplib.all_errors as ex:
            print(f"[delete_file] FTP Error = {ex}")
        except Exception as ex:
            print(f"[delete_file] Error = {ex}")

     # 디렉터리 삭제
    def delete_dir(self, server_dir_path: str, dir_name: str):
        try:
            ftp = self.connect_setting()
            ftp.cwd(dirname=server_dir_path)
            ftp.rmd(dir_name)
            ftp.close()

        except ftplib.all_errors as ex:
            print(f"[delete_dir] FTP Error = {ex}")   
        except Exception as ex:
            print(f"[delete_dir] Error = {ex}")

    # 디렉토리 목록
    def dir_list(self, dir_name='/'):
        try:
            ftp = self.connect_setting()
            ftp.cwd(dir_name)
            files = []
            ftp.dir(files.append)  # LIST 명령으로 반환되는 디렉토리 목록 생성
            ftp.close()
            return files

        except ftplib.all_errors as ex:
            print(f"[dir_list] FTP Error = {ex}")
        except Exception as ex:
            print(f"[dir_list] Error = {ex}")

    # 디렉토리 생성
    def create_dir(self, dir_name: str):
        try:
            # FTP 서버 디렉토리 목록 얻어오기
            files_list = self.dir_list(f"{dir_name}/../")
            d_list = []
            for file in files_list:
                if file[0] == 'd':
                    d_list.append(file.split(' ')[-1])
            print(f"\x1b[1;36m [create_dir] d_list = {d_list}")

            if not (dir_name in d_list):
                # 디렉토리 생성
                ftp = self.connect_setting()
                ftp.mkd(dir_name)  # 새 디렉토리 생성
                ftp.close()
                print(f"\x1b[1;36m [create_dir] Dir Name = {dir_name}")

        except ftplib.all_errors as ex:
            print(f"[create_dir] FTP Error = {ex}")
        except Exception as ex:
            print(f"[create_dir] Error = {ex}")

    # # FTP 명령어 전송
    # def send_cmd(self, cmd: str):
    #     try:
    #         ftp = self.connect_setting()
    #         work_dir = ftp.sendcmd('PWD')  # PWD 명령을 직접 전송 하고 pwd()메소드를 사용하여 현재 작업 디렉토리를 검색
    #         print(ftplib.parse257(work_dir))  # 상태 코드가 들어있는 반환 된 문자열에서 디렉토리를 검색
    #
    #         work_dir2 = ftp.pwd()  # 현재 작업 디렉토리를 검색
    #         print(work_dir2)
    #     except ftplib.all_errors as ex:
    #         print(f"[send_cmd] FTP Error = {ex}")


```

## FTP Client 예제

client.py
```python
from FTPClient import FTPClient 

ftp_client = FTPClient()
client_path = "D:/ftp_share/ftpclient"

# FTP 다운로드 
# server_dir_path 경로 : D:/ftp_share/ftpserver/temp/
# client_dir_path 경로 : D:/ftp_share/ftpclient/temp/
ftp_client.download(server_dir_path='/ftpserver/temp/', client_dir_path=f'{client_path}/temp/', filename='test.txt')

# FTP Server 테스트 파일 삭제
ftp_client.deleteFile(server_dir_path='/ftpserver/temp/', filename='test.txt')

# FTP Server temp 디렉토리 삭제
ftp_client.deleteDir(server_dir_path='/ftpserver/', dir_name='temp')

# FTP 업로드
self.ftpClient.upload(server_dir_path="/ftpserver/temp2/", client_dir_path=f'{client_path}/temp/', filename='test.txt')
```