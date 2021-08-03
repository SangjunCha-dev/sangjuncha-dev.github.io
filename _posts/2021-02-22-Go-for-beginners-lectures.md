---
title: 노마드코더 쉽고 빠른 Go 시작하기(nomadcoders Go-for-beginners lectures)
author: Sangjun Cha
date: 2021-02-22 00:09:23 +0900
categories: [Go, Learning, Scraper]
tags: [go, learning, scraper]
pin: false
---

# 0. 소개

설치 환경 : Windows 10
IDE : vscode

## 0.1. 설치

해당 URL에서 golang 설치파일을 다운받아 실행  

- https://golang.org/dl/
- 설치파일에서 안내한 경로인 `C:\Program Files\Go` 폴더에 Go 설치 되었는지 확인

## 0.2. 설정

1. Go Path 환경변수 확인

- Go는 지정된 Go Path 디렉토리에만 저장되어야함
- 시스템 환경변수에 등록된 `GOPATH` 값 확인
- `%USERPROFILE%\go` -> `C:\Users\[Profle_Name]\go`
- 해당 경로가 Go Path 환경변수로 사용되는 경로

2. 기본 설정

- go 디렉토리로 이동하여 `bin`, `pkg`, `src` 디렉토리 생성
- src 디렉토리안에 지정된 도메인 디렉토리 추가
    - go에서 다운받은 코드를 지정된 도메인 별로 분류하여 저장
- src 디렉토리안에 `github.com` 디렉토리 생성
- github.com 디렉토리안에 깃유저별로 디렉토리 구분하여 저장 
- `C:\Users\[Profle_Name]\go\src\github.com\[git_username]\learngo\` 디렉토리 생성

3. vscode 실행

- learngo 디렉토리내 `main.go` 파일 생성 후 vscode로 실행
- vscode 우측 하단에 표시된 go 관련 Extensions 업데이트 모두 설치
- vscode 재실행

# 1. 이론

## 1.0. Main Package

컴파일 되기 위해서는 main.go 파일에 코드 작성해야함

또한 main pkackage 와 main function 을 꼭 호출해야함

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

## 1.1. Packages and Imports

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

`fmt` : formatting을 위한 package
 - 다른 package function을 export 하고 싶으면 대문자로 시작되는 function을 호출
 - 소문자로 시작하는 함수는 private 함수로 외부에서 호출할 수 없음

## 1.2. 변수와 상수(Variables and Constants)

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

## 1.3. 함수(Functions)

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

함수 선언부분에서 return 변수명 명시하면
return 변수를 명시하지 않아도 됨

```go
package main

import (
	"fmt"
	"strings"
)

func lenAndUpper(name string) (lenght int, uppercase string) {
	lenght = len(name)
	uppercase = strings.ToUpper(name)
	return
}

func main() {
	totalLenght, up := lenAndUpper("first")
	fmt.Println(totalLenght, up)
}
```

`defer` : function 실행 끝나고 추가동작 지정가능

```go
package main

import (
	"fmt"
	"strings"
)

func lenAndUpper(name string) (lenght int, uppercase string) {
	defer fmt.Println("I'm done")
	lenght = len(name)
	uppercase = strings.ToUpper(name)
	return
}

func main() {
	totalLenght, up := lenAndUpper("first")
	fmt.Println(totalLenght, up)
}

```

## 1.4. for, range, args

`for` : loop는 for함수로만 가능  
`range` : array에 loop 적용 가능  
	- range는 index를 같이 넘겨줌

for ~ range 사용한 loop 예시  

```go
package main

import (
	"fmt"
)

func superAdd(numbers ...int) int {
	for index, number := range numbers {
		fmt.Println(index, number)
	}
	return 1
}

func main() {
	superAdd(1, 2, 3, 4, 5, 6)
}
```

for 사용한 loop 예시  

```go
func superAdd(numbers ...int) int {
	for i := 0; i < len(numbers); i++ {
		fmt.Println(i, numbers[i])
	}
	return 1
}
```

for ~ range 사용한 loop 예시 2  
 
```go
package main

import (
	"fmt"
)

func superAdd(numbers ...int) int {
	total := 0
	for _, number := range numbers {
		total += number
	}
	return total
}

func main() {
	result := superAdd(1, 2, 3, 4, 5, 6)
	fmt.Println(result)
}
```

## 1.5. If with a Twist

if 조건문 사용형식

```go
package main

import (
	"fmt"
)

func canIDrink(age int) bool {
	if age < 18 {
		return false
	}
	return true
}

func main() {
	fmt.Println(canIDrink((16)))
}
```

if 문안에 조건문에서 사용할 변수를 선언하여 사용가능
- if-else의 조건에서만 사용하기 위해 variable을 생성한다는 의미
- variable expression

```go
func canIDrink(age int) bool {
	if koreanAge := age + 2; koreanAge < 18 {
		return false
	}
	return true
}
```

## 1.6. Switch

```go
package main

