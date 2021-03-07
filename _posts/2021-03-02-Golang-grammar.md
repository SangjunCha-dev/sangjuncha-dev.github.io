---
title: Go 언어 문법 (Golang grammar)
author: Sangjun Cha
date: 2021-02-10 11:13:23 +0900
categories: [Go]
tags: [go]
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
    "value":"bar"
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
// origin :  2021-03-03 15:26:37.123456789 +0900 KST

fmt.Println("mm-dd-yyyy :", nowTime.Format("01-02-2006"))
// mm-dd-yyyy :  03-03-2021

fmt.Println("yyyy-mm-dd :", nowTime.Format("2006-01-02"))
// yyyy-mm-dd :  2021-03-03

// separated by .
fmt.Println("yyyy.mm.dd :", nowTime.Format("2006.01.02"))
// yyyy.mm.dd :  2021.03.03

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
    fmt.Println( i , f.Name())
}
```

```bash
0 test1.txt
1 test2.txt
```
