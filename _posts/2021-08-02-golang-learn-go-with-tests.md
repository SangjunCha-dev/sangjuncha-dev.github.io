---
title: [golang] learn-go-with-tests
author: Sangjun Cha
date: 2021-08-02 17:56:14 +0900
categories: [Go]
tags: [go]
pin: false
---

# 사이트 주소

원본 : https://github.com/quii/learn-go-with-tests  
한글 번역본 : https://github.com/MiryangJung/learn-go-with-tests-ko

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