import (
	"fmt"
)

func canIDrink(age int) bool {
	switch age {
	case 10:
		return false
	case 18:
		return true
	}
	return false
}

func main() {
	fmt.Println(canIDrink((18)))
}
```

case 구문에 조건식 작성가능

```go
func canIDrink(age int) bool {
	switch {
	case age < 18:
		return false
	case age == 18:
		return true
	case 50 < age:
		return false
	}
	return false
}
```

if문과 마찬가지로 switch 전용 variable 사용가능

```go
func canIDrink(age int) bool {
	switch koreanAge := age + 2; koreanAge {
	case 10:
		return false
	case 18:
		return true
	}
	return false
}
```

## 1.7 Pointers

복사는 기본적으로 값 복사를 실행

```go
package main

import (
	"fmt"
)

func main() {
	a := 2
	b := a
	a = 10
	fmt.Println(&a, &b)
}
```

주소값 대입으로 여러 변수에서 같은 주소의 값 접근가능

```go
func main() {
	a := 2
	b := &a
	fmt.Println(&a, b)
}
```

```go
func main() {
	a := 2
	b := &a
	fmt.Println(a, *b)
}
```

## 1.8 Arrays and Slices

`array` : 크기를 정해서 사용하는 배열 (크기 수정 불가)

```go
package main

import (
	"fmt"
)

func main() {
	names := [5]string{"a", "b", "c"}
	names[3] = "d"
	names[4] = "e"
	fmt.Println(names)
}
```

`slices` : 크기를 정하지 않고 사용하는 배열 (크기 수정 가능)

```go
func main() {
	names := []string{"a", "b", "c"}
	fmt.Println(names)
}
```

`append(slice, item)` : slice 변수에 item 추가

```go
func main() {
	names := []string{"a", "b", "c"}
	names = append(names, "d")
	fmt.Println(names)
}
```

## 1.9 Maps

map : key, value 로 이루어진 자료형

`map[string]string`  
- key : string 타입 선언  
- value : string 타입 선언  

```go
package main

import (
	"fmt"
)

func main() {
	val := map[string]string{"name": "a", "age": "20"}
	fmt.Println(val)
}
```

map, range 같이 사용가능

```go
func main() {
	test := map[string]string{"name": "a", "age": "20"}
	for key, value := range test {
		fmt.Println(key, value)
	}
}
```

```go
func main() {
	test := map[string]string{"name": "a", "age": "20"}
	for _, value := range test {
		fmt.Println(value)
	}
}
```

## 1.10 Structs

map 자료형보다 유연함

```go
package main

import "fmt"

type person struct {
	name    string
	age     int
	favFood []string
}

func main() {
	favFood := []string{"apple", "bottle"}
	test := person{"a", 30, favFood}
	fmt.Println("name", test.name)
	fmt.Println("age", test.age)
	fmt.Println("favFood", test.favFood)
}
```

struct 변수 선언시 변수명도 같이 작성가능

```go
func main() {
	favFood := []string{"apple", "bottle"}
	test := person{name: "a", age: 20, favFood: favFood}
	fmt.Println("name", test.name)
	fmt.Println("age", test.age)
	fmt.Println("favFood", test.favFood)
}

```

# 2. BANK & DICTIONARY PROJECTS

## 2.0 Account + NewAccount

변수명 첫글자가 `대문자`이면 public  
변수명 첫글자가 `소문자`이면 private  
- private은 외부 패키지에서 접근할 수 없음

### 2.0.1 pubilc

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/banking"
)

func main() {
	account := banking.Account{Owner: "foo", Balance: 1000}
	fmt.Println("Owner : ", account.Owner)
	fmt.Println("Balance : ", account.Balance)
}
```

banking/banking.go

```go
package banking

// Account struct
type Account struct {
	Owner   string
	Balance int
}
```

`no required module provides package /lectures/banking`

위와 같은 에러 발생시 아래의 명령어로 mod 파일 생성 및 빌드 진행후 다시 go run 실행  
- go.mod 파일은 모듈의 루트에 존재

```bash
go mod init
go build
```

### 2.0.2 private

외부 패키지에서 account 정보 수정하지 못하도록 private 방식으로 코드 수정

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/accounts"
)

func main() {
	account := accounts.NewAccount("foo")
	fmt.Println("account : ", account)
}
```

생성한 object 주소를 return  

- 이미 만들어진 object를 반환하기 위함  
- 결과값만 저장한 변수를 새로 만들지 않음  

accounts/accounts.go

```go
package accounts

// Account struct
type Account struct {
	owner   string
	balance int
}

