---
title: Enable SR-IOV in Intel 82599ES 10Gbps Ethernet Controller
date: 2024-11-08 21:06:48
tags:
---

**Intel® 82599ES 10Gbps Ethernet Controller** _https://ark.intel.com/content/www/us/en/ark/products/41282/intel-82599es-10-gigabit-ethernet-controller.html_ supports **SR-IOV**, which is Intel® Virtualization Technology for Connectivity (VT-c) solution.

> **NOTE:** Intel VT Virtualization Features

- **VT-x**, Intel Virtualization Technology for IA-32 and Intel 64 Processors
- **VT-d**, Intel Virtualization Technology for Directed I/O
- **VT-c**, Intel Virtualization Technology for Connectivity

The following step by step instructions is made in `Proxmox`.

Intel i3 N305 Mini PC _https://www.aliexpress.com/item/1005007278560105.html_ 

![Intel Network - Mini PC](/img/Intel%20Network%20-%20Mini%20PC.png "Intel Network - Mini PC")

equips with 2x10G SFP+ (Intel 82599ES) and 2x2.5G (Intel i226-V) network cards:

```
root@sense:~# lspci -v 
...
01:00.0 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
        Flags: bus master, fast devsel, latency 0, IRQ 16, IOMMU group 12
        Memory at 80a20000 (64-bit, non-prefetchable) [size=128K]
        I/O ports at 3020 [disabled] [size=32]
        Memory at 80a44000 (64-bit, non-prefetchable) [size=16K]
        Capabilities: [40] Power Management version 3
        Capabilities: [50] MSI: Enable- Count=1/1 Maskable+ 64bit+
        Capabilities: [70] MSI-X: Enable+ Count=64 Masked-
        Capabilities: [a0] Express Endpoint, MSI 00
        Capabilities: [e0] Vital Product Data
        Capabilities: [100] Advanced Error Reporting
        Capabilities: [140] Device Serial Number a8-b8-e0-ff-ff-05-96-4d
        Capabilities: [150] Alternative Routing-ID Interpretation (ARI)
        Capabilities: [160] Single Root I/O Virtualization (SR-IOV)
        Kernel driver in use: ixgbe
        Kernel modules: ixgbe

01:00.1 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
        Flags: bus master, fast devsel, latency 0, IRQ 17, IOMMU group 13
        Memory at 80a00000 (64-bit, non-prefetchable) [size=128K]
        I/O ports at 3000 [disabled] [size=32]
        Memory at 80a40000 (64-bit, non-prefetchable) [size=16K]
        Capabilities: [40] Power Management version 3
        Capabilities: [50] MSI: Enable- Count=1/1 Maskable+ 64bit+
        Capabilities: [70] MSI-X: Enable+ Count=64 Masked-
        Capabilities: [a0] Express Endpoint, MSI 00
        Capabilities: [e0] Vital Product Data
        Capabilities: [100] Advanced Error Reporting
        Capabilities: [140] Device Serial Number a8-b8-e0-ff-ff-05-96-4d
        Capabilities: [150] Alternative Routing-ID Interpretation (ARI)
        Capabilities: [160] Single Root I/O Virtualization (SR-IOV)
        Kernel driver in use: ixgbe
        Kernel modules: ixgbe

01:10.0 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
        Flags: bus master, fast devsel, latency 0, IOMMU group 18
        Memory at 4017000000 (64-bit, prefetchable) [virtual] [size=16K]
        Memory at 4017100000 (64-bit, prefetchable) [virtual] [size=16K]
        Capabilities: [70] MSI-X: Enable+ Count=3 Masked-
        Capabilities: [a0] Express Endpoint, MSI 00
        Capabilities: [100] Advanced Error Reporting
        Capabilities: [150] Alternative Routing-ID Interpretation (ARI)
        Kernel driver in use: vfio-pci
        Kernel modules: ixgbevf

...

02:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
        Subsystem: Intel Corporation Ethernet Controller I226-V
        Flags: bus master, fast devsel, latency 0, IRQ 18, IOMMU group 14
        Memory at 80600000 (32-bit, non-prefetchable) [size=1M]
        Memory at 80700000 (32-bit, non-prefetchable) [size=16K]
        Capabilities: [40] Power Management version 3
        Capabilities: [50] MSI: Enable- Count=1/1 Maskable+ 64bit+
        Capabilities: [70] MSI-X: Enable+ Count=5 Masked-
        Capabilities: [a0] Express Endpoint, MSI 00
        Capabilities: [100] Advanced Error Reporting
        Capabilities: [140] Device Serial Number a8-b8-e0-ff-ff-05-96-4f
        Capabilities: [1c0] Latency Tolerance Reporting
        Capabilities: [1f0] Precision Time Measurement
        Capabilities: [1e0] L1 PM Substates
        Kernel driver in use: igc
        Kernel modules: igc

03:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
        Subsystem: Intel Corporation Ethernet Controller I226-V
        Flags: bus master, fast devsel, latency 0, IRQ 16, IOMMU group 15
        Memory at 80400000 (32-bit, non-prefetchable) [size=1M]
        Memory at 80500000 (32-bit, non-prefetchable) [size=16K]
        Capabilities: [40] Power Management version 3
        Capabilities: [50] MSI: Enable- Count=1/1 Maskable+ 64bit+
        Capabilities: [70] MSI-X: Enable+ Count=5 Masked-
        Capabilities: [a0] Express Endpoint, MSI 00
        Capabilities: [100] Advanced Error Reporting
        Capabilities: [140] Device Serial Number a8-b8-e0-ff-ff-05-96-50
        Capabilities: [1c0] Latency Tolerance Reporting
        Capabilities: [1f0] Precision Time Measurement
        Capabilities: [1e0] L1 PM Substates
        Kernel driver in use: igc
        Kernel modules: igc

04:00.0 Network controller: Intel Corporation Wi-Fi 7(802.11be) AX1775*/AX1790*/BE20*/BE401/BE1750* 2x2 (rev 1a)
        Subsystem: Intel Corporation Wi-Fi 7(802.11be) AX1775*/AX1790*/BE20*/BE401/BE1750* 2x2 (BE200 320MHz [Gale Peak])
        Flags: bus master, fast devsel, latency 0, IRQ 17, IOMMU group 16
        Memory at 80900000 (64-bit, non-prefetchable) [size=16K]
        Capabilities: [40] Power Management version 3
        Capabilities: [50] MSI: Enable- Count=1/1 Maskable- 64bit+
        Capabilities: [70] Express Endpoint, MSI 00
        Capabilities: [b0] MSI-X: Enable+ Count=32 Masked-
        Capabilities: [100] Advanced Error Reporting
        Capabilities: [148] Secondary PCI Express
        Capabilities: [158] Physical Layer 16.0 GT/s <?>
        Capabilities: [17c] Lane Margining at the Receiver <?>
        Capabilities: [188] Latency Tolerance Reporting
        Capabilities: [190] L1 PM Substates
        Capabilities: [1a0] Vendor Specific Information: ID=0002 Rev=4 Len=100 <?>
        Capabilities: [2a0] Data Link Feature <?>
        Capabilities: [2ac] Precision Time Measurement
        Capabilities: [2b8] Vendor Specific Information: ID=0003 Rev=1 Len=054 <?>
        Capabilities: [500] Vendor Specific Information: ID=0023 Rev=1 Len=010 <?>
        Kernel driver in use: iwlwifi
        Kernel modules: iwlwifi
...

root@sense:~# ip a
...
2: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr2 state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:4f brd ff:ff:ff:ff:ff:ff
3: enp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr3 state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:50 brd ff:ff:ff:ff:ff:ff
4: enp1s0f0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr0 state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:4d brd ff:ff:ff:ff:ff:ff
5: enp1s0f1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq master vmbr1 state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:4e brd ff:ff:ff:ff:ff:ff
7: enp1s0f0v1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 7a:de:19:c7:c2:ce brd ff:ff:ff:ff:ff:ff    
...
22: wlp4s0f0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether e0:8f:4c:b2:58:95 brd ff:ff:ff:ff:ff:ff
```

