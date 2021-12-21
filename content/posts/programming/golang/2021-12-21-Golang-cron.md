---
title: "golang cron 스케줄링 라이브러리 사용법"
date: 2021-12-21T09:59:03+09:00
description: golang cron 라이브러리 사용법
tags: ["go", "cron"]
categories: ["Go", "cron"]
---



go언어에서 cron 처럼 동작하는 스케줄링 외부 라이브러리 사용법

- cron 함수는 지정한 주기마다 동작시키는 기능으로 별도의 비동기함수로 실행된다.
- main 함수가 종료되면 go 프로세스가 종료되어 비동기 함수도 종료된다.
- cron 기능을 사용할려면 main 함수가 종료되지 않도록 적절한 조치를 해야한다.

## 라이브러리 설치

go version : 1.17

```
> go get github.com/robfig/cron/v3@v3.0.0
```

## 예제코드

- `select` : 복수 채널이 대기하면서 준비된 (데이터를 전송받은) 채널을 실행하는 기능이다. case 채널들이 준비되지 않으면 계속 대기하게 되고, 가장 먼저 도착한 채널의 case를 실행한다.
- `select {}` : 채널을 지정하지 않아 main 함수 종료 방지용으로 사용한다.

기본 예제

```go
package main

import (
	"fmt"

	"github.com/robfig/cron/v3"
)

func main() {
	c := cron.New()
	c.AddFunc("* * * * *", func() {
		fmt.Println("cron 실행")
	})
	c.Start()

	// main 함수 종료 방지
	select {}
}
```

## AddFunc 다양한 사용법

AddFunc에서 지정한 스케줄링은 기본 linux 시스템의 cron문법과 유사하다.

```go
c.AddFunc("* * * * *",    func() { fmt.Println("Every hour on the half hour") })  // 매분마다
c.AddFunc("30 * * * *",   func() { fmt.Println("Every hour on the half hour") })  // 매시간 30분마다
c.AddFunc("@hourly",      func() { fmt.Println("Every hour") }) // 매시간마다
c.AddFunc("@daily",       func() { fmt.Println("Every day") })  // 매일마다
c.AddFunc("@every 1h30m", func() { fmt.Println("Every hour thirty") }) // 1시간 30분 경과할때마다
```

## Docs URL
 - [cron package](https://pkg.go.dev/github.com/robfig/cron)
