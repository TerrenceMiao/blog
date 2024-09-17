---
title: Configure and setup Mellanox network adapter
date: 2024-09-17 11:42:01
tags:
---

A _Mellanox ConnectX-4 MCX455A-ECAT PCIe x16 3.0 100GBe VPI EDR IB_  network adapter, configure and setup.

```
root@aorus:# lspci
...
06:00.0 Ethernet controller: Mellanox Technologies MT27700 Family [ConnectX-4]
...
```

Go to NVIDIA Firmware Tools (MFT) _https://network.nvidia.com/products/adapter-software/firmware-tools/_ and download the `MFT`; Updating Firmware for ConnectX®-4 VPI PCI Express Adapter Cards (InfiniBand, Ethernet, VPI) _https://network.nvidia.com/support/firmware/connectx4ib/_ and download the updated firmware e.g. `mft-4.26.1-6-x86_64-deb.tgz`.

Start up **Mellanox Software Tools** service:

```
root@aorus:# mst start
Starting MST (Mellanox Software Tools) driver set
Loading MST PCI module - Success
Loading MST PCI configuration module - Success
Create devices
Unloading MST PCI module (unused) - Success
```

Check status:

```
root@aorus:# mst status
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

Query:

```
root@aorus:# flint -d /dev/mst/mt4115_pciconf0 query
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

Check LINK type (Ethernet):

```
root@aorus:# mlxconfig -d /dev/mst/mt4115_pciconf0 query | grep LINK_TYPE
        LINK_TYPE_P1                                ETH(2)
```

Verify network adapter configuration:

```
terrence@aorus:~$ ethtool enp6s0np0
Settings for enp6s0np0:
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





References
----------

- Mellanox ConnectX-4 100Gb NIC Firmware Update and Ethernet Mode, _https://www.youtube.com/watch?v=D1qN7Qg3bSg_
- Single Root IO Virtualization (SR-IOV), _https://docs.nvidia.com/networking/display/mlnxofedv53100143/single+root+io+virtualization+(sr-iov)_