Verify Intel 82599ES network card status:

```
root@sense:~# ethtool enp1s0f0
Settings for enp1s0f0:
        Supported ports: [ FIBRE ]
        Supported link modes:   10000baseT/Full
        Supported pause frame use: Symmetric
        Supports auto-negotiation: No
        Supported FEC modes: Not reported
        Advertised link modes:  10000baseT/Full
        Advertised pause frame use: Symmetric
        Advertised auto-negotiation: No
        Advertised FEC modes: Not reported
        Speed: 10000Mb/s
        Duplex: Full
        Auto-negotiation: off
        Port: FIBRE
        PHYAD: 0
        Transceiver: internal
        Supports Wake-on: d
        Wake-on: d
        Current message level: 0x00000007 (7)
                               drv probe link
        Link detected: yes

root@sense:~# ethtool -i enp1s0f0
driver: ixgbe
version: 6.8.12-2-pve
firmware-version: 0x800003de
expansion-rom-version: 
bus-info: 0000:01:00.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: yes
supports-register-dump: yes
supports-priv-flags: yes
```

Enable **IOMMU** in `Proxmox`:

```
root@sense:~# cat /etc/default/grub | grep GRUB_CMDLINE_LINUX_DEFAULT
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"
```

IOMMU PT mode improves the performance of other PCIe devices in the system when passthrough is being used.