// NewAccount creates Account
func NewAccount(owner string) *Account {
	account := Account{owner: owner, balance: 0}
	return &account
}
```

## 2.1 Methods

### 2.1.1 receiver(method 설정)

`a Account` : receiver에서 접근할 struct의 첫 글자를 따서 변수명을 소문자로 지어야함  
- 해당 Account 정보는 복사한 정보

accounts/accounts.go

```go
package accounts

// Account struct
type Account struct {
	owner   string
	balance int
}

// NewAccount creates Account
func NewAccount(owner string) *Account {
	account := Account{owner: owner, balance: 0}
	return &account
}

// Deposit x amount on your account 
func (a Account) Deposit(amount int) {
	a.balance += amount
}
```

### 2.1.2 Pointer receiver

Deposit method 호출한 account 주소 사용

accounts/accounts.go

```go
...

// NewAccount creates Account
func NewAccount(owner string) *Account {
	account := Account{owner: owner, balance: 0}
	return &account
}
```

### 2.1.3 error handling

error 반환 유형 2가지
- `return errors.New()`
- `nil`

```go
...
// Withdraw x amount from your account
func (a *Account) Withdraw(amount int) error {
	if a.balance < amount {
		return errors.New("Cant't withdraw you are poor")
	}
	a.balance -= amount
	return nil
}
```

main.go 실행 결과 Withdraw error가 반환됨
- `log.Fatalln(err)` : 에러구문 출력 후 프로그램 종료
- `fmt.Println(err)` : 에러구문 출력

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/accounts"
)

func main() {
	account := accounts.NewAccount("foo")
	account.Deposit(10)
	fmt.Println("account : ", account)
	fmt.Println("balance : ", account.Balance())
	err := account.Withdraw(20)
	if err != nil {
		log.Fatalln(err)
	}
	fmt.Println("balance : ", account.Balance())
}

```

에러구문을 별도의 변수로 선언하여 사용가능
- 에러 변수명은 `err~` 식으로 선언해야함

```go
...

var errNoMoney = errors.New("Can't withdraw")

...

// Withdraw x amount from your account
func (a *Account) Withdraw(amount int) error {
	if a.balance < amount {
		return errNoMoney
	}
	a.balance -= amount
	return nil
}
```

## 2.2 Finishing Up

Go에서 struct 사용시 내부적으로 호출해주는 method
- `String` : struct 출력시 실행되는 method

method는 struct, type 에 추가할 수 있음

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/accounts"
)

func main() {
	account := accounts.NewAccount("foo")
	account.Deposit(10)
	err := account.Withdraw(20)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println("account : ", account.Owner(), ", balance : ", account.Balance())
}
```

accounts/accounts.go

```go
...

// ChangeOwner of the account
func (a *Account) ChangeOwner(newOnwer string) {
	a.owner = newOnwer
}

// Owner of the account
func (a Account) Owner() string {
	return a.owner
}

func (a Account) String() string {
	return fmt.Sprint(a.Owner(), "'s account.\nHas: ", a.Balance())
}
```

## 2.3 Dictionary

type Dictionary는 `map[string]string`자료형의 별명(alias)이다.

mydict/mydict.go

```go
package mydict

// Dictionary type
type Dictionary map[string]string
```

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/mydict"
)

func main() {
	dictionary := mydict.Dictionary{}
	dictionary["hello"] = "foo"
	fmt.Println(dictionary)
}
```

### 2.3.1 Search

dictionary 와 method 사용

Search method 추가


mydict/mydict.go

```go
package mydict

import "errors"

// Dictionary type
type Dictionary map[string]string

var errNotFound = errors.New("Not Found")

// Search for a word
func (d Dictionary) Search(word string) (string, error) {
	value, exists := d[word]
	if exists {
		return value, nil
	}
	return "", errNotFound
}
```

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/mydict"
)

func main() {
	dictionary := mydict.Dictionary{"first": "First word"}
	definition, err := dictionary.Search("second")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(definition)
	}
}
```

### 2.3.2 Add

Add method 추가

mydict/mydict.go

if문 형식

```go
var errWordExists = erros.New("That word already exists")
...

// Add a word to the dictionary
func (d Dictionary) Add(word, def string) error {
	_, err := d.Search(word)
	if err == errNotFound {
		d[word] = def
	} else if err == nil {
		return errWordExists
	}
	return nil
}
```

switch문 형식

```go
// Add a word to the dictionary
func (d Dictionary) Add(word, def string) error {
	_, err := d.Search(word)
	switch err {
	case errNotFound:
		d[word] = def
	case nil:
		return errWordExists
	}
	return nil
}
```

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/mydict"
)

func main() {
	dictionary := mydict.Dictionary{}
	word := "hello"
	definition := "Greeting"
	err := dictionary.Add(word, definition)
	if err != nil {
		fmt.Println(err)
	}
	hello, _ := dictionary.Search(word)
	fmt.Println("found :", word, ", definition :", hello)
	err2 := dictionary.Add(word, definition)
	if err2 != nil {
		fmt.Println(err2)
	}
}
```

