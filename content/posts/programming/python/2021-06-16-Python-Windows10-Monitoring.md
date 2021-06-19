---
title: "python을 이용한 windows10 시스템 정보 및 사용량 모니터링"
date: 2021-06-16T16:10:28+09:00
description: python을 이용한 windows10 시스템 정보 및 사용량 모니터링
# menu:
#   sidebar:
#     name: windows10 시스템 모니터링
#     identifier: python-windows10-system-monitoring
#     parent: python
#     weight: 30
tags: ["python", "windows"]
categories: ["Python", "Windows"]
---


---

운영체제 : windows 10  
설치환경 : python 3.8.8, powershell 7.1  


---

## 1. WMI

WMI(Windows Management Instrumentation)는 Windows 시스템 관리를 위한 다양한 정보를 일관되게 표시하는 기술이다.
WMI가 표시하는 정보의 양이 제한되어 있기 때문에 WMI 개체에 액세스하기 위한 PowerShell cmdlet인 `Get-CimInstance` 개체 가져오기 도구를 사용한다.

해당 WMI 개체 정보 조회

```shell
Get-CimInstance -ClassName (WMI 개체이름) | select *
```

|WMI 개체이름|정보|
|---|---|
|Win32_OperatingSystem | 운영체제 및 메모리 정보|
|Win32_Processor       | CPU 정보|
|Win32_PhysicalMemory  | 물리 메모리 정보|
|Win32_DiskDrive       | 물리 디스크 정보|
|Win32_LogicalDisk     | 논리 디스크 정보|
|Win32_VideoController | 그래픽카드 정보|

Get-CimInstance 옵션 매개변수로 `Select-Object`사용하면 WMI 클래스 인스턴스에서 반환되는 속성을 선택할 수 있다. Get-CimInstance의 `-Property` 매개 변수는 PowerShell로 반환되는 개체가 아니라 WMI 클래스 인스턴스에서 반환되는 속성을 제한하기 때문에 추가 데이터가 반환된다.

```shell
Get-CimInstance -ClassName Win32_OperatingSystem | Select-Object -Property Caption
```

WMI 클래스 인스턴스에서 반환되는 모든 옵션 조회

```shell
Get-CimInstance -ClassName Win32_OperatingSystem | Select-Object -Property *
```


---

## 2. python에서 powershell 명령 실행 방법

### 2.1 콘솔 명령 실행

```python
import subprocess
import json
cmd = "Get-CimInstance -Class Win32_OperatingSystem"
subprocess.Popen(f'powershell.exe {cmd}')
```

### 2.2. 실행결과 반환

디코딩을 하지 않으면 바이트 문자열로 나열되서 반환된다.

```python
cmd = "Get-CimInstance -Class Win32_OperatingSystem"
result = subprocess.Popen(f'powershell.exe {cmd}', stdout=subprocess.PIPE)
print(result)

result = result.stdout.read()
print(result)

```

결과

```shell
<subprocess.Popen object at 0x0000025175617B50>

b'\r\nSystemDirectory     Organization BuildNumber RegisteredUser SerialNumber            Version   \r\n---------------     ------------ ----------- -------------- ------------            -------   
\r\nC:\\Windows\\system32              19000       UserName         00000-00000 10.0\r\n\r\n\r\n'
```

### 2.3.실행결과 디코딩

```python
cmd = "Get-CimInstance -Class Win32_OperatingSystem"
result = subprocess.Popen(f'powershell.exe {cmd}', stdout=subprocess.PIPE)
result = result.stdout.read().decode('cp949')

print(result)
```

결과

```shell
SystemDirectory     Organization BuildNumber RegisteredUser SerialNumber Version
---------------     ------------ ----------- -------------- ------------ -------
C:\Windows\system32              19000       UserName       00000-00000  10.0
```

### 2.4. json 형식의 문자열 형태로 반환

```python
cmd = "Get-CimInstance -Class Win32_OperatingSystem | Select-Object -Property Caption, OSArchitecture, Version"
result = subprocess.Popen(f'powershell.exe {cmd} | ConvertTo-JSON', stdout=subprocess.PIPE)
result = result.stdout.read().decode('cp949')

print(result)
print(type(result))
```

결과