Update GRUB:

```
root@sense:~# update-grub
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.8.12-2-pve
Found initrd image: /boot/initrd.img-6.8.12-2-pve
Found linux image: /boot/vmlinuz-6.8.4-2-pve
Found initrd image: /boot/initrd.img-6.8.4-2-pve
Found memtest86+ 64bit EFI image: /boot/memtest86+x64.efi
Adding boot menu entry for UEFI Firmware Settings ...
done
```

Reboot `Proxmox` and verify **IOMMU** is enabled:

```
root@sense:~# dmesg | grep -i IOMMU
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.8.12-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on iommu=pt
[    0.053988] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.8.12-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on iommu=pt
[    0.054043] DMAR: IOMMU enabled
[    0.145258] DMAR-IR: IOAPIC id 2 under DRHD base  0xfed91000 IOMMU 1
[    0.403235] pci 0000:00:02.0: DMAR: Skip IOMMU disabling for graphics
[    0.440538] iommu: Default domain type: Translated
[    0.440538] iommu: DMA domain TLB invalidation policy: lazy mode
[    0.483908] DMAR: IOMMU feature fl1gp_support inconsistent
[    0.483909] DMAR: IOMMU feature pgsel_inv inconsistent
[    0.483910] DMAR: IOMMU feature nwfs inconsistent
[    0.483911] DMAR: IOMMU feature dit inconsistent
[    0.483912] DMAR: IOMMU feature sc_support inconsistent
[    0.483912] DMAR: IOMMU feature dev_iotlb_support inconsistent
[    0.493686] pci 0000:00:02.0: Adding to iommu group 0
[    0.493726] pci 0000:00:00.0: Adding to iommu group 1
[    0.493739] pci 0000:00:0d.0: Adding to iommu group 2
[    0.493764] pci 0000:00:14.0: Adding to iommu group 3
[    0.493774] pci 0000:00:14.2: Adding to iommu group 3
[    0.493786] pci 0000:00:16.0: Adding to iommu group 4
[    0.493793] pci 0000:00:17.0: Adding to iommu group 5
[    0.493806] pci 0000:00:1c.0: Adding to iommu group 6
[    0.493820] pci 0000:00:1c.6: Adding to iommu group 7
[    0.493831] pci 0000:00:1d.0: Adding to iommu group 8
[    0.493842] pci 0000:00:1d.1: Adding to iommu group 9
[    0.493859] pci 0000:00:1d.3: Adding to iommu group 10
[    0.493878] pci 0000:00:1f.0: Adding to iommu group 11
[    0.493886] pci 0000:00:1f.3: Adding to iommu group 11
[    0.493895] pci 0000:00:1f.4: Adding to iommu group 11
[    0.493903] pci 0000:00:1f.5: Adding to iommu group 11
[    0.493918] pci 0000:01:00.0: Adding to iommu group 12
[    0.493934] pci 0000:01:00.1: Adding to iommu group 13
[    0.493945] pci 0000:02:00.0: Adding to iommu group 14
[    0.493956] pci 0000:03:00.0: Adding to iommu group 15
[    0.493982] pci 0000:04:00.0: Adding to iommu group 16
[    0.493993] pci 0000:05:00.0: Adding to iommu group 17
[    3.684070] pci 0000:01:10.1: Adding to iommu group 18
[    3.684303] pci 0000:01:10.3: Adding to iommu group 19
[    3.684501] pci 0000:01:10.5: Adding to iommu group 20
[    3.684685] pci 0000:01:10.7: Adding to iommu group 21
[    3.684885] pci 0000:01:11.1: Adding to iommu group 22
[    3.685096] pci 0000:01:11.3: Adding to iommu group 23
[    3.685296] pci 0000:01:11.5: Adding to iommu group 24
[    3.685473] pci 0000:01:11.7: Adding to iommu group 25
[    3.755233] pci 0000:01:10.0: Adding to iommu group 26
[    3.755462] pci 0000:01:10.2: Adding to iommu group 27
[    3.755761] pci 0000:01:10.4: Adding to iommu group 28
[    3.755939] pci 0000:01:10.6: Adding to iommu group 29
[    3.756107] pci 0000:01:11.0: Adding to iommu group 30
[    3.756276] pci 0000:01:11.2: Adding to iommu group 31
[    3.756444] pci 0000:01:11.4: Adding to iommu group 32
[    3.756628] pci 0000:01:11.6: Adding to iommu group 33
```