### 2.3.3 Update Delete

Update method 추가

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/mydict"
)

func main() {
	dictionary := mydict.Dictionary{}
	baseWord := "hello"
	dictionary.Add(baseWord, "First")
	err := dictionary.Update(baseWord, "Second")
	if err != nil {
		fmt.Println(err)
	}
	word, _ := dictionary.Search(baseWord)
	fmt.Println(word)
}
```

mydict/mydict.go

```go
var (
	errNotFound   = errors.New("Not Found")
	errCantUpdate = errors.New("Cant update non-existing word")
	errWordExists = errors.New("That word already exists")
)

...

// Update a word
func (d Dictionary) Update(word, definition string) error {
	_, err := d.Search(word)
	switch err {
	case nil:
		d[word] = definition
	case errNotFound:
		return errCantUpdate
	}
	return nil
}
```

Delete method 추가

main.go

```go
package main

import (
	"fmt"

	"github.com/sangjuncha-dev/lectures/mydict"
)

func main() {
	dictionary := mydict.Dictionary{}
	baseWord := "hello"
	dictionary.Add(baseWord, "First")
	dictionary.Search(baseWord)
	dictionary.Delete(baseWord)
	word, err := dictionary.Search(baseWord)
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println(word)
	}
}
```

mydict/mydict.go

```go
...

// Delete a word
func (d Dictionary) Delete(word string) {
	delete(d, word)
}
```

# 3. URL CHECKER & GO ROUTINES

## 3.0 hitURL

hit : 인터넷 웹 서버의 파일 1개에 접속하는것을 뜻함

request 사용을 위해 내장 library `http` 사용

main.go

```go
package main

import (
	"errors"
	"fmt"
	"net/http"
)

var (
	errRequestFailed = errors.New("Request failed")
)

func main() {
	urls := []string{
		"https://www.airbnb.com/",
		"https://www.google.com/",
		"https://www.amazon.com/",
		"https://www.reddit.com/",
		"https://soundcloud.com/",
		"https://www.facebook.com",
		"https://www.instagram.com/",
		"https://academy.nomadcoders.co/",
	}
	for _, url := range urls {
		hitURL(url)
	}
	for url, result := range results {
		fmt.Println(url, result)
	}
}

func hitURL(url string) error {
	fmt.Println("Chacking :", url)
	resp, err := http.Get(url)
	if err != nil || 400 <= resp.StatusCode {
		return errRequestFailed
	}
	return nil
}
```

## 3.1 Slow URLChecker

panic : 컴파일러가 못찾은 에러

map 자료형은 초기화 안 하면 값을 넣을 수 없음

```go
var results map[string]string
results["hello"] = "Hello"
```

map 자료형은 빈 값으로 초기화 선언해야 값을 넣을 수 있음

```go
var results = map[string]string{}
results["hello"] = "Hello"
```

`make()` : empty map 만들고 초기화 해주는 함수

```go
var results = make(map[string]string)
```

main.go

```go
package main

import (
	"errors"
	"fmt"
	"net/http"
)

var (
	errRequestFailed = errors.New("Request failed")
)

func main() {
	var results = make(map[string]string)
	urls := []string{
		"https://www.airbnb.com/",
		"https://www.google.com/",
		"https://www.amazon.com/",
		"https://www.reddit.com/",
		"https://soundcloud.com/",
		"https://www.facebook.com",
		"https://www.instagram.com/",
		"https://academy.nomadcoders.co/",
	}
	for _, url := range urls {
		result := "OK"
		err := hitURL(url)
		if err != nil {
			result = "FAILED"
		}
		results[url] = result
	}
	for url, result := range results {
		fmt.Println(url, result)
	}
}

func hitURL(url string) error {
	fmt.Println("Chacking :", url)
	resp, err := http.Get(url)
	if err != nil || 400 <= resp.StatusCode {
		fmt.Println(err, resp.StatusCode)
		return errRequestFailed
	}
	return nil
}

```

status code
 - 401 : Unauthorized
 - 429 : Too Many Requests

## 3.2 Goroutines

Top-down 방식의 프로그래밍

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	myCount("foo")
	myCount("bar")
}

func myCount(person string) {
	for i := 0; i < 10; i++ {
		fmt.Println(person, "is number :", i)
		time.Sleep(time.Second)
	}
}
```

`Goroutines` : 다른 함수와 동시에 실행시키는 함수
 - Goroutines은 프로그램이 작동하는 동안만 실행

main 함수 부분에서 go 루틴사용시 함수가 끝나기전에 main함수가 끝나면 프로그램이 종료되므로 main함수가 끝나지 않게 만든 상태에서 go 루틴 사용해야함


```go
package main

import (
	"fmt"
	"time"
)

func main() {
	go myCount("foo")
	go myCount("bar")
	time.Sleep(time.Second * 5)
}

func myCount(person string) {
	for i := 0; i < 10; i++ {
		fmt.Println(person, "is number :", i)
		time.Sleep(time.Second)
	}
}
```