```shell
{
    "Caption":  "Microsoft Windows 10 Pro",
    "OSArchitecture":  "64비트",
    "Version":  "10.0"
}
<class 'str'>
```

### 2.5. dict 형태로 반환

```python
cmd = "Get-CimInstance -Class Win32_OperatingSystem | Select-Object -Property Caption, OSArchitecture, Version"
result = subprocess.Popen(f'powershell.exe {cmd} | ConvertTo-JSON', stdout=subprocess.PIPE)
result = ast.literal_eval(result.stdout.read().decode('cp949'))

print(result)
print(type(result))
```

결과

```shell
{'Caption': 'Microsoft Windows 10 Pro', 'OSArchitecture': '64비트', 'Version': '10.0.19042'}
<class 'dict'>
```

### 2.6. int형으로 반환
```python
cmd = "(Get-CimInstance -ClassName Win32_Processor).LoadPercentage"
result = subprocess.Popen(f'powershell.exe {cmd}', stdout=subprocess.PIPE)
result = result.stdout.read().decode('cp949').strip()
result = int(result)

# 공백 or 에러를 반환하는 경우 예외처리
if result.isnumeric(): 
    result = int(result)
else:
    result = None

print(result)
print(type(result))
```

결과

```shell
23
<class 'int'>
```


---

## 3. power shell 명령어 예시

### 3.1. 운영체제 및 메모리 사용량 정보 조회

```
Get-CimInstance -Class Win32_OperatingSystem | Select-Object -Property Caption, OSArchitecture, Version, TotalVisibleMemorySize, FreePhysicalMemory
```

### 3.2. CPU 정보 및 사용량 조회

```
Get-CimInstance -ClassName Win32_Processor | Select-Object -Property Name, MaxClockSpeed, LoadPercentage
```

### 3.2.1. CPU 사용량 조회

```
(Get-CimInstance -ClassName Win32_Processor).LoadPercentage
```

### 3.3. GPU 정보 조회

- AdapterRAM 필드의 자료형이 dword(uint32)형으로 최대 4GB 까지 표현한다.

```
Get-CimInstance -ClassName  Win32_VideoController | Select-Object -Property Name, AdapterRAM
```

- qwMemorySize 필드 자료형은 qword(uint64)형으로 최대 약 16 EB(엑사바이트) 까지 표현한다.

```
(Get-ItemProperty -Path "HKLM:\SYSTEM\ControlSet001\Control\Class\{4d36e968-e325-11ce-bfc1-08002be10318}\0*" -Name HardwareInformation.qwMemorySize -ErrorAction SilentlyContinue)."HardwareInformation.qwMemorySize"
```

### 3.3.1. GPU 메모리 사용량 조회

```
(((Get-Counter '\GPU Process Memory(*)\Local Usage').CounterSamples | where CookedValue).CookedValue | measure -sum).sum
```

### 3.4. 메모리 정보 조회

```
Get-CimInstance -ClassName Win32_PhysicalMemory | Select-Object -Property Manufacturer, PartNumber, Speed, Capacity
```

### 3.5. 디스크 정보 조회

```
Get-CimInstance -ClassName Win32_DiskDrive | Select-Object -Property Index, Model, Size
```

### 3.6. 볼륨 정보 조회

- DriveType 3 (WMI에서 고정 하드 디스크에 사용하는 값)

```
Get-CimInstance -ClassName Win32_LogicalDisk -Filter 'DriveType=3' | Select-Object -Property Name, FileSystem, Size, FreeSpace
```


---

## 4. 예시코드 Git

[monitoring_windows](https://github.com/SangjunCha-dev/monitoring_windows)


---

## 참고(Reference)

- [컴퓨터에 대한 정보 수집](https://docs.microsoft.com/ko-kr/powershell/scripting/samples/collecting-information-about-computers?view=powershell-7.1)
- [WMI 개체 가져오기(Get-CimInstance)](https://docs.microsoft.com/ko-kr/powershell/scripting/samples/getting-wmi-objects--get-ciminstance-?view=powershell-7.1)
    - powershell 명령어 사용법
- [개체의 일부 선택(Select-Object)](https://docs.microsoft.com/ko-kr/powershell/scripting/samples/selecting-parts-of-objects--select-object-?view=powershell-7.1)