> **ATTENTION:**  IOMMU Group not order by PCI BUS IDs

This is an **_ISSUE_** CAN"T setup PCI Device Resource Mappings, because of errors, e.g., "Configuration for iommugroup not correct ('21' != ''27)" thrown after `Proxmox` reboot!

Filter out network card **udev** information, e.g., **enp1s0f1**:

```
root@sense:~# udevadm info -e 
...
P: /devices/pci0000:00/0000:00:1c.0/0000:01:00.1/net/enp1s0f1
M: enp1s0f1
R: 1
U: net
I: 5
E: DEVPATH=/devices/pci0000:00/0000:00:1c.0/0000:01:00.1/net/enp1s0f1
E: SUBSYSTEM=net
E: INTERFACE=enp1s0f1
E: IFINDEX=5
E: USEC_INITIALIZED=2364228
E: ID_NET_NAMING_SCHEME=v252
E: ID_NET_NAME_MAC=enxa8b8e005964e
E: ID_OUI_FROM_DATABASE=Changwang Technology inc.
E: ID_NET_NAME_PATH=enp1s0f1
E: ID_BUS=pci
E: ID_VENDOR_ID=0x8086
E: ID_MODEL_ID=0x10fb
E: ID_PCI_CLASS_FROM_DATABASE=Network controller
E: ID_PCI_SUBCLASS_FROM_DATABASE=Ethernet controller
E: ID_VENDOR_FROM_DATABASE=Intel Corporation
E: ID_MODEL_FROM_DATABASE=82599ES 10-Gigabit SFI/SFP+ Network Connection
E: ID_PATH=pci-0000:01:00.1
E: ID_PATH_TAG=pci-0000_01_00_1
E: ID_NET_DRIVER=ixgbe
E: ID_NET_LINK_FILE=/usr/lib/systemd/network/99-default.link
E: ID_NET_NAME=enp1s0f1
E: SYSTEMD_ALIAS=/sys/subsystem/net/devices/enp1s0f1
E: TAGS=:systemd:
E: CURRENT_TAGS=:systemd:

P: /devices/pci0000:00/0000:00:1c.0/0000:01:00.1/net/enp1s0f1/ixgbe-mdio-0000:01:00.1
M: ixgbe-mdio-0000:01:00.1
R: 1
U: mdio_bus
E: DEVPATH=/devices/pci0000:00/0000:00:1c.0/0000:01:00.1/net/enp1s0f1/ixgbe-mdio-0000:01:00.1
E: SUBSYSTEM=mdio_bus
...
```

