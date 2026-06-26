---
title: MediaTek MT7925 Bluetooth stops working / turning on on Ubuntu 26.04 LTS
date: 2026-06-22 20:32:18
tags:
---

`MediaTek MT7925` is an M.2 Wi-Fi 7 and Bluetooth 5.4 wireless network card which Wireless and BlueTooth are both supported by the latest Linux Kernel.

```
$ uname -r
7.0.0-22-generic

$ lspci -k | grep -i -A3 network
01:00.0 Network controller: MEDIATEK Corp. MT7925 802.11be 160MHz 2x2 PCIe Wireless Network Adapter [Filogic 360]
	Subsystem: MEDIATEK Corp. MT7925 802.11be 160MHz 2x2 PCIe Wireless Network Adapter [Filogic 360]
	Kernel driver in use: mt7925e
	Kernel modules: mt7925e
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller (rev 0c)

$ lsusb | grep -i mediatek
Bus 003 Device 002: ID 0e8d:7925 MediaTek Inc. Wireless_Device

$ rfkill list
0: hci0: Bluetooth
	Soft blocked: no
	Hard blocked: no
1: phy0: Wireless LAN
	Soft blocked: no
	Hard blocked: no

$ ls -al /lib/firmware/mediatek/mt7925/
...
-rw-r--r--  1 root root  459528 Mar 19 23:32 BT_RAM_CODE_MT7925_1_1_hdr.bin.zst
-rw-r--r--  1 root root  197811 Mar 19 23:32 WIFI_MT7925_PATCH_MCU_1_1_hdr.bin.zst
-rw-r--r--  1 root root 1152702 Mar 19 23:32 WIFI_RAM_CODE_MT7925_1_1.bin.zst
```

`MT7925` BlueTooth is usually exposed via USB (even though WiFi is PCIe).

```
$ dmesg | grep -i -E "bluetooth|mt7925|btmtk|firmware"
...
Bluetooth: hci0: Failed to get device id (-108) [   11.847463]
...
```

**Failed to get device id (-108)** is an ENOTUNIQ error from the USB/MMIO ID lookup in **btusb/btmtk**, often a known issue with `MT7925` where the BlueTooth function doesn't enumerate correctly or there's a race during probe. This is a fairly common bug on **MT7925 / 7925e** cards on newer kernels.

The **-108** error sometimes is caused by the BT USB device not being reset properly at boot. Try a full module reload in correct order:

```
$ sudo systemctl stop bluetooth

$ sudo lsof /dev/rfkill 2>/dev/null

$ sudo modprobe -r btusb
$ sudo modprobe -r btmtk

$ sudo modprobe btmtk
$ sudo modprobe btusb

$ sudo systemctl start bluetooth
```