## 3.3 Channels

`Channel`
- `goroutine`과 `main`함수 사이에 정보를 전달하기 위한 방법
- `goroutine`에서 다른 `goroutine`으로 커뮤니케이션
- 채널을 통해서 데이터를 받거나 보낼 수 있음

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	c := make(chan bool)
	people := [2]string{"foo", "bar"}
	for _, person := range people {
		go isMy(person, c)
	}
	fmt.Println(<-c)
	fmt.Println(<-c)
}

func isMy(person string, c chan bool) {
	time.Sleep(time.Second * 5)
	fmt.Println(person)
	c <- true
}
```

## 3.4 Channels Recap

blocking operation : 해당 작업이 끝날때까지 멈춤
- `<-c` : 채널로부터 메세지를 얻음
- 하나의 메세지를 받으면 다음 라인으로 넘어감

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	c := make(chan string)
	people := [2]string{"foo", "bar"}
	for _, person := range people {
		go isMy(person, c)
	}
	fmt.Println("Waiting for message")
	for i := 0; i < len(people); i++ {
		fmt.Print("waiting for ", i, " ")
		fmt.Println(<-c)
	}
}

func isMy(person string, c chan string) {
	time.Sleep(time.Second * 5)
	c <- person + " is OK"
}
```

1. 채널의 메세지를 받는것 == blocking operation
2. 채널을 통해서 보내는 부분과 받는 부분 모두 타입 지정해야함  

## 3.5 URLChecker + Go Routines

`c chan<-` : Send Only(보내기만 가능)

```go
package main

import (
	"errors"
	"fmt"
	"net/http"
)

type requestResult struct {
	url    string
	status string
}

var errRequestFailed = errors.New("Request failed")

func main() {
	var results = make(map[string]string)
	c := make(chan requestResult)
	urls := []string{
		"https://www.airbnb.com/",
		"https://www.google.com/",
		"https://www.amazon.com/",
		"https://www.reddit.com/",
		"https://soundcloud.com/",
		"https://www.facebook.com",
		"https://www.instagram.com/",
		"https://academy.nomadcoders.co/",
	}
	for _, url := range urls {
		go hitURL(url, c)
	}
	for i := 0; i < len(urls); i++ {
		result := <-c
		results[result.url] = result.status
	}
	for url, status := range results {
		fmt.Println(url, status)
	}
}

func hitURL(url string, c chan<- requestResult) {
	resp, err := http.Get(url)
	status := "OK"
	if err != nil || 400 <= resp.StatusCode {
		status = "FAILED"
	}
	c <- requestResult{url: url, status: status}
}
```

# 4. JOB SCRAPPER

## 4.0 getPages

라이브러리 설치

```bash
go get github.com/PuerkitoBio/goquery
```

goquery Doc URL
- https://github.com/PuerkitoBio/goquery
- https://pkg.go.dev/github.com/PuerkitoBio/goquery

strconv : 타입 변환 라이브러리

```go
package main

import (
	"fmt"
	"log"
	"net/http"
	"strconv"

	"github.com/PuerkitoBio/goquery"
)

var baseURL string = "https://kr.indeed.com/jobs?q=python&limit=50"

func main() {
	totalPages := getPages()
	// fmt.Println(totalPages)

	for i := 0; i < totalPages; i++ {
		getPage(i)
	}
}

func getPage(page int) {
	pageURL := baseURL + "&start=" + strconv.Itoa(page*50)
	fmt.Println("Requesting :", pageURL)
}

func getPages() int {
	pages := 0
	res, err := http.Get(baseURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	doc.Find(".pagination").Each(func(i int, s *goquery.Selection) {
		// fmt.Println(s.Html())
		pages = s.Find("a").Length()
	})

	return pages
}

func checkErr(err error) {
	if err != nil {
		log.Fatalln(err)
	}
}

func checkCode(res *http.Response) {
	if res.StatusCode != 200 {
		log.Fatalln("Request failed with Status :", res.StatusCode)
	}
}
```

## 4.1 extractJob

strings.TrimSpace(str) : 문자열 양쪽 끝 공백 제거

strings.Fields(str) : 문자열 배열형태로 반환

strings.Join([]string, " ") : 문자열 배열을 " "으로 합쳐서 문자열 변환

append(slice1, slice2) : slice1배열 안의 원소로 slice2 추가

append(slice1, slice2...) : 두개의 배열을 하나의 배열로 합치기

