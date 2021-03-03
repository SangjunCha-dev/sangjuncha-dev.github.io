---
title: Go 언어 문법 (Golang grammar)
author: Sangjun Cha
date: 2021-02-10 11:13:23 +0900
categories: [Go]
tags: [go]
pin: false
---

# 디렉토리 생성

os.Mkdir()

```go
func Mkdir(name string, perm FileMode) error
```
- name : 생성할 디렉토리 이름
- perm : 디렉토리 권한 설정

사용예제

```go
err := os.Mkdir("/temp", 0755)
if err != nil {
    log.Fatal(err)
}
```

# 디렉토리 존재유무 확인

os.Stat()

```go
if _, err := os.Stat("/dirname"); os.IsNotExist(err) {
	// does not exist
}

if _, err := os.Stat("/dirname"); !os.IsNotExist(err) {
	// exist
}
```

## struct 구조체 기본값 지정

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
