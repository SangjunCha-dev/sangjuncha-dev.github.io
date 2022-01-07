---
title: "golang Socket, TimeRotateFile logging"
date: 2022-01-07T13:24:20+09:00
description: golang Socket, TimeRotateFile logging
tags: ["go", "logging"]
categories: ["Go", "logging"]
---


---


## 1. 개요

golang 기본 로깅에서는 지원하지 않는 TimeRotate 로깅은 별도의 외부 라이브러리를 사용한다.

```bash
go get github.com/lestrrat-go/file-rotatelogs
```

이후에 소켓 통신을 같이 사용한 logging 프로그램까지 구현한다.


---

## 2. TimeRotateFile logging

해당 file-rotatelogs 라이브러리는 단일 파일 작성만 지원한다. 멀티 파일 작성은 다른 라이브러리를 사용해야 한다.

### 2.1. OPTIONS

1. **Patterm**
- 로그저장 경로 및 파일이름 패턴지정(required)
- 예시: `rotatelogs.New("log/%Y-%m-%d.log")`
2. **Clock** (default: rotatelogs.Local)
- 시간대 시스템 로컬 시간으로 설정
- 예시: `rotatelogs.WithClock(rotatelogs.Local)`
3. **RotationTime** (default: 86400 sec)
- 로테이션 반복 주기
- 지정한 시간간격으로 파일 로테이션
- 동일파일 존재시 로그 추가작성으로 동작
- time.Duration 타입 사용
- 예시: `rotatelogs.WithRotationTime(time.Hour)`
4. **MaxAge** (default: 7 days)
- 지정한 기간을 지난 파일 삭제 
- -1 : 옵션 비활성화
- 예시: `rotatelogs.WithMaxAge(-1)`
5. **RotationCount** (default: -1)
- 유지되는 로그파일 개수
- 지정한 개수보다 많아지면 오래된 로그파일 삭제
- -1 : 옵션 비활성화
- 예시: `rotatelogs.WithRotationCount(90)`


### 2.2. 예시코드

`SetFlags` 옵션은 golang 기본 log 출력형식 옵션이다.  
- log.Ldate: 날짜 지정 (2022/01/07)
- log.Ltime: 시간 지정 (16:16:45)
- log.Lmicroseconds: 마이크로초 지정 (573193)

`SetFlags(0)` 옵션은 기본 출력 형식 없이 로그메시지만 출력할 때 사용한다.
- 외부로부터 로그를 전송받아 작성하는 경우 시간 값도 전송받기 때문에 메시지만 작성한다.

```go
import (
	"log"
	"time"

	rotatelogs "github.com/lestrrat-go/file-rotatelogs"
)

// 로그 설정
func setLog() {
	rl, _ := rotatelogs.New(
		"log/%Y-%m-%d.log",
		rotatelogs.WithMaxAge(-1),
		rotatelogs.WithRotationTime(time.Hour),
		rotatelogs.WithClock(rotatelogs.Local),
		rotatelogs.WithRotationCount(90),
	)
	log.SetFlags(log.Ldate | log.Ltime | log.Lmicroseconds)
	log.SetOutput(rl)
}

func main() {
	setLog()

    log.Println("로그 테스트")
}
```

로그 출력 예시

```
2022/01/07 16:16:45.573193 로그 테스트
```


---

## 3. socket logging

### 3.1. Server

내장 라이브러리만 사용한 TCP소켓서버로 byte array 형태로 수신받는다.

log 설정은 TimeRotateFile logging 예시를 사용한다.

수신받은 데이터를 문자열 형태로 저장하는 예시이다.

```go
func loggingServer() {
	listen, err := net.Listen("tcp", ":8010")
	if err != nil {
		log.Println(err)
	}
	defer listen.Close()

	log.Println("Logging Server Start Port :", 8010)

	for {
		conn, err := listen.Accept()
		if err != nil {
			log.Println(err)
			continue
		}
		defer conn.Close()
		go ConnHandler(conn)
	}
}

func ConnHandler(conn net.Conn) {
	recvBuffer := make([]byte, 4096)
	for {
		n, err := conn.Read(recvBuffer)
		if err != nil {
			if err != io.EOF {
				log.Println(err)
			}
			return
		}

		if 0 < n {
			data := recvBuffer[:n]
			log.Println(string(data))
		}
	}
}

func main() {
	setLog()
	loggingServer()
}
```

### 3.2. Client

1초마다 메시지를 전송하는 예시용 프로그램이다.

```go
import (
	"fmt"
	"net"
	"strconv"
	"time"
)

func main() {
	conn, err := net.Dial("tcp", ":8010")
	if nil != err {
		fmt.Println(err)
	}

	cnt := 1
	for {
		msg := "hello world " + strconv.Itoa(cnt)
		conn.Write([]byte(msg))
		fmt.Println(msg)
		time.Sleep(time.Duration(1) * time.Second)
		cnt += 1
	}
}
```

log 출력

```
2022/01/07 17:24:27.901790 Logging Server Start Port : 8010
2022/01/07 17:24:39.485043 hello world 1
2022/01/07 17:24:40.487938 hello world 2
```

### 3.3. Json Log Format

json형태 로그를 수신받을 경우 구조체로 변환하여 특정 형식으로 작성하도록 활용할 수 있다.

```go
...
type LogData struct {
	ActionTime string
	Level      string
	Message    string
}

...
func setLog() {
	...
	log.SetFlags(0)
	...
}

...

data := recvBuf[:n]
dataStr := strings.ReplaceAll(string(data), "}{", "}\n{")
objects := strings.Split(dataStr, "\n")
for _, object := range objects {
	logData := LogData{}
	json.Unmarshal([]byte(object), &logData)
	log.Printf("%s [%8s] %s\n", logData.ActionTime, logData.Level, logData.Message)
}
...
```

client → Server 전송데이터 예시

```json
{
	"ActionTime": "2022-01-07 17:58:23,999",
	"Level": "INFO",
	"Message": "info 테스트"
}
```

log 출력

```
2022-01-07 17:58:23,999 [INFO    ] info 테스트
2022-01-07 17:58:24,000 [WARNING ] warning 테스트
2022-01-07 17:58:24,000 [ERROR   ] error 테스트
```


---

## 4. 예시코드 Git

- [golang-socket-logging](https://github.com/SangjunCha-dev/blog/tree/main/golang/golang-socket-logging)

---

## 참고(Reference)

- [file-rotatelogs docs](https://github.com/lestrrat-go/file-rotatelogs)
- [Go socket](https://zetcode.com/golang/socket/)