---
title: django socketio 라이브러리 활용한 통신
author: Sangjun Cha
date: 2021-06-11 10:59:51 +0900
categories: [Django]
tags: [django]
pin: false
---

# 라이브러리 설치

## server 라이브러리 설치
```shell
pip install python-socketio
```

## client 라이브러리 설치
```shell
pip install "python-socketio[client]"
```

# Server django settings

django server  
wsgi.py  
```python
...

import socketio
from appname.views import sio  # sio code path
...
# application = get_wsgi_application()

sio.register_namespace(TransferNamespace('/transfer'))
django_app = get_wsgi_application()
application = socketio.WSGIApp(sio, django_app)
```

appname.views.py  
```python
import socketio

sio = socketio.Server(async_mode='threading', async_handlers=True, ping_interval=60)

class TransferNamespace(socketio.Namespace):
    def on_connect(self, sid: str, environ: dict):
        ''' 클라이언트 접속 '''
        print(f"connect address = {environ['REMOTE_ADDR']}, sid = {sid}")

    def on_disconnect(self, sid: str):
        ''' 클라이언트 접속 종료 '''
        print(f"disconnect Client disconnected = {sid}")

    def send_event(self, sid: str):
        ''' 특정 sid 클라이언트에 이벤트 전송 '''
        print("send_event")
        self.emit('send_event', data, room=sid, namespace='/transfer')

    def on_client_response(self, sid):
        '''
        클라이언트에서 전송한 이벤트
        '''
        print(f"on_client_response sid = {sid}")
```

# Client settings

client.py
```python
import socketio

sio = socketio.Client()

@sio.event
def connect():
    print("server connect")

# 서버로부터 이벤트 데이터 수신
@sio.on('send_event')
def on_send_event():
    print("receive send_eventdata")
 
    client_response()

# 서버로 데이터 송신
@sio.event
def client_response():
    print("client_response")
    sio.emit('client_response')

@sio.event
def disconnect():
    print("disconnect From Server")

def socketio_run():
    # sio.connect('http://host:port', namespaces=['/namespace'])
    sio.connect(url='http://127.0.0.1:8080', transports='polling', namespaces='/transfer')
    print("socketio server start")

    sio.wait()
```