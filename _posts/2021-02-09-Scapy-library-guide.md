---
title: scapy 라이브러리 사용법 (Scapy Library Guide)
author: Sangjun Cha
date: 2021-02-09 15:25:35 +0900
categories: [Python, Guide, Sniff]
tags: [python, library, sniff]
pin: false
---



Docs URL - https://scapy.readthedocs.io/en/latest/#

설치환경 : Windows 10

# 1. 설치

1. 와이어샤크 프로그램에 포함된 `npcap` 설치 필요
    - https://www.wireshark.org/download.html
2. `pip install --pre scapy[basic]`

# 2. Client 예제

```python
from scapy.all import *
from scapy.utils import rdpcap

import datetime as dt

# PCAP 파일 읽기
# could be used like this rdpcap("filename",500) fetches first 500 pkts
pkts = rdpcap("./pcap/test.pcapng", -1)

pkts = pkts[21:100]

cnt = 0
repeat = 10
s_time = dt.datetime.now()

for _ in range(repeat):
    for pkt in pkts:
        # print(f"hexdump(pkt) = {hexdump(pkt)}")
        # Send one or more packets at 2 layer
        sendp(pkt, inter=0, loop=0, count=1, iface=None)

        # Send one or more packets at 3 layer
        # send(pkt, inter=0, loop=0, count=1, iface=None)
        cnt += 1
    print(cnt)
    sendp(b'', inter=0, loop=0, count=1, iface=None)
    time.sleep(0.05)

e_time = dt.datetime.now()
print(f"실행시간 : {e_time - s_time}")
```

# 3. Server 예제

- 소량 데이터 스니핑에는 문제없으나 패킷 전송간격 1ms 이내의 스니핑은 TShark 이용하는 pyshark를 추천함

```python
from scapy.all import *
import struct

cnt = 0
while 1:
    # 비동기 Sniffer 실행방식
    # t = AsyncSniffer(prn=lambda x: x.summary(), count=10, store=True, filter='host 11:22:33:44:55:66')
    # t.start()
    # t.join()
    # pkts = t.results

    # 동기 sniff 실행방식
    # filter 사용 참고사이트 (https://docs.extrahop.com/8.3/bpf-syntax/)
    # pkts = sniff(prn=lambda x: x.summary(), count=10, store=True, filter='ether src host 11:22:33:44:55:66')
    pkts = sniff(iface=None, count=800, store=True, filter='ether src host 11:22:33:44:55:66')

    for pkt in pkts:
        # 802.3 11:22:33:44:55:66 > e4:54:e8:51:d0:6f / LLC / Raw
        cnt += 1
        # val = ls(pkt)
        raw_data = bytes(pkt)

        print(f"1. pkt.src = {pkt.src}")  # 11:22:33:44:55:66
        print(f"2. pkt.dst = {pkt.dst}")  # e4:54:e8:51:d0:6f
        print(f"3. pkt.len = {pkt.len}")  # 1164
        print(f"4. len(pkt) = {len(pkt)}")  # 1178
        dst_mac = struct.unpack('!6B', raw_data[:6])
        dst_mac = '%02x:%02x:%02x:%02x:%02x:%02x' % dst_mac
        print(f"6. dst_mac = {dst_mac}")

        src_mac = struct.unpack('!6B', raw_data[6:12])
        src_mac = '%02x:%02x:%02x:%02x:%02x:%02x' % src_mac
        print(f"6. src_mac = {src_mac}")

        (len1,) = struct.unpack('!H', raw_data[12:14])
        print(f"6. len1 = {len1}")

        (VH,) = struct.unpack('!B', raw_data[14:15])
        print(f"6. VH = {VH}")
        (HL,) = struct.unpack('!B', raw_data[15:16])
        print(f"6. HL = {HL}")

        (frame_num,) = struct.unpack('!L', raw_data[16:20])
        print(f"6. frame_num = {frame_num}")

        (len2,) = struct.unpack('!H', raw_data[20:22])
        print(f"6. len2 = {len2}")

        data = raw_data[22:]
        print(f"6. len(data) = {len(data)}")

    print(f"cnt = {cnt}")
```