```go
package main

import (
	"fmt"
	"log"
	"net/http"
	"strconv"
	"strings"

	"github.com/PuerkitoBio/goquery"
)

type extractedJob struct {
	id       string
	title    string
	location string
	salary   string
	summary  string
}

var baseURL string = "https://kr.indeed.com/jobs?q=python&limit=50"

func main() {
	var jobs []extractedJob
	totalPages := getPages()

	for i := 0; i < totalPages; i++ {
		extractedJobs := getPage(i)
		jobs = append(jobs, extractedJobs...)
	}
	fmt.Println(jobs)
}

func getPage(page int) []extractedJob {
	var jobs []extractedJob
	pageURL := baseURL + "&start=" + strconv.Itoa(page*50)
	fmt.Println("Requesting :", pageURL)
	res, err := http.Get(pageURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	searchCards := doc.Find(".jobsearch-SerpJobCard")
	searchCards.Each(func(i int, card *goquery.Selection) {
		job := extractJob(card)
		jobs = append(jobs, job)
	})

	return jobs
}

func extractJob(card *goquery.Selection) extractedJob {
	id, _ := card.Attr("data-jk")
	title := cleanString(card.Find(".title>a").Text())
	location := cleanString(card.Find(".sjcl").Text())
	salary := cleanString(card.Find(".salaryText").Text())
	summary := cleanString(card.Find(".summary").Text())
	return extractedJob{
		id:       id,
		title:    title,
		location: location,
		salary:   salary,
		summary:  summary,
	}
}

// card 데이터에서 공백 제거
func cleanString(str string) string {
	return strings.Join(strings.Fields(strings.TrimSpace(str)), " ")
}

// 전체 페이지수 얻기
func getPages() int {
	pages := 0
	res, err := http.Get(baseURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	doc.Find(".pagination").Each(func(i int, s *goquery.Selection) {
		pages = s.Find("a").Length()
	})

	return pages
}

func checkErr(err error) {
	if err != nil {
		log.Fatalln(err)
	}
}

func checkCode(res *http.Response) {
	if res.StatusCode != 200 {
		log.Fatalln("Request failed with Status :", res.StatusCode)
	}
}
```

## 4.2 Writing Jobs

encoding/csv : csv 관련 패키지

w.Write([]string) : 파일 버퍼에 기록

defer w.Flush() : 함수가 끝나는 시점에 파일에 데이터를 작성하는 함수

```go
package main

import (
	"encoding/csv"
	"fmt"
	"log"
	"net/http"
	"os"
	"strconv"
	"strings"

	"github.com/PuerkitoBio/goquery"
)

type extractedJob struct {
	id       string
	title    string
	location string
	salary   string
	summary  string
}

var baseURL string = "https://kr.indeed.com/jobs?q=python&limit=50"

func main() {
	var jobs []extractedJob
	totalPages := getPages()

	for i := 0; i < totalPages; i++ {
		extractedJobs := getPage(i)
		jobs = append(jobs, extractedJobs...)
	}

	writeJons(jobs)
	fmt.Println("Done, extracted", len(jobs))
}

func writeJons(jobs []extractedJob) {
	file, err := os.Create("jobs.csv")
	checkErr(err)

	w := csv.NewWriter(file)
	defer w.Flush()

	headers := []string{"Link", "Title", "Location", "Salary", "Summary"}

	wErr := w.Write(headers)
	checkErr(wErr)

	for _, job := range jobs {
		jobSclice := []string{"https://kr.indeed.com/viewjob?jk=" + job.id, job.title, job.location, job.salary, job.summary}
		jwErr := w.Write(jobSclice)
		checkErr(jwErr)
	}
}

func getPage(page int) []extractedJob {
	var jobs []extractedJob
	pageURL := baseURL + "&start=" + strconv.Itoa(page*50)
	fmt.Println("Requesting :", pageURL)
	res, err := http.Get(pageURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	searchCards := doc.Find(".jobsearch-SerpJobCard")
	searchCards.Each(func(i int, card *goquery.Selection) {
		job := extractJob(card)
		jobs = append(jobs, job)
	})

	return jobs
}

// 카드에서 일자리 정보 추출
func extractJob(card *goquery.Selection) extractedJob {
	id, _ := card.Attr("data-jk")
	title := cleanString(card.Find(".title>a").Text())
	location := cleanString(card.Find(".sjcl").Text())
	salary := cleanString(card.Find(".salaryText").Text())
	summary := cleanString(card.Find(".summary").Text())
	return extractedJob{
		id:       id,
		title:    title,
		location: location,
		salary:   salary,
		summary:  summary,
	}
}

// 데이터에서 공백 제거
func cleanString(str string) string {
	return strings.Join(strings.Fields(strings.TrimSpace(str)), " ")
}

// 전체 페이지수 얻기
func getPages() int {
	pages := 0
	res, err := http.Get(baseURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	doc.Find(".pagination").Each(func(i int, s *goquery.Selection) {
		pages = s.Find("a").Length()
	})

	return pages
}

func checkErr(err error) {
	if err != nil {
		log.Fatalln(err)
	}
}

func checkCode(res *http.Response) {
	if res.StatusCode != 200 {
		log.Fatalln("Request failed with Status :", res.StatusCode)
	}
}
```

