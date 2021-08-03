---
title: Go 언어 문법 (Golang grammar)
author: Sangjun Cha
date: 2021-02-10 11:13:23 +0900
categories: [Go, Learning]
tags: [go, learning]
pin: false
---

# 디렉토리 관련 함수
2021년 3월 3일 수요일 오전 8:55:31

## 단일 디렉토리 생성

`os.Mkdir(path, permission)`

```go
err := os.Mkdir("tmp", 0755)
if err != nil {
    log.Fatal(err)
}
```

## 다중 디렉토리 생성

`os.MkdirAll(path, permission)` : 

```go
err := os.MkdirAll("tmp/new", 0755)
if err != nil {
    log.Fatal(err)
}
```

## 현재 작업 디렉토리 얻기

`os.Getwd()`

```go
path, err := os.Getwd()
if err != nil { 
    log.Println(err) 
} 
fmt.Println(path)
```

## 디렉토리 존재유무 확인

`os.Stat(paht)`

```go
if _, err := os.Stat("/dirname"); os.IsNotExist(err) {
	// does not exist
}

if _, err := os.Stat("/dirname"); !os.IsNotExist(err) {
	// exist
}
```

## 디렉토리 이름 바꾸기

`os.Rename(oldpath, newpath)`

```go
err := os.Rename("tmp", "tmp2") 
if err != nil { 
    log.Fatal(err) 
}
```



# struct 구조체 기본값 지정
2021년 3월 3일 수요일 오후 5:43:23

```json
{
    "index":0,
    "value":"bar",
    "result":false
}
```

```go
package main

import (
    "encoding/json"
    "fmt"
)

type Config struct {
    Index   int	    `json:"index"`
    Value string    `json:"value"`
    Result bool     `json:"result"`
}

var data = Config{}

func main() {
    con, err := ioutil.ReadFile("config.json")
	if err != nil {
		log.Print(err)
	}
	if err := json.Unmarshal(con, &data); err != nil {
		log.Print(err)
	}
    fmt.Println(data)
}
```



# Time 변수 문자열 변환 (convert time.Time to string)
2021년 3월 4일 목요일 오후 2:54:09

```go
// caution : format string is `2006-01-02 15:04:05.000000000`
nowTime := time.Now()

fmt.Println("origin :", nowTime.String())
// origin : 2021-03-03 15:26:37.123456789 +0900 KST

fmt.Println("mm-dd-yyyy :", nowTime.Format("01-02-2006"))
// mm-dd-yyyy : 03-03-2021

fmt.Println("yyyy-mm-dd :", nowTime.Format("2006-01-02"))
// yyyy-mm-dd : 2021-03-03

// separated by .
fmt.Println("yyyy.mm.dd :", nowTime.Format("2006.01.02"))
// yyyy.mm.dd : 2021.03.03

fmt.Println("yyyy-mm-dd HH:mm:ss :", nowTime.Format("2006-01-02 15:04:05"))
// yyyy-mm-dd HH:mm:ss : 2021-03-03 15:26:37

// Stamp Micro second
fmt.Println("yyyy-mm-dd HH:mm:ss :", nowTime.Format("2006-01-02 15:04:05.000000"))
// yyyy-mm-dd HH:mm:ss : 2021-03-03 15:26:37.123456

// Stamp Nano second
fmt.Println("yyyy-mm-dd HH:mm:ss :", nowTime.Format("2006-01-02 15:04:05.000000000"))
// yyyy-mm-dd HH:mm:ss : 2021-03-03 15:26:37.123456789
```



# 디렉토리 내 파일목록 읽기
2021년 3월 4일 목요일 오후 5:45:44

`ioutil.ReadDir(path)`

```go
files, err := ioutil.ReadDir("tmp") 
if err != nil{ 
    fmt.Println(err) 
} 
for i ,f := range files {
    fmt.Println(i, f.Name())
}
```

```bash
0 test1.txt
1 test2.txt
```



# 문자열 분할
2021년 3월 8일 월요일 오전 1:21:36

Split (s, sep string ) [] string

```go
tmp := "one_two_three"
slice := strings.Split(tmp, "_")

for i, str := range slice {
    fmt.Println(i, str)
}
```

```bash
0 one
1 two
2 three
```

SplitAfter(s, sep string) []string

```go
tmp := "one_two_three"
slice := strings.SplitAfter(tmp, "_")

for i, str := range slice {
    fmt.Println(i, str)
}
```

```bash
0 one_
1 two_
2 three
```

아래의 함수는 split 횟수를 n번으로 제한하는 함수

SplitN(s, sep string, n int) []string  
SplitAfterN(s, sep string, n int) []string  



# 타입 확인
2021년 3월 8일 월요일 오전 9:26:56  