Important information like: **SUBSYSTEM=net**, **INTERFACE=enp1s0f1**, **ID_NET_DRIVER=ixgbe**, **ID_NET_NAME=enp1s0f1** ...

Assign **8** Virtual Network Cards:

```
root@sense:~# echo 8 > /sys/class/net/enp1s0f0/device/sriov_numvfs 
root@sense:~# echo 8 > /sys/class/net/enp1s0f1/device/sriov_numvfs 
```

Verify network cards again:

```
root@sense:~# lspci | grep Ethernet
01:00.0 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
01:00.1 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
01:10.0 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
...
01:10.7 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
01:11.0 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
...
01:11.7 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
02:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
03:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
```

Persist number of SR_IOV Virtual Functions after `Proxmox` reboot:

```
root@sense:~# cat /etc/udev/rules.d/enp.rules 
ACTION=="add", SUBSYSTEM=="net", ENV{INTERFACE}=="enp1s0f0", ATTR{device/sriov_numvfs}="8"
ACTION=="add", SUBSYSTEM=="net", ENV{INTERFACE}=="enp1s0f1", ATTR{device/sriov_numvfs}="8"
```

Add Virtual Network Adapters into **Resource Mappings** in `Proxmox`:

![Intel Network - Resource Mappings](/img/Intel%20Network%20-%20Resource%20Mappings.png "Intel Network - Resource Mappings")

Add Mapped PCI Devices into Virtual Machine in `Proxmox`:

![Intel Network - Mapped PCI Devices](/img/Intel%20Network%20-%20Mapped%20PCI%20Devices.png "Intel Network - Mapped PCI Devices")

Virtual Network Cards can be set up as Network Cards, with hardware direct access, from a Network Cards pool (shared by other Virtual Machines), when the first Network Card is available.

![Intel Network - Virtual Network Cards](/img/Intel%20Network%20-%20Virtual%20Network%20Cards.png "Intel Network - Virtual Network Cards")

Virtual Machine's `Proxmox` configuration file:

```
root@sense:/etc/pve/qemu-server# cat 101.conf
agent: 1
bios: ovmf
boot: order=scsi0;ide2;net0
cores: 8
cpu: x86-64-v2-AES,flags=+aes
efidisk0: local-lvm:vm-101-disk-0,efitype=4m,size=4M
hostpci0: mapping=enp0_vf
hostpci1: mapping=enp1_vf
ide2: local:iso/OPNsense-24.7-dvd-amd64.iso,media=cdrom,size=2131548K
machine: q35
memory: 8192
meta: creation-qemu=9.0.2,ctime=1731054484
name: LINKsense
net0: virtio=BC:24:11:0C:52:4B,bridge=vmbr2,firewall=1,queues=4
net1: virtio=BC:24:11:21:3C:E0,bridge=vmbr3,firewall=1,queues=4
numa: 0
ostype: l26
scsi0: local-lvm:vm-101-disk-1,discard=on,iothread=1,size=128G,ssd=1
scsihw: virtio-scsi-single
smbios1: uuid=3e913d90-c284-47d7-881a-599013fa21a4
sockets: 1
vmgenid: aab228d8-bccc-46c1-b627-927271ad20b6
```


References
----------

- Overview of the Intel VT Virtualization Features, _https://www.thomas-krenn.com/en/wiki/Overview_of_the_Intel_VT_Virtualization_Features_
- How to Pass-through PCIe NICs with Proxmox VE on Intel and AMD, _https://www.servethehome.com/how-to-pass-through-pcie-nics-with-proxmox-ve-on-intel-and-amd/_
- Persisting SR-IOV virtual functions after reboot, _https://www.reddit.com/r/homelab/comments/hf12mv/persisting_sriov_virtual_functions_after_reboot/_
- Chapter 11. Managing virtual devices, _https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/configuring_and_managing_virtualization/managing-virtual-devices_configuring-and-managing-virtualization_
