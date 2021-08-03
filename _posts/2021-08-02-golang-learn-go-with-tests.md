---
title: golang learn-go-with-tests GitBook 정리
author: Sangjun Cha
date: 2021-08-02 17:56:14 +0900
categories: [Go]
tags: [go]
pin: false
---



# 사이트 주소

[원본 링크](https://github.com/quii/learn-go-with-tests)  
[한글 번역 링크](https://github.com/MiryangJung/learn-go-with-tests-ko)



# 5. struct, method & interface

## struct
구조체의 변수 첫글자는 대문자여야한다. (외부에서 사용 가능하도록)

```go
type Rectangle struct {
	Width  float64
	Height float64
}
```

구조체 필드이름을 선택적으로 지정할 수 있다.
```go
{shape: Rectangle{Width: 12, Height: 6}, want: 72.0},
{shape: Circle{Radius: 10}, want: 314.1592653589793},
```

## methods
func (receiverName ReceiverType) MethodName(args)
`r Rectangle` 수신자 변수를 유형의 첫 번째 문자로 지정하는 것이 Go의 관례이다.

```go
func (r Rectangle) Area() float64 {
	return 0
}
```

## interface
Go에서 인터페이스 자료형은 암시적 이다. 전달하는 유형이 인터페이스가 요청하는 유형과 일치하면 컴파일 된다.

```go
type Shape interface {
	Area() float64
}
```

## 익명구조
익명구조 예시로 shape와 want라는 두 개의 필드가 있는 []struct를 사용하여 구조체를 선언한다.
```go
areaTests := []struct {
	shape Shape
	want  float64
}{
	{Rectangle{12, 6}, 72.0},
	{Circle{10}, 314.1592653589793},
}
```

## 예제 코드

struct_method_interface_test.go
```go
package main

import (
	"math"
	"testing"
)

type Rectangle struct {
	Width  float64
	Height float64
}

func (r Rectangle) Area() float64 {
	return r.Width * r.Height
}

type Circle struct {
	Radius float64
}

func (c Circle) Area() float64 {
	return math.Pi * c.Radius * c.Radius
}

type Triangle struct {
	Base   float64
	Height float64
}

func (t Triangle) Area() float64 {
	return (t.Base * t.Height) * 0.5
}

type Shape interface {
	Area() float64
}

func TestArea(t *testing.T) {
	areaTests := []struct {
		name    string
		shape   Shape
		hasArea float64
	}{
		{shape: Rectangle{Width: 12, Height: 6}, hasArea: 72.0},
		{shape: Circle{Radius: 10}, hasArea: 314.1592653589793},
		{shape: Triangle{Base: 12, Height: 6}, hasArea: 36.0},
	}

	for _, tt := range areaTests {
		got := tt.shape.Area()
		if got != tt.hasArea {
			t.Errorf("%#v got %g want %g", tt.shape, got, tt.hasArea)
		}
	}
}
```



# 6. pointer & error

## Pointer

구조체에 대한 포인터는 구조체 포인터 라고 불리고 특별한 역참조에 대한 명시 없이도 `자동 역참조`가 된다. `(*w).balance` 와 `w.balance` 는 같은 의미로 사용된다.

## Error

반환되는 에러를 확인하지 않은 코드라인을 확인할 수 있는 라이브러리 설치
`go get -u github.com/kisielk/errcheck`
실행 명령 : `errcheck .`

## 예제 코드

pointer_error_test.go
```go 
package main

import (
	"errors"
	"fmt"
	"testing"
)

type Bitcoin int

func (b Bitcoin) String() string {
	return fmt.Sprintf("%d BTC", b)
}

type Stringer interface {
	String() string
}

type Wallet struct {
	balance Bitcoin
}

func (w *Wallet) Deposit(amount Bitcoin) {
	fmt.Printf("address of balance in Deposit is %v \n", &w.balance)
	w.balance += amount
}

func (w *Wallet) Balance() Bitcoin {
	return w.balance
}

var ErrInsufficientFunds = errors.New("cannot withdraw, insufficient funds")

func (w *Wallet) Withdraw(amount Bitcoin) error {
	if w.balance < amount {
		return ErrInsufficientFunds
	}

	w.balance -= amount
	return nil
}

func TestWallet(t *testing.T) {
	t.Run("Deposit", func(t *testing.T) {
		wallet := Wallet{}
		wallet.Deposit(Bitcoin(10))
		assertBalance(t, wallet, Bitcoin(10))
	})

	t.Run("Withdraw", func(t *testing.T) {
		wallet := Wallet{balance: Bitcoin(20)}
		err := wallet.Withdraw(Bitcoin(10))

		assertBalance(t, wallet, Bitcoin(10))
		assertNoError(t, err)
	})

	t.Run("Withdraw insufficient funds", func(t *testing.T) {
		startingBalance := Bitcoin(20)
		wallet := Wallet{startingBalance}
		err := wallet.Withdraw(Bitcoin(100))

		assertBalance(t, wallet, startingBalance)
		assertError(t, err, ErrInsufficientFunds)
	})
}

func assertBalance(t testing.TB, wallet Wallet, want Bitcoin) {
	t.Helper()
	got := wallet.Balance()

	if got != want {
		t.Errorf("got %q want %q", got, want)
	}
}

func assertNoError(t testing.TB, got error) {
	t.Helper()
	if got != nil {
		t.Fatal("got an error but didn't want one")
	}
}

func assertError(t testing.TB, got error, want error) {
	t.Helper()
	if got == nil {
		t.Fatal("want on error but didn't get one")
	}

	if got != want {
		t.Errorf("got %q, want %q", got, want)
	}
}
```



# 7. map

## map
맵을 선언하려면 map이라는 키워드로 시작하고 두 개의 타입이 있어야한다.  
첫번째 타입 : key 타입으로 비교 가능한 타입만 사용 가능하다.  
두번째 타입 : value 타입으로 어떤타입이든 사용 가능하다.  

map은 두 개의 값을 반환하며 두번째 값은 키를 찾는데 성공 여부를 boolean으로 반환한다.

```go
definition, ok := d[word]
```

## error

Error는 .Error() 메소드를 통해 문자열로 변환될 수 있다.

## pointer

map의 주소를 전달(&myMap)하지 않고서도 수정할 수 있다

## map 초기화 방법

```go
var dictionary = map[string]string{}

// OR

var dictionary = make(map[string]string)
```

## 예제 코드

dictionary_test.go
```go

package main

import (
	"testing"
)

const (
	ErrNotFound         = DictionaryErr("could not find the word you were looking for")
	ErrWordExists       = DictionaryErr("cannot add word because it already exists")
	ErrWordDoesNotExist = DictionaryErr("cannot update word because it does not exist")
)

type DictionaryErr string

func (e DictionaryErr) Error() string {
	return string(e)
}

type Dictionary map[string]string

func (d Dictionary) Search(word string) (string, error) {
	definition, ok := d[word]
	if !ok {
		return "", ErrNotFound
	}
	return definition, nil
}

func (d Dictionary) Add(word, definition string) error {
	_, err := d.Search(word)

	switch err {
	case ErrNotFound:
		d[word] = definition
	case nil:
		return ErrWordExists
	default:
		return err
	}

	return nil
}

func (d Dictionary) Update(word, definition string) error {
	_, err := d.Search(word)

	switch err {
	case ErrNotFound:
		return ErrWordDoesNotExist
	case nil:
		d[word] = definition
	default:
		return err
	}

	return nil
}

func (d Dictionary) Delete(word string) {
	delete(d, word)
}

func TestSearch(t *testing.T) {
	dictionary := Dictionary{"test": "this is just a test"}

	t.Run("known word", func(t *testing.T) {
		got, _ := dictionary.Search("test")
		want := "this is just a test"

		assertStrings(t, got, want)
	})

	t.Run("unknown word", func(t *testing.T) {
		_, got := dictionary.Search("unknown")

		assertError(t, got, ErrNotFound)
	})
}

func TestAdd(t *testing.T) {
	t.Run("new word", func(t *testing.T) {
		dictionary := Dictionary{}
		word := "test"
		definition := "this is just a test"

		err := dictionary.Add(word, definition)

		assertError(t, err, nil)
		assertDefinition(t, dictionary, word, definition)
	})

	t.Run("existing word", func(t *testing.T) {
		word := "test"
		definition := "this is just a test"
		dictionary := Dictionary{word: definition}
		err := dictionary.Add(word, "new test")

		assertError(t, err, ErrWordExists)
		assertDefinition(t, dictionary, word, definition)
	})
}

func TestUpdate(t *testing.T) {
	t.Run("existing word", func(t *testing.T) {
		word := "test"
		definition := "this is just a test"
		newDefinition := "new definition"
		dictionary := Dictionary{word: definition}

		err := dictionary.Update(word, newDefinition)

		assertError(t, err, nil)
		assertDefinition(t, dictionary, word, newDefinition)
	})

	t.Run("new word", func(t *testing.T) {
		word := "test"
		definition := "this is just a test"
		dictionary := Dictionary{}

		err := dictionary.Update(word, definition)

		assertError(t, err, ErrWordExists)
	})
}

func TestDelete(t *testing.T) {
	word := "test"
	dictionary := Dictionary{word: "test definition"}

	dictionary.Delete(word)

	_, err := dictionary.Search(word)
	if err != ErrNotFound {
		t.Errorf("Expected %q to be deleted", word)
	}
}

func assertStrings(t testing.TB, got, want string) {
	t.Helper()

	if got != want {
		t.Errorf("got %q want %q", got, want)
	}
}

func assertError(t testing.TB, got, want error) {
	t.Helper()

	if got != want {
		t.Errorf("got %q want %q", got, want)
	}
}

func assertDefinition(t testing.TB, dictionary Dictionary, word, definition string) {
	t.Helper()

	got, err := dictionary.Search(word)
	if err != nil {
		t.Fatal("should find added word :", err)
	}

	if definition != got {
		t.Errorf("got %q want %q", got, definition)
	}
}
```



# 8. dependency injection

`fmt.Printf` : 기본적으로 stdout을 사용한다.  
`fmt.Fprintf` : fmt.Printf와 비슷하지만, 문자열을 보낼 곳 Writer를 가진다.  

## 특징

- 프레임워크가 필요하지 않다.
- 디자인을 지나치게 복잡하게 하지 않는다.
- 테스트를 용이하게 한다.
- 범용 함수를 작성할 수 있다.

## 예제코드

di_test.go
```go
package main

import (
	"bytes"
	"fmt"
	"io"
	"net/http"
	"testing"
)

func Greet(writer io.Writer, name string) {
	fmt.Fprintf(writer, "Hello, %s", name)
}

func MyGreeterHandler(w http.ResponseWriter, r *http.Request) {
	Greet(w, "world")
}

func TestGreat(t *testing.T) {
	buffer := bytes.Buffer{}
	Greet(&buffer, "Chris")

	got := buffer.String()
	want := "Hello, Chris"

	if got != want {
		t.Errorf("got %q want %q", got, want)
	}
}

func main() {
	http.ListenAndServe(":5000", http.HandlerFunc(MyGreeterHandler))
}
```