reflect.TypeOf(i interface{}) reflect.Type

```go
x, y, z := 1, "2", 3.14

fmt.Println("x type :", reflect.TypeOf(x))
fmt.Println("y type :", reflect.TypeOf(y))
fmt.Println("z type :", reflect.TypeOf(z))
```

```bash
x type : int
y type : string
z type : float64
```

# 타입 변경

strconv

## 숫자 변환

Atoi : 문자열에서 int로  
Itoa : int에서 문자열로  

```go
i, err := strconv.Atoi("-42")
s := strconv.Itoa(-42)
```

문자열을 값으로 변환

```go
b, err := strconv.ParseBool("true")
f, err := strconv.ParseFloat("3.1415", 64)
i, err := strconv.ParseInt("-42", 10, 64)
u, err := strconv.ParseUint("42", 10, 64)
```

문자열을 가장 넓은 유형 (float64, int64 및 uint64)을 반환  
- size 인수가 더 좁은 너비를 지정하면 결과를 데이터 손실없이 더 좁은 유형으로 변환

```go
s := "2147483647" // biggest int32
i64, err := strconv.ParseInt(s, 10, 32)
...
i := int32(i64)
```

값을 문자열로 변환

```go
s := strconv.FormatBool(true)
s := strconv.FormatFloat(3.1415, 'E', -1, 64)
s := strconv.FormatInt(-42, 16)
s := strconv.FormatUint(42, 16)
```



## 문자열 변환
2021년 3월 8일 월요일 오전 10:14:38 

문자열을 인용 된 Go 문자열 리터럴로 변환

```go
q := strconv.Quote("Hello, World")
q := strconv.QuoteToASCII("Hello, World")
```



# 에러 핸들링
2021년 3월 8일 월요일 오전 10:52:04  

## 복합한 에러 핸들링

빈 디렉토리 tmp 생성후 해당 코드 실행시  
`*fs.PathError` 에러가 발생하는데 아래의 switch case `err.(*fs.PathError)` 구문으로 별도의 에러 핸들링이 가능해진다.

```go
dirPath := "tmp"
files, err := ioutil.ReadDir(dirPath)
switch err {
case nil:
    break
case err.(*fs.PathError):
    fmt.Println("[findKey] 현재 디렉토리에 파일이 없습니다.")
default:
    fmt.Println("[findKey] Error :", err)
}
```



# 특정 날짜/시간 동작
2021년 3월 8일 월요일 오후 1:29:32

```bash
go get github.com/robfig/cron
```

```go
c := cron.New()
c.AddFunc("@midnight", func() {
    findKey()
})
c.Start()
```

```go
customLocation, _ := time.LoadLocation("Asia/Seoul")
c := cron.NewWithLocation(customLocation)
c.AddFunc("@midnight", func() {
    fmt.Print("time :", time.Now())
})
c.Start()
```



# 로그파일 작성
2021년 3월 8일 월요일 오후 1:29:32

```go
rl, _ := rotatelogs.New(
"D:/tmp/Logfile.%Y-%m-%d.log",
rotatelogs.WithMaxAge(-1),              // 정해진 시간보다 지난 파일 삭제 (-1은 비활성화)
rotatelogs.WithRotationTime(time.Hour), // 로테이션 반복 주기
rotatelogs.WithClock(rotatelogs.Local), // 로컬 시간으로 설정
rotatelogs.WithRotationCount(30),       // 유지되는 파일 개수
)
log.SetOutput(rl)

log.Println("log write")
```



# 프로젝트내 다른 로컬패키지 호출
2021년 3월 11일 목요일 오후 6:02:36

go언어는 패키지를 GOPATH 기준으로 검색하기때문에  
GOPATH가 아닌 다른경로에서 프로젝트를 개발할 경우  
프로젝트이름 기준으로 패키지 검색

디렉토리 구조 예시

```
project1
├── package1/
|   └── package1.go
└── package2/
    └── package2.go
```

## 로컬패키지 호출 예시

package1.go파일에서 로컬패키지 package2 호출

./package1.go

```go
import (
    "project1/package2"
)
```

## 잘못 사용한 예시

go언어에서는 import 구문으로 상대경로를 사용하지 않음

```go
import (
    "../package2"
)
```


# uint16형 데이터 2Byte형태로 변환

```go
b := make([]byte, 2)
binary.BigEndian.PutUint16(b, uint16(258))

fmt.Println(string(b))
```

# 서로 다른 byte 데이터 비교

```go
b := make([]byte, 2)
binary.BigEndian.PutUint16(b, uint16(258))

if bytes.Compare(b, []byte{1, 2}) == 0 {
    fmt.Println("같은 데이터 입니다.")
}
```