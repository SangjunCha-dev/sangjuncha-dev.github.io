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

## 2. 콜솔 명령 실행결과 반환

```python
cmd = "Get-CimInstance -Class Win32_OperatingSystem"
result = subprocess.Popen(f'powershell.exe {cmd}', stdout=subprocess.PIPE)
print(result)
```

결과

```shell
<subprocess.Popen object at 0x0000025175617B50>
```

## 2. 콜솔 명령 실행결과 반환

디코딩을 하지 않으면 바이트 문자열로 나열되서 반환된다.

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

## 3. 콜솔 명령 실행결과 json 형태로 반환

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

## 4. 문자열 형태로 자료형 변환

```python
cmd = "Get-CimInstance -Class Win32_OperatingSystem | Select-Object -Property Caption, OSArchitecture, Version"
result = subprocess.Popen(f'powershell.exe {cmd} | ConvertTo-JSON', stdout=subprocess.PIPE)
result = ast.literal_eval(result.stdout.read().decode('cp949'))
print(result)
print(type(result))
```

출력 결과

```shell
{'Caption': 'Microsoft Windows 10 Pro', 'OSArchitecture': '64비트', 'Version': '10.0.19042'}
<class 'dict'>
```


# 예제 코드

```python
import subprocess   # 시스템 명령 실행
import ast          # 자료형 변환

class PowerShell:
    '''
    powershell 명령어 실행
    '''
    @staticmethod
    def literal_eval(cmd: str):
        result = subprocess.Popen(f'powershell.exe {cmd} | ConvertTo-JSON', stdout=subprocess.PIPE)
        result = ast.literal_eval(result.stdout.read().decode('cp949'))

        return result
    
    @staticmethod
    def isnumeric(cmd: str):
        result = subprocess.Popen(f'powershell.exe {cmd}', stdout=subprocess.PIPE)
        result = result.stdout.read().decode('cp949').strip()

        # 공백 or 에러를 반환하는 경우
        result = int(result) if result.isnumeric() else None

        return result

def system_info(self):
    '''
    시스템 정보 조회
    '''
    # 운영체제
    ps_cmd = 'Get-CimInstance -Class Win32_OperatingSystem | Select-Object -Property Caption, OSArchitecture, Version, TotalVisibleMemorySize, FreePhysicalMemory'
    os_info = PowerShell.literal_eval(cmd=ps_cmd)

    print(f"운영체제 및 메모리 사용량 정보 조회\n {os_info}")

    # CPU
    ps_cmd = 'Get-CimInstance -ClassName Win32_Processor | Select-Object -Property Name, MaxClockSpeed, LoadPercentage'
    cpu_info = PowerShell.literal_eval(cmd=ps_cmd)
    
    print(f"CPU 정보 조회\n {cpu_info}")

    # GPU
    ps_cmd = 'Get-CimInstance -ClassName  Win32_VideoController | Select-Object -Property Name, AdapterRAM'
    gpu_info = PowerShell.literal_eval(cmd=ps_cmd)

    print(f"GPU 정보 조회\n {gpu_info}")

    ps_cmd = "(((Get-Counter '\GPU Process Memory(*)\Local Usage').CounterSamples | where CookedValue).CookedValue | measure -sum).sum"
    gpu_use_memory = PowerShell.isnumeric(cmd=ps_cmd)

    print(f"GPU 메모리 사용량 조회\n {gpu_use_memory}")

    # 메모리
    ps_cmd = 'Get-CimInstance -ClassName Win32_PhysicalMemory | Select-Object -Property Manufacturer, PartNumber, Speed, Capacity'
    memory_info = PowerShell.literal_eval(cmd=ps_cmd)

    print(f"메모리 정보 조회\n {memory_info}")

    # 디스크
    ps_cmd = 'Get-CimInstance -ClassName Win32_DiskDrive | Select-Object -Property Index, Model, Size'
    disk_info = PowerShell.literal_eval(cmd=ps_cmd)

    print(f"디스크 정보 조회\n {memory_info}")

    # 볼륨
    ps_cmd = "Get-CimInstance -ClassName Win32_LogicalDisk -Filter 'DriveType=3' | Select-Object -Property Name, FileSystem, Size, FreeSpace"
    # DriveType 3 (WMI에서 고정 하드 디스크에 사용하는 값)
    volume_info = PowerShell.literal_eval(cmd=ps_cmd)

    print(f"볼륨 정보 조회\n {memory_info}")


def system_monitoring(self):
    '''
    시스템 사용량 모니터링
    '''
    # CPU
    ps_cmd = "(Get-CimInstance -ClassName Win32_Processor).LoadPercentage"
    cpu_use_percent = PowerShell.isnumeric(cmd=ps_cmd)

    print(f"CPU 사용량 조회\n {cpu_use_percent}")

    # GPU
    ps_cmd = "(((Get-Counter '\GPU Process Memory(*)\Local Usage').CounterSamples | where CookedValue).CookedValue | measure -sum).sum"
    gpu_use_memory = PowerShell.isnumeric(cmd=ps_cmd)
    
    print(f"GPU 메모리 사용량 조회\n {gpu_use_memory}")

    # 메모리
    ps_cmd = "(Get-CimInstance -Class Win32_OperatingSystem).FreePhysicalMemory"
    memory_free_size = PowerShell.isnumeric(cmd=ps_cmd)
    
    print(f"메모리 사용량 조회\n {memory_free_size}")

    # 볼륨
    ps_cmd = "Get-CimInstance -ClassName Win32_LogicalDisk -Filter 'DriveType=3' | Select-Object -Property Name, FreeSpace"
    volume_info = PowerShell.literal_eval(cmd=ps_cmd)

    print(f"볼륨 사용량 조회\n {volume_info}")


if __name__ == '__main__':
    system_info()
    system_monitoring()
```
