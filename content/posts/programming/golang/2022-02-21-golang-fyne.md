---
title: "[golang] gui 라이브러리 fyne"
date: 2022-02-21T10:50:16+09:00
description: "golang gui 라이브러리 fyne"
tags: ["go", "gui"]
categories: ["Go", "Gui"]
---


---

## 1. 개요 

`Fyne`는 사용하기 쉬운 UI 툴킷과 Go로 작성된 앱 API로 데스크탑 및 모바일 환경을 지원합니다.

- [fyne git](https://github.com/fyne-io/fyne)

### 1.1. 사전 설정

Fyne 사용시 필요한 개발환경
- Go 버전 1.14 이상
- C 컴파일러 및 시스템 개발 도구
	- [tdm-gcc download](https://jmeubank.github.io/tdm-gcc/download/)

테스트 시스템 환경
|분류|버전|
|---|---|
|OS|Windows 10|
|go|1.17|
|fyne|2.1.2|

### 1.2. 라이브러리 설치

```
> go get fyne.io/fyne/v2
```


---

## 2. 간단한 사용법

### 2.1. 예제 코드

```go
package main

import (
	"fyne.io/fyne/v2/app"
	"fyne.io/fyne/v2/container"
	"fyne.io/fyne/v2/widget"
)

func main() {
	a := app.New()
	w := a.NewWindow("Hello") // 윈도우 명칭 지정

	hello := widget.NewLabel("Hello World!")
	w.SetContent(
		container.NewVBox( // 컨텐츠 수직 정렬
			hello,
			widget.NewButton(
				"Hi!",                                  // 버튼 명칭
				func() { hello.SetText("Welcome :)") }, // 버튼 이벤트
			),
		),
	)

	w.ShowAndRun() // 윈도우 출력
}
```

실행

```
> go run main.go
```

<br><br>

|버튼 클릭 전|버튼 클릭 후|
|:---:|:---:|
|![](../images/golang-fyne/golang-fyne-1.png?raw=true)|![](../images/golang-fyne/golang-fyne-2.png?raw=true)|

### 2.2. 실행 파일 생성

윈도우에서 실행할 수 있는 `.exe` 파일을 생성한다.

실행 파일 생성이 필요한 라이브러리 설치

``` 
> go get fyne.io/fyne/v2/cmd/fyne
```

임의의 아이콘 파일 `icon.png`을 프로젝트 루트 경로에 생성한다.

실행 파일 생성 명령어

```
> fyne install
```

---

## 3. 레이아웃 예시

### 3.1. dialog

다이얼로그는 팝업 형태의 창으로 정보를 보여주는 기능이다.

#### 3.1.1. Information

정보확인용 다이얼로그

```go
dialog.ShowInformation("title", "information message", window)
```

기본 설정은 영어 버튼이 표시되므로 수정하려면 아래와 같은 방식을 사용한다.

```go
informationDialog := dialog.NewInformation("title", "information message", window)
informationDialog.SetDismissText("확인")
informationDialog.Show()
```

<br><br>

![](../images/golang-fyne/golang-fyne-3.png?raw=true)


#### 3.1.2. Confirm

확인/취소 버튼있는 다이얼로그
- 확인/취소에 따른 콜백 구현이 필요하다.

```go
confirmDialog := dialog.NewConfirm("title", "프로그램을 종료하시겠습니까?", (func(res bool) { fmt.Println(res) }), w)
confirmDialog.SetDismissText("취소")
confirmDialog.SetConfirmText("확인")
confirmDialog.Show()
```

<br><br>

![](../images/golang-fyne/golang-fyne-4.png?raw=true)


#### 3.1.3. Error

에러 출력용 다이얼로그

```go
err := errors.New("error message")
dialog.ShowError(err, w)
```

<br><br>

![](../images/golang-fyne/golang-fyne-5.png?raw=true)

이외에도 다양한 다이얼로그 기능이 있다.


### 3.2. Entry(input) / Button

`input` : 문자열을 입력하는 박스  
`button` : 특정 기능을 동작 시키는 버튼

```go
entry := widget.NewEntry()
entry.SetPlaceHolder("Entry")
```

입력한 문자열은 `entry.Text` 로 사용가능 하다.

button은 아이콘이 없거나 있는 버튼이 있다.

```go
// input에 입력한 값을 label에 표시하는 버튼
button := widget.NewButton("Submit Text Button",
	func() {
		label.SetText(entry.Text)
	})

// input에 입력한 값을 label에 표시하는 아이콘 버튼
iconButton := widget.NewButtonWithIcon("Submit Icon Button", theme.ConfirmIcon(),
	func() {
		label.SetText(entry.Text)
	})
```

<br><br>

|버튼 클릭 전|임의의 값 입력 후 버튼 클릭|
|:---:|:---:|
|![](../images/golang-fyne/golang-fyne-6.png?raw=true)|![](../images/golang-fyne/golang-fyne-7.png?raw=true)|


### 3.3. Check / Radio

`check` : 여러 항목 중 복수개를 선택할 수 있는 체크 버튼  
- 체크 박스 선택에 따른 핸들러 설정이 필요하다.

```go
checkButton := widget.NewCheck("Check Button",
	func(b bool) {
		msg := fmt.Sprintf("check = %v", b)
		label.SetText(msg)
	})
```

`radio` : 여러 항목 중 1개만 선택할 수 있는 라디오 버튼  
- 라디오 버튼 선택에 따른 핸들러 설정이 필요하다.

```go
options := []string{"1", "2", "3"}
radioButton := widget.NewRadioGroup(options,
	func(s string) {
		msg := fmt.Sprintf("radio = %s", s)
		label.SetText(msg)
	})
```

<br><br>

![](../images/golang-fyne/golang-fyne-8.png?raw=true)


### 3.4. ProgressBar

`progressbar` : 진행률, 진행상황, 진행여부 등 표시하는 기능
	- SetValue(0) ~ SetValue(1) 범위로 동작

```go
progress := widget.NewProgressBar()
infProgress := widget.NewProgressBarInfinite()

go func() {
	for i := 0.0; i <= 100; i++ {
		progress.SetValue(i / 100)
		time.Sleep(time.Millisecond * 100)
	}
}()
```

<br><br>

![](../images/golang-fyne/golang-fyne-9.png?raw=true)


---

## 4. 한글 폰트 설정

fyne 라이브러리 사용시 한글을 사용하면 글자가 깨져서 UI로 표시된다.

<br><br>

|폰트 설정 전|폰트 설정 후|
|:---:|:---:|
|![](../images/golang-fyne/golang-fyne-10.png?raw=true)|![](../images/golang-fyne/golang-fyne-11.png?raw=true)|

한글폰트를 지정하지 않아 생긴 문제로 `한글 폰트를 별도로 지정`하면 문제가 해결된다.

- key : `FYNE_FONT`
- value : `(폰트파일 이름).ttf`

```go
func main() {
	// 한글 폰트 지정(실행 파일과 동일경로에 폰트파일 위치)
	os.Setenv("FYNE_FONT", "NanumGothic.ttf")
	...
```

위와 같이 golang에서 환경 변수를 지정한다.


---

## 5. 예시코드 Git

[golang-fyne](https://github.com/SangjunCha-dev/)


---

## 참고(Reference)

- [fyne git](https://github.com/fyne-io/fyne)
- [fyne examples](https://github.com/fyne-io/examples/)
- [Can i change font?](https://github.com/fyne-io/fyne/issues/400)