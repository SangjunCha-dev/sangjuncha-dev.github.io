---
title: golang gopacket 라이브러리를 이용한 통신
author: Sangjun Cha
date: 2021-07-29 17:12:58 +0900
categories: [Go, Gopacket]
tags: [go, gopacket]
pin: false
---

# 설명

ethernet 통신

- 이더넷 어댑터 설명(dev.Description)을 기준으로 통신하는 코드이다.

# 예제 코드


```go
package main

import (
	"fmt"
	"log"
	"net"
	"strings"
	"time"

	"github.com/google/gopacket"
	"github.com/google/gopacket/layers"
	"github.com/google/gopacket/pcap"
)

var (
	device       string = "Realtek PCIe GbE Family Controller"
	eth_name     string
	currentIP    string
	snapshot_len int32 = 1522
	promiscuous  bool  = false
	err          error
	timeout      time.Duration = 30 * time.Second
	handle       *pcap.Handle
	buffer       gopacket.SerializeBuffer
	options      gopacket.SerializeOptions
)

func main() {
	devices, err := pcap.FindAllDevs()
	if err != nil {
		log.Fatalln("pcap.FindAllDevs Error :", err)
	}
	for _, dev := range devices {
		if device == dev.Description {
			device = dev.Name
			currentIP = dev.Addresses[1].IP.String()
			// fmt.Println("dev.Name =", dev.Name)
			// fmt.Println("dev.Description =", dev.Description)
			// fmt.Println("dev.Addresses =", currentIP)
		}
	}

	src_mac := get_mac(currentIP)

	// Open device
	handle, err = pcap.OpenLive(device, snapshot_len, promiscuous, timeout)
	if err != nil {
		log.Fatal("pcap.OpenLive Error :", err)
	}
	defer handle.Close()

	// Send raw bytes over wire
	rawBytes := []byte{0x33, 0x22, 0x11, 0x00}

	ethernetLayer := &layers.Ethernet{
		EthernetType: 0x888E,
		SrcMAC:       src_mac,
		DstMAC:       net.HardwareAddr{0x11, 0x22, 0x33, 0x44, 0x55, 0x66},
	}

	options := gopacket.SerializeOptions{
		ComputeChecksums: true,
		FixLengths:       true,
	}

	// And create the packet with the layers
	buffer = gopacket.NewSerializeBuffer()
	gopacket.SerializeLayers(buffer, options,
		ethernetLayer,
		gopacket.Payload(rawBytes),
	)
	outgoingPacket := buffer.Bytes()

	// Send our packet
	err = handle.WritePacketData(outgoingPacket)
	if err != nil {
		log.Fatal("handle.WritePacketData Error :", err)
	}
	fmt.Println(outgoingPacket)
}

func get_mac(currentIP string) net.HardwareAddr {
	var currentNetworkHardwareName string

	interfaces, _ := net.Interfaces()
	for _, interf := range interfaces {
		if addrs, err := interf.Addrs(); err == nil {
			for _, addr := range addrs {
				// only interested in the name with current IP address
				if strings.Contains(addr.String(), currentIP) {
					currentNetworkHardwareName = interf.Name
					break
				}
			}
		}
	}

	// fmt.Println("currentNetworkHardwareName =", currentNetworkHardwareName)

	// extract the hardware information base on the interface name
	netInterface, err := net.InterfaceByName(currentNetworkHardwareName)
	if err != nil {
		log.Fatal("net.InterfaceByName Error :", err)
	}

	macAddress := netInterface.HardwareAddr

	// fmt.Println("Hardware name : ", netInterface.Name)
	// fmt.Println("MAC address : ", macAddress)

	return macAddress
}
```