## 4.3 Channels Time

```go
package main

import (
	"encoding/csv"
	"fmt"
	"log"
	"net/http"
	"os"
	"strconv"
	"strings"

	"github.com/PuerkitoBio/goquery"
)

type extractedJob struct {
	id       string
	title    string
	location string
	salary   string
	summary  string
}

var baseURL string = "https://kr.indeed.com/jobs?q=python&limit=50"

func main() {
	var jobs []extractedJob
	c := make(chan []extractedJob)
	totalPages := getPages()

	for i := 0; i < totalPages; i++ {
		go getPage(i, c)
	}
	for i := 0; i < totalPages; i++ {
		extractJobs := <-c
		jobs = append(jobs, extractJobs...)
	}

	writeJons(jobs)
	fmt.Println("Done, extracted", len(jobs))
}

func getPage(page int, mainC chan<- []extractedJob) {
	var jobs []extractedJob
	c := make(chan extractedJob)
	pageURL := baseURL + "&start=" + strconv.Itoa(page*50)
	fmt.Println("Requesting :", pageURL)
	res, err := http.Get(pageURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	searchCards := doc.Find(".jobsearch-SerpJobCard")
	searchCards.Each(func(i int, card *goquery.Selection) {
		go extractJob(card, c)
	})

	for i := 0; i < searchCards.Length(); i++ {
		job := <-c
		jobs = append(jobs, job)
	}
	mainC <- jobs
}

// 카드에서 일자리 정보 추출
func extractJob(card *goquery.Selection, c chan<- extractedJob) {
	id, _ := card.Attr("data-jk")
	title := cleanString(card.Find(".title>a").Text())
	location := cleanString(card.Find(".sjcl").Text())
	salary := cleanString(card.Find(".salaryText").Text())
	summary := cleanString(card.Find(".summary").Text())
	c <- extractedJob{
		id:       id,
		title:    title,
		location: location,
		salary:   salary,
		summary:  summary,
	}
}

// 데이터에서 공백 제거
func cleanString(str string) string {
	return strings.Join(strings.Fields(strings.TrimSpace(str)), " ")
}

// 전체 페이지수 얻기
func getPages() int {
	pages := 0
	res, err := http.Get(baseURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	doc.Find(".pagination").Each(func(i int, s *goquery.Selection) {
		pages = s.Find("a").Length()
	})

	return pages
}

func writeJons(jobs []extractedJob) {
	file, err := os.Create("jobs.csv")
	checkErr(err)

	w := csv.NewWriter(file)
	defer w.Flush()

	headers := []string{"Link", "Title", "Location", "Salary", "Summary"}

	wErr := w.Write(headers)
	checkErr(wErr)

	for _, job := range jobs {
		jobSclice := []string{"https://kr.indeed.com/viewjob?jk=" + job.id, job.title, job.location, job.salary, job.summary}
		jwErr := w.Write(jobSclice)
		checkErr(jwErr)
	}
}

func checkErr(err error) {
	if err != nil {
		log.Fatalln(err)
	}
}

func checkCode(res *http.Response) {
	if res.StatusCode != 200 {
		log.Fatalln("Request failed with Status :", res.StatusCode)
	}
}
```

# 5. WEB SERVER WITH ECHO

## 5.0 Setup

go echo 서버 만들기

라이브러리 설치

```bash
go get github.com/labstack/echo
```

main.go

```go
package main

import (
	"net/http"

	"github.com/labstack/echo"
)

func handleHome(c echo.Context) error {
	return c.String(http.StatusOK, "Hello, World!")
}

func main() {
	e := echo.New()
	e.GET("/", handleHome)
	e.Logger.Fatal(e.Start(":1323"))
}
```

`go run main.go` 실행 후

웹 브라우저에서 `localhost:1323` 접속시 접속되는 것을 확인

## 5.1 scrapper 서버 생성

`scrapper` 폴더 생성후 해당 폴더로 `main.go` 파일 이동후 아래와 같이 수정
- 파일명 `main.go` -> `scrapper.go`
- 패키지명 `package main` -> `package scrapper`
- 함수명 `func main()` -> `func scrape`

scrapper/scrapper.go

