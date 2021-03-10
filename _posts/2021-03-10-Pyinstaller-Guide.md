---
title: Pyinstaller 사용법 (Pyinstaller Guide)
author: Sangjun Cha
date: 2021-03-10 14:46:19 +0900
categories: [Python, Guide]
tags: [python, library]
pin: false
---



python 파일을 윈도우에서 python 이나 가상환경 설정없이 실행이 가능한 `.exe` 으로 변환해주는 라이브러리

docs URL - https://pyinstaller.readthedocs.io/en/stable/usage.html

설치환경 : Windows 10

# 1. 설치

python 실행이 가능한 가상환경 터미널에서 아래의 명령어 실행

```bash
pip install pyinstaller
```



# 2. 간단한 사용예제

```bash
pyinstaller --clean --distpath . -F -n [프로그램이름] [변환시킬 파일].py
```



# 3. 옵션

|옵션           |설명|
|---                |---|
|*--clean*          |빌드하기 전에 PyInstaller 캐시를 정리하고 임시파일 제거|
|*-F, --onefile*    |단일 실행파일로 생성(실행시 사용한 라이브러리 임시파일 생성됨)|
|*-D, --onedir*     |실행 파일을 포함하는 단일 폴더로 생성|
|*--distpath DIR*   |실행파일 생성 경로(default: ./dist)|
|*-n*               |실행파일 이름 지정|
|*-c, --console, --nowindowed*|console 모드|
|*-w, --windowed, --noconsole*|Not console 모드|
|*--hidden-import*  |import 에러 발생한 라이브러리 명시적 포함옵션 </br> 이 옵션은 여러 번 사용가능|
|*-win-private-assemblies*|응용프로그램에 번들로 제공된 모든 공유 어셈블리가 개인 어셈블리로 변경 </br>즉, 변환한 환경의 어셈블리 버전으로 항상 사용되며 사용자 시스템에 설치된 모든 최신 버전은 시스템 수준에서 무시됨|
|*-runtime-tmpdir PATH*|onefile 모드 에서 라이브러리 및 지원 파일이 압축 해제될 위치 </br> .exe 실행시 생성되는 임시디렉토리 경로지정 (default : C:\Users\{user*name}\AppData\Local\Temp)|
|*--icon=icon.ico*  |실행파일에 포함될 아이콘 지정|
|*-p DIR, --paths DIR*|가져오기를 검색하는 경로 (예: PYTON PATH 사용) </br> 경로가 여러 개 허용되거나 ':'로 구분되거나 이 옵션을 여러 번 사용할 수 있습니다. </br> --paths .\venv\Lib\site-packages|
|-workpath WORKPATH|모든 임시 작업 파일, .log, .pyz 등을 저장할 위치 (기본값 : ./build)|

## 3.1 ^ 줄넘김 옵션

옵션줄이 길어질경우 가독성을 위해 ^ 문자로 구분하여 명령 실행

```bash
pyinstaller --clean ^
	--distpath . ^
	--onedir ^
	-n [프로그램이름] ^
	--win-private-assemblies ^
	--runtime-tmpdir ./temp ^
	--console ^
	--icon=logo.ico ^
	--key [바이트 AES블록암호화 키 16자리 문자열] ^
	run.py
```

## 3.2 실행파일 바이트코드 블록 암호화

필요한 라이브러리 설치

```bash
pip install tinyaes
```

key : AES 블록암호화 키 문자열 16자리 

```bash
pyinstaller --clean ^
	--onedir ^
	-n [프로그램이름] ^
	--win-private-assemblies ^
	--key [암호화키 문자열 16자리] ^
	run.py
```



# 4. 참고사항

## 4.1 python 코드가 아닌 외부 참조 파일사용시 별도로 추가필요

예를 들어 ./config/config.json 파일이 있으면  
.exe 생성후 같은 상대경로에 config.json 파일이 있어야 실행파일이 해당 외부참조 파일사용 가능함    

생성된 실행파일이 있는 디렉토리안에 config 디렉토리에서 사용하는 .json 파일 복사

## 4.2 별도의 외장 라이브러리 사용시 주의사항

문제점 : python 라이브러리에서 조건문을 통한 import 사용시 pyinstaller 에서 인식하지 못함
해결방안 : 조건문 없이도 import 할 수 있도록 라이브러리 코드 수정해야함

해결예시 : tensorpack 라이브러리 디렉토리안에 모든 `__init__.py` 파일에서 if STATICA_HACK 조건문을 통한 import 코드를
조건문 없이 import 하도록 코드 수정

## 4.3 tensorflow 사용시

문제점 : numpy import error 발생

해결방안 : 별도로 numpy import 필요함

```bash
--hidden-import numpy.random.common ^
--hidden-import numpy.random.bounded_integers ^
--hidden-import numpy.random.entropy ^
```

문제점 : tensorflow 1.15버전 이후부터 라이브러리 폴더명이 tensorflow-core 로 수정되어 인식하지 못함

해결방안 : tensorflow 폴더를 인식할 수 있도록 수정한 hooks-contrib 버전 업데이트

`pip install "pyinstaller-hooks-contrib==2020.9"`

## 4.4 아이콘 파일 옵션 적용시 참고사항

문제점 : 아이콘 옵션 적용 후 아이콘이 변경되지 않을경우 windows 시스템 버퍼에 저장된 이미지가 적용되어 안바뀐것 처럼 보임

해결방안 : 정상적으로 적용이 되었으나 버퍼상에 전 이미지가 남아있는것으로 다른 경로로 복사하여 아이콘 이미지 적용이 되었는지 확인
