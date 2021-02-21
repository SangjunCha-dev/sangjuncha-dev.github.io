---
title: 노마드코더 쉽고 빠른 Go 시작하기(nomadcoders Go-for-beginners lectures)
author: Sangjun Cha
date: 2021-02-22 00:09:23 +0900
categories: [Go, Lecture, Scraper]
tags: [Go, note, scraper]
pin: false
---

# 0. 소개

설치 환경 : Windows 10
IDE : vscode

## 1. 설치

해당 URL에서 golang 설치파일을 다운받아 실행  

- https://golang.org/dl/
- 설치파일에서 안내한 경로인 `C:\Program Files\Go` 폴더에 Go 설치 되었는지 확인

## 2. 설정

1. Go Path 환경변수 확인

- Go는 지정된 Go Path 디렉토리에만 저장되어야함
- 시스템 환경변수에 등록된 `GOPATH` 값 확인
- `%USERPROFILE%\go` -> `C:\Users\[Profle_Name]\go`
- 해당 경로가 Go Path 환경변수로 사용되는 경로

2. 기본 설정

- go 디렉토리로 이동하여 `bin`, `pkg`, `src` 디렉토리 생성
- src 디렉토리안에 지정된 도메인 디렉토리 추가
    - go에서 다운받은 코드를 지정된 도메인 별로 분류하여 저장함
- src 디렉토리안에 `github.com` 디렉토리 생성
- github.com 디렉토리안에 깃유저별로 디렉토리 구분하여 저장 
- `C:\Users\[Profle_Name]\go\src\github.com\[git_username]\learngo\` 디렉토리 생성

3. vscode 실행

- learngo 디렉토리내 `main.go` 파일 생성 후 vscode로 실행
- vscode 우측 하단에 표시된 go 관련 Extensions 업데이트 모두 설치
- vscode 재실행

# 1. 이론

## 0. Main Package

컴파일 되기 위해서는 main.go 파일에 코드 작성해야함

또한 main pkackage 와 function main 을 꼭 호출해야함

- go 컴파일시 맨 처음 호출되는 부분이기 때문

```python
package main

func main() {

}
```

go 파일 실행 명령어

```
go run main.go
```

## 1. Packages and Imports

터미널에서 문자열 출력

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World")
}
```

vscode에서는 import 없이 함수를 사용해도  `ctrl+s`  저장하면 자동으로 import 코드를 추가해줌  
반대로 함수를 사용하지 않으면 import 코드가 사라짐  

fmt : formatting을 위한 package

- 다른 package function을 export 하고 싶으면 대문자로 시작되는 function을 호출
- 소문자로 시작하는 함수는 private 함수로 외부에서 호출할 수 없음

## 2. 변수와 상수(Variables and Constants)

const : 상수  
let : 변수  

- Go 언어는 타입언어로 타입을 지정해야만 사용가능
- 정해진 타입은 수정할 수 없음
- 축약형 선언은 func 안에서만 사용가능하고 변수에만 적용

```go
package main

import "fmt"

func main() {
	const name1 string = "first"
	fmt.Println(name1)

	var name2 string = "first"
	name2 = "second"
	fmt.Println(name2)

	name3 := "third"
	fmt.Println(name3)
}
```

## 3. 함수(Functions)

Go 언어에서 사용가능한 Type 목록
- https://go101.org/article/type-system-overview.html
- https://golang.org/pkg/go/types/

func에서 변수와 반환을 전달할때는 해당 변수의 타입이 무엇인지 명시해야함

```go
package main

import "fmt"

func multiply(a int, b int) int { // (a, b int) 도 가능
	return a * b
}

func main() {
	fmt.Println(multiply(2, 2))
}
```

함수에서 여러개 변수 return 가능함

```go
package main

import (
	"fmt"
	"strings"
)

func lenAndUpper(name string) (int, string) {
	return len(name), strings.ToUpper(name)
}

func main() {
	totalLenght, _ := lenAndUpper("first") // _ 변수  무시가능
	fmt.Println(totalLenght)
	totalLenght, upperName := lenAndUpper("first")
	fmt.Println(totalLenght, upperName)
}
```

함수에 매개변수 여러개를 전달하여 호출하기

- 매개변수 type 앞에 ...(3개) 추가
- 아래 코드 실행결과는 array 형태로 출력됨
  [one two three]
  
```go
package main

import "fmt"

func repeatMe(words ...string) {
	fmt.Println(words)
}

func main() {
	repeatMe("one", "two", "three")
}
```
