---
title: Enable SR-IOV in Intel 82599ES 10Gbps Ethernet Controller
date: 2024-11-08 21:06:48
tags:
---

**Intel® 82599ES 10Gbps Ethernet Controller** _https://ark.intel.com/content/www/us/en/ark/products/41282/intel-82599es-10-gigabit-ethernet-controller.html_ supports **SR-IOV**, which is Intel® Virtualization Technology for Connectivity (VT-c) solution.

The following step by step instructions is made in `Proxmox`.

Intel i3 N305 Mini PC _https://www.aliexpress.com/item/1005007278560105.html_ 

![Intel Network - Mini PC](/img/Intel%20Network%20-%20Mini%20PC.png "Intel Network - Mini PC")

equips with 2x10G SFP+ (Intel 82599ES) and 2x2.5G (Intel i226-V) network cards:

```
root@sense:~# lspci | grep Ethernet
01:00.0 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
01:00.1 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
02:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
03:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
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
```

Enable **IOMMU** in `Proxmox`:

```
root@sense:~# cat /etc/default/grub | grep GRUB_CMDLINE_LINUX_DEFAULT
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
```

Update UEFI:

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
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.8.12-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on
[    0.053988] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.8.12-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on
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
[    0.484136] pci 0000:00:02.0: Adding to iommu group 0
[    0.484654] pci 0000:00:00.0: Adding to iommu group 1
...
[    0.484866] pci 0000:04:00.0: Adding to iommu group 15
```

Assign **8** Virtual Network Cards to **Intel® 82599ES 10Gbps Ethernet Controller**:

```
root@sense:~# ip addr
...
4: enp1s0f0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr0 state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:4d brd ff:ff:ff:ff:ff:ff
5: enp1s0f1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq master vmbr1 state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:4e brd ff:ff:ff:ff:ff:ff
...

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