```go
package scrapper

import (
	"encoding/csv"
	"fmt"
	"log"
	"net/http"
	"os"
	"strconv"
	"strings"

	"github.com/PuerkitoBio/goquery"
)

type extractedJob struct {
	id       string
	title    string
	location string
	salary   string
	summary  string
}

// Scrape Indeed by a term
func Scrape(term string) {
	var baseURL string = "https://kr.indeed.com/jobs?q=" + term + "&limit=50"
	var jobs []extractedJob
	c := make(chan []extractedJob)
	totalPages := getPages(baseURL)

	for i := 0; i < totalPages; i++ {
		go getPage(i, baseURL, c)
	}
	for i := 0; i < totalPages; i++ {
		extractJobs := <-c
		jobs = append(jobs, extractJobs...)
	}

	writeJobs(jobs)
	fmt.Println("Done, extracted", len(jobs))
}

func getPage(page int, url string, mainC chan<- []extractedJob) {
	var jobs []extractedJob
	c := make(chan extractedJob)
	pageURL := url + "&start=" + strconv.Itoa(page*50)
	fmt.Println("Requesting :", pageURL)
	res, err := http.Get(pageURL)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	searchCards := doc.Find(".jobsearch-SerpJobCard")
	searchCards.Each(func(i int, card *goquery.Selection) {
		go extractJob(card, c)
	})

	for i := 0; i < searchCards.Length(); i++ {
		job := <-c
		jobs = append(jobs, job)
	}
	mainC <- jobs
}

// 카드에서 일자리 정보 추출
func extractJob(card *goquery.Selection, c chan<- extractedJob) {
	id, _ := card.Attr("data-jk")
	title := CleanString(card.Find(".title>a").Text())
	location := CleanString(card.Find(".sjcl").Text())
	salary := CleanString(card.Find(".salaryText").Text())
	summary := CleanString(card.Find(".summary").Text())
	c <- extractedJob{
		id:       id,
		title:    title,
		location: location,
		salary:   salary,
		summary:  summary,
	}
}

// CleanString cleans a string
func CleanString(str string) string {
	return strings.Join(strings.Fields(strings.TrimSpace(str)), " ")
}

// 전체 페이지수 얻기
func getPages(url string) int {
	pages := 0
	res, err := http.Get(url)
	checkErr(err)
	checkCode(res)

	defer res.Body.Close()

	doc, err := goquery.NewDocumentFromReader(res.Body)
	checkErr(err)

	doc.Find(".pagination").Each(func(i int, s *goquery.Selection) {
		pages = s.Find("a").Length()
	})

	return pages
}

func writeJobs(jobs []extractedJob) {
	file, err := os.Create("jobs.csv")
	checkErr(err)

	w := csv.NewWriter(file)
	defer w.Flush()

	headers := []string{"Link", "Title", "Location", "Salary", "Summary"}

	wErr := w.Write(headers)
	checkErr(wErr)

	for _, job := range jobs {
		jobSclice := []string{"https://kr.indeed.com/viewjob?jk=" + job.id, job.title, job.location, job.salary, job.summary}
		jwErr := w.Write(jobSclice)
		checkErr(jwErr)
	}
}

func checkErr(err error) {
	if err != nil {
		log.Fatalln(err)
	}
}

func checkCode(res *http.Response) {
	if res.StatusCode != 200 {
		log.Fatalln("Request failed with Status :", res.StatusCode)
	}
}
```

main.go

```go
package main

import (
	"strings"

	"github.com/SangjunCha-dev/learngo/scrapper"
	"github.com/labstack/echo"
)

func handleHome(c echo.Context) error {
	return c.File("home.html")
}

func handleScrape(c echo.Context) error {
	term := strings.ToLower(scrapper.CleanString(c.FormValue("term")))
	scrapper.Scrape(term)
	return nil
}

func main() {
	e := echo.New()
	e.GET("/", handleHome)
	e.POST("/scrape", handleScrape)
	e.Logger.Fatal(e.Start(":1323"))
}
```

home.html 파일 생성

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Go Jobs</title>
</head>
    <h1>Go Jobs</h1>
    <h3>Indeed.com scrapper</h3>
    <form method="POST" action="/scrape">
        <input placeholder="what job do you want" name="term"/>
        <button>Search</button>
    </form>
</html>
```

## 5.2 File Download

return c.Attachment(filename1, filename2) : filename1 파일 찾아서 filename2 이름으로 다운로드

main.go

```go
package main

import (
	"os"
	"strings"

	"github.com/SangjunCha-dev/learngo/scrapper"
	"github.com/labstack/echo"
)

const fileName string = "jobs.csv"

func handleHome(c echo.Context) error {
	return c.File("home.html")
}

func handleScrape(c echo.Context) error {
	defer os.Remove(fileName)
	term := strings.ToLower(scrapper.CleanString(c.FormValue("term")))
	scrapper.Scrape(term)
	return c.Attachment(fileName, fileName)
}

func main() {
	e := echo.New()
	e.GET("/", handleHome)
	e.POST("/scrape", handleScrape)
	e.Logger.Fatal(e.Start(":1323"))
}
```


[buffalo](https://gobuffalo.io/en/) - django의 go버전 프레임워크

db, template, api, server, logger, orm 등 모든 것이 있는 풀스택 프레임워크

라우팅, hot code reload, 프론트엔드 파이프라인, 모델, 테스트 등 기능 지원