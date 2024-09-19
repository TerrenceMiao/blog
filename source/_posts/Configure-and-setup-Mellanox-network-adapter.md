---
title: Configure and setup Mellanox network adapter
date: 2024-09-17 11:42:01
tags:
---

A _Mellanox ConnectX-4 MCX455A-ECAT PCIe x16 3.0 100GBe VPI EDR IB_ network adapter. Enable `VT-d`(Intel Virtualization Technology for Directed I/O), `SR-IOV` (Single Root IO Virtualization), and the number of Virtual Functions in Chipset and Network in BIOS.

Configuration and Setup
----------------------

Go to NVIDIA Firmware Tools (MFT) _https://network.nvidia.com/products/adapter-software/firmware-tools/_ and download the `MFT`; go to Updating Firmware for ConnectX®-4 VPI PCI Express Adapter Cards (InfiniBand, Ethernet, VPI) _https://network.nvidia.com/support/firmware/connectx4ib/_ and download the updated firmware e.g. `mft-4.26.1-6-x86_64-deb.tgz`.

After installation, start up **Mellanox Software Tools** service:

```
root@pve:~# mst start
Starting MST (Mellanox Software Tools) driver set
Loading MST PCI module - Success
Loading MST PCI configuration module - Success
Create devices
Unloading MST PCI module (unused) - Success
```

Check status:

```
root@pve:~# mst status
MST modules:
------------
    MST PCI module is not loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4115_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:06:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
```

Query Mellanox network adapter:

```
root@pve:~# flint -d /dev/mst/mt4115_pciconf0 query
Image type:            FS3
FW Version:            12.28.2006
FW Release Date:       15.9.2020
Product Version:       12.28.2006
Rom Info:              type=UEFI version=14.21.17 cpu=AMD64
                       type=PXE version=3.6.102 cpu=AMD64
Description:           UID                GuidsNumber
Base GUID:             ec0d9a030076eae2        4
Base MAC:              ec0d9a76eae2            4
Image VSD:             N/A
Device VSD:            N/A
PSID:                  LNV2180110032
Security Attributes:   N/A
```

Check Mellanox network adapter configuration:

```
root@pve:~# mlxconfig -d /dev/mst/mt4115_pciconf0 query
Device #1:
----------

Device type:        ConnectX4
Name:               00KH925_Ax
Description:        Mellanox ConnectX-4 EDR IB VPI Single-port x16 PCIe 3.0 HCA
Device:             /dev/mst/mt4115_pciconf0

Configurations:
        LINK_TYPE_P1                                ETH(2)
        SRIOV_EN                                    True(1)
        NUM_OF_VFS                                  8
...
```

Verify network adapter configuration:

```
terrence@pve:~# ethtool enp1s0np0
Settings for enp1s0np0:
	Supported ports: [ Backplane ]
	Supported link modes:   1000baseKX/Full
	                        10000baseKR/Full
	                        40000baseKR4/Full
	                        40000baseCR4/Full
	                        40000baseSR4/Full
	                        40000baseLR4/Full
	                        56000baseKR4/Full
	                        25000baseCR/Full
	                        25000baseKR/Full
	                        25000baseSR/Full
	                        50000baseCR2/Full
	                        50000baseKR2/Full
	                        100000baseKR4/Full
	                        100000baseSR4/Full
	                        100000baseCR4/Full
	                        100000baseLR4_ER4/Full
	Supported pause frame use: Symmetric
	Supports auto-negotiation: Yes
	Supported FEC modes: None	 RS	 BASER
	Advertised link modes:  1000baseKX/Full
	                        10000baseKR/Full
	                        40000baseKR4/Full
	                        40000baseCR4/Full
	                        40000baseSR4/Full
	                        40000baseLR4/Full
	                        56000baseKR4/Full
	                        25000baseCR/Full
	                        25000baseKR/Full
	                        25000baseSR/Full
	                        50000baseCR2/Full
	                        50000baseKR2/Full
	                        100000baseKR4/Full
	                        100000baseSR4/Full
	                        100000baseCR4/Full
	                        100000baseLR4_ER4/Full
	Advertised pause frame use: Symmetric
	Advertised auto-negotiation: Yes
	Advertised FEC modes: RS
	Speed: 100000Mb/s
	Duplex: Full
	Auto-negotiation: on
	Port: Direct Attach Copper
	PHYAD: 0
	Transceiver: internal
	Link detected: yes
```

Virtualization
--------------

```
root@pve:~# lspci | grep Mellanox
06:00.0 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4]

root@pve:~# ip link show
...
4: enp1s0np0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr2 state UP mode DEFAULT group default qlen 1000
    link/ether ec:0d:9a:76:eb:2a brd ff:ff:ff:ff:ff:ff
...
```

Write to the sysfs file the number of Virtual Functions:

```
root@pve:~# echo 8 > /sys/class/infiniband/mlx5_0/device/sriov_numvfs
```

Verify that the Virtual Functions were created:

```
root@pve:~# lspci | grep Mellanox
01:00.0 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4]
01:00.1 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]
01:00.2 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]
01:00.3 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]
01:00.4 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]
01:00.5 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]
01:00.6 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]
01:00.7 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]
01:01.0 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4 Virtual Function]

root@pve:~# ip link show
...
4: enp1s0np0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr2 state UP mode DEFAULT group default qlen 1000
    link/ether ec:0d:9a:76:eb:2a brd ff:ff:ff:ff:ff:ff
    vf 0     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
    vf 1     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
    vf 2     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
    vf 3     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
    vf 4     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
    vf 5     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
    vf 6     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
    vf 7     link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff, spoof checking off, link-state auto, trust off, query_rss off
...
```

Persistence
-----------

Print out device information:

```
root@pve:~# udevadm info /sys/class/infiniband/mlx5_0
P: /devices/pci0000:00/0000:00:01.0/0000:01:00.0/infiniband/mlx5_0
M: mlx5_0
R: 0
U: infiniband
E: DEVPATH=/devices/pci0000:00/0000:00:01.0/0000:01:00.0/infiniband/mlx5_0
E: SUBSYSTEM=infiniband
E: NAME=mlx5_0
```

Make the network adapter Virtual Functions persistent after reboot:

```
root@pve:~# cat /etc/udev/rules.d/mlx.rules
ACTION=="add", SUBSYSTEM=="infiniband", ENV{NAME}=="mlx5_0", ATTR{device/sriov_numvfs}="8"
```

References
----------

- Mellanox ConnectX-4 100Gb NIC Firmware Update and Ethernet Mode, _https://www.youtube.com/watch?v=D1qN7Qg3bSg_
- Single Root IO Virtualization (SR-IOV), _https://docs.nvidia.com/networking/display/mlnxofedv53100143/single+root+io+virtualization+(sr-iov)_
- Persisting SR-IOV virtual functions after reboot, _https://www.reddit.com/r/homelab/comments/hf12mv/persisting_sriov_virtual_functions_after_reboot/_
- PCI Device Assignment with SR-IOV Devices, _https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-pci_devices-pci_passthrough_
- 100 Gbe & TrueNAS Scale 23.10 iSCSI - Performance Unleashed, _https://forum.level1techs.com/t/100-gbe-truenas-scale-23-10-iscsi-performance-unleashed/206452_
