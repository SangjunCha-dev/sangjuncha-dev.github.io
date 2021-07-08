---
title: python을 이용한 windows10 시스템 정보 및 사용량 모니터링
author: Sangjun Cha
date: 2021-06-16 16:10:28 +0900
categories: [Python, Windows]
tags: [python, windows]
pin: false
---

운영체제 : windows 10
설치환경 : python 3.8.8, powershell 7.1

# powershell 명령어 사용법

- [WMI 개체 가져오기(Get-CimInstance)](https://docs.microsoft.com/ko-kr/powershell/scripting/samples/getting-wmi-objects--get-ciminstance-?view=powershell-7.1)

# python에서 powershell 명령 실행 방법

## 1. 콘솔 명령 실행

```python
cmd = "Get-CimInstance -Class Win32_OperatingSystem"
subprocess.Popen(f'powershell.exe {cmd}')
```

## 2. 실행결과 반환

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

## 2.실행결과 디코딩

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

## 3. json 형식의 문자열 형태로 반환

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

## 4. dict 형태로 반환

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

## 5. int형으로 반환
```python
cmd = "(Get-CimInstance -ClassName Win32_Processor).LoadPercentage"
result = subprocess.Popen(f'powershell.exe {cmd}', stdout=subprocess.PIPE)
result = result.stdout.read().decode('cp949').strip()
result = int(result)

print(result)
print(type(result))
```

결과

```shell
23
<class 'int'>
```


# power shell 명령어 예시

* 운영체제 및 메모리 사용량 정보 조회

```powershell
Get-CimInstance -Class Win32_OperatingSystem | Select-Object -Property Caption, OSArchitecture, Version, TotalVisibleMemorySize, FreePhysicalMemory
```

* CPU 정보 및 사용량 조회

```powershell
Get-CimInstance -ClassName Win32_Processor | Select-Object -Property Name, MaxClockSpeed, LoadPercentage
```

* CPU 사용량 조회

```powershell
(Get-CimInstance -ClassName Win32_Processor).LoadPercentage
```

* GPU 정보 조회

AdapterRAM 필드의 자료형이 dword(uint32)형으로 최대 4GB 까지 표현한다.

```powershell
Get-CimInstance -ClassName  Win32_VideoController | Select-Object -Property Name, AdapterRAM
```

qwMemorySize 필드 자료형은 qword(uint64)형으로 최대 약 17,175,674,880 GB 까지 표현한다.

```powershell
(Get-ItemProperty -Path "HKLM:\SYSTEM\ControlSet001\Control\Class\{4d36e968-e325-11ce-bfc1-08002be10318}\0*" -Name HardwareInformation.qwMemorySize -ErrorAction SilentlyContinue)."HardwareInformation.qwMemorySize"
```

* GPU 메모리 사용량 조회

```powershell
(((Get-Counter '\GPU Process Memory(*)\Local Usage').CounterSamples | where CookedValue).CookedValue | measure -sum).sum
```

* 메모리 정보 조회

```powershell
Get-CimInstance -ClassName Win32_PhysicalMemory | Select-Object -Property Manufacturer, PartNumber, Speed, Capacity
```

* 디스크 정보 조회

```powershell
Get-CimInstance -ClassName Win32_DiskDrive | Select-Object -Property Index, Model, Size
```

* 볼륨 정보 조회

- DriveType 3 (WMI에서 고정 하드 디스크에 사용하는 값)

```powershell
Get-CimInstance -ClassName Win32_LogicalDisk -Filter 'DriveType=3' | Select-Object -Property Name, FileSystem, Size, FreeSpace
```
