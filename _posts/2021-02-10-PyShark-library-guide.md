---
title: pyshark 라이브러리 사용법 (Scapy Library Guide)
author: Sangjun Cha
date: 2021-02-10 11:13:23 +0900
categories: [Python, Guide, Sniff]
tags: [python, library, Sniff]
pin: false
---



Docs URL - https://github.com/KimiNewt/pyshark/

설치환경 : Windows 10

# 1. 설치

1. 와이어샤크 프로그램에 포함된 `npcap` 설치 필요
    - https://www.wireshark.org/download.html
2. `pip install pyshark`

# 2. 사용법

캡쳐파일 읽기
- capture = pyshark.FileCapture('./pcap/first_box1.pcapng')

패킷 1개만 캡처하거나 10ms 경과하면 캡처 결과 반환
- capture.sniff(packet_count=1, timeout=10)
패킷 10개만 캡처하거나 10ms 경과하면 캡처 결과 반환
- capture.sniff(packet_count=10, timeout=10)

패킷 bytes 형태로 반환
- print(capture[0].get_raw_packet())

모든 패킷을 실행하고 읽은대로 각 패킷과 함께 주어진 콜백(함수) 호출
```python
def print_callback(self, pkt):
    print(f"packet = {pkt}")
print(capture.apply_on_packets(print_callback(pkt)))
```

# 3. bpf_filter

패킷 캡쳐 조건 지정

bpf_filter 사용 참고사이트 
- https://docs.extrahop.com/8.3/bpf-syntax/
- https://biot.com/capstats/bpf.html

사용 예제

- bpf_filter='ether host 11:22:33:44:55:66'
- bpf_filter='ip src 192.168.0.14'
- bpf_filter='len == 1178'

# 4. display_filter(wireshark) 필터

display_filter='eth.addr == 11:22:33:44:55:66'

# 4. 예제

```python
import pyshark
import struct

import os
import datetime as dt


class SniffPacket():
    def __init__(self):
        pass

    def run(self):
        # 실시간 패킷캡처 interface
        capture = pyshark.LiveCapture(interface='이더넷', bpf_filter='ether src host 11:22:33:44:55:66', use_json=True, include_raw=True)

        cnt = 1
        start_time = dt.datetime.now()
        print('capture start')
        for raw_data in capture.sniff_continuously():
            # capture.sniff_continuously() : 설정된 인터페이스에서 캡처하여 패킷을 지속적으로 반환하는 생성기를 반환
            # pyshark.LiveCapture(..., use_json=True, include_raw=True)  # 위의 기능을 사용하기 위해서는 Capture 설정에서 use_json=True, include_raw=True 옵션을 추가해야함
            
            if cnt == 1: start_time = dt.datetime.now()

            self.analysis_data(raw_data.get_raw_packet())
            
            cnt += 1
            if cnt == 8291: 
                print(f"cnt {cnt}")
                print(dt.datetime.now() - start_time)
                cnt = 1

    def print_callback(self, pkt):
        print(f"packet = {pkt}")

    def analysis_data(self, raw_data):
        src_mac = struct.unpack('!6B', raw_data[6:12])
        src_mac = '%02x:%02x:%02x:%02x:%02x:%02x' % src_mac

        if src_mac == '11:22:33:44:55:66':
            dst_mac = struct.unpack('!6B', raw_data[:6])
            dst_mac = '%02x:%02x:%02x:%02x:%02x:%02x' % dst_mac

            (len1,) = struct.unpack('!H', raw_data[12:14])

            (VH,) = struct.unpack('!B', raw_data[14:15])
            (HL,) = struct.unpack('!B', raw_data[15:16])

            (frame_num,) = struct.unpack('!L', raw_data[16:20])

            (len2,) = struct.unpack('!H', raw_data[20:22])

            data = raw_data[22:]
            
sniffPacket = SniffPacket()
sniffPacket.run()
```