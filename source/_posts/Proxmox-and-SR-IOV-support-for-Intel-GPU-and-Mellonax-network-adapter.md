---
title: Proxmox and SR-IOV support for Intel GPU and Mellonax network adapter
date: 2024-08-04 11:56:12
tags:
---

- Enable `VT-d` for IOMMU (（Input Output Memory Management Unit) services, and `SR-IOV` in motherboard BIOS in Chipset, e.g. _ASRock Z790 Riptide WiFi_.

- Enable `SR-IOV` for Mellonax network adapter e.g. _Mellanox ConnectX-4 MCX455A-ECAT PCIe x16 3.0 100GBe VPI EDR IB_ in the same motherboard BIOS.

- Add Proxmox No Subscription URL:

```
root@pve:~# cat /etc/apt/sources.list
deb http://ftp.au.debian.org/debian bookworm main contrib

deb http://ftp.au.debian.org/debian bookworm-updates main contrib

# security updates
deb http://security.debian.org bookworm-security main contrib

# Proxmox VE pve-no-subscription repository provided by proxmox.com,
# NOT recommended for production use
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

and run packages update:

```
root@pve:~# apt update
```

and install all build tools:

```
root@pve:~# apt install build-* dkms
```

- Set/Pin Proxmox kernel version:

```
root@pve:~# proxmox-boot-tool kernel pin 6.8.4-2-pve
Setting '6.8.4-2-pve' as grub default entry and running update-grub.
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.8.4-2-pve
Found initrd image: /boot/initrd.img-6.8.4-2-pve
Found memtest86+ 64bit EFI image: /boot/memtest86+x64.efi
Adding boot menu entry for UEFI Firmware Settings ...
done
```

and verify Proxmox kernal version:

```
root@pve:~# proxmox-boot-tool kernel list
Manually selected kernels:
None.

Automatically selected kernels:
6.8.4-2-pve

Pinned kernel:
6.8.4-2-pve
```

- Install Proxmox kernel headers source code package:

```
root@pve:~# apt install proxmox-headers-6.8.4-2-pve
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  proxmox-headers-6.8.4-2-pve
0 upgraded, 1 newly installed, 0 to remove and 39 not upgraded.
Need to get 13.7 MB of archives.
After this operation, 97.0 MB of additional disk space will be used.
Get:1 http://download.proxmox.com/debian/pve bookworm/pve-no-subscription amd64 proxmox-headers-6.8.4-2-pve amd64 6.8.4-2 [13.7 MB]
Fetched 13.7 MB in 1s (23.8 MB/s)
Selecting previously unselected package proxmox-headers-6.8.4-2-pve.
(Reading database ... 70448 files and directories currently installed.)
Preparing to unpack .../proxmox-headers-6.8.4-2-pve_6.8.4-2_amd64.deb ...
Unpacking proxmox-headers-6.8.4-2-pve (6.8.4-2) ...
Setting up proxmox-headers-6.8.4-2-pve (6.8.4-2) ...
```

- Download `Linux i915 driver with SR-IOV` support for Linux kernel:

```
root@pve:~# git clone https://github.com/strongtz/i915-sriov-dkms
```

and change into the cloned repository and run:

```
root@pve:~/i915-sriov-dkms# cat VERSION
2024.07.24

root@pve:~/i915-sriov-dkms# dkms add .
Creating symlink /var/lib/dkms/i915-sriov-dkms/2024.07.24/source -> /usr/src/i915-sriov-dkms-2024.07.24
```

and build, install `i915-sriov-dkms` Linux kernel module:

```
root@pve:~/i915-sriov-dkms# dkms install -m i915-sriov-dkms -v $(cat VERSION) --force
Sign command: /lib/modules/6.8.4-2-pve/build/scripts/sign-file
Signing key: /var/lib/dkms/mok.key
Public certificate (MOK): /var/lib/dkms/mok.pub
Certificate or key are missing, generating self signed certificate for MOK...

Building module:
Cleaning build area...
make -j20 KERNELRELEASE=6.8.4-2-pve -C /lib/modules/6.8.4-2-pve/build M=/var/lib/dkms/i915-sriov-dkms/2024.07.24/build.......
Signing module /var/lib/dkms/i915-sriov-dkms/2024.07.24/build/i915.ko
Cleaning build area...

i915.ko:
Running module version sanity check.
 - Original module
 - Installation
   - Installing to /lib/modules/6.8.4-2-pve/updates/dkms/
depmod...
```

and enable `i915-sriov-dkms` module with upto maximum **7** VFS (Virtual File System) in Linux kernel:

```
root@pve:~# cat /etc/default/grub | grep GRUB_CMDLINE_LINUX_DEFAULT
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on i915.enable_guc=3 i915.max_vfs=7"
```

- Reboot Proxmox:

```
root@pve:~# lspci | grep VGA
00:02.0 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.1 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.2 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.3 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.4 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.5 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.6 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.7 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
```

First VGA `00:02.0` is the **REAL** GPU. Other **7** are **Virtual** ones.

Verify IOMMU has been enabled:

```
root@pve:~# dmesg | grep -i iommu
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.8.4-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on i915.enable_guc=3 i915.max_vfs=7
[    0.036486] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.8.4-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on i915.enable_guc=3 i915.max_vfs=7
[    0.036515] DMAR: IOMMU enabled
[    0.090320] DMAR-IR: IOAPIC id 2 under DRHD base  0xfed91000 IOMMU 1
[    0.244292] pci 0000:00:02.0: DMAR: Skip IOMMU disabling for graphics
[    0.270125] iommu: Default domain type: Translated
[    0.270125] iommu: DMA domain TLB invalidation policy: lazy mode
[    0.303735] DMAR: IOMMU feature fl1gp_support inconsistent
[    0.303735] DMAR: IOMMU feature pgsel_inv inconsistent
[    0.303736] DMAR: IOMMU feature nwfs inconsistent
[    0.303736] DMAR: IOMMU feature dit inconsistent
[    0.303737] DMAR: IOMMU feature sc_support inconsistent
[    0.303737] DMAR: IOMMU feature dev_iotlb_support inconsistent
[    0.304175] pci 0000:00:02.0: Adding to iommu group 0
[    0.304544] pci 0000:00:00.0: Adding to iommu group 1
[    0.304554] pci 0000:00:01.0: Adding to iommu group 2
[    0.304562] pci 0000:00:01.1: Adding to iommu group 3
[    0.304570] pci 0000:00:06.0: Adding to iommu group 4
[    0.304582] pci 0000:00:14.0: Adding to iommu group 5
[    0.304589] pci 0000:00:14.2: Adding to iommu group 5
[    0.304598] pci 0000:00:15.0: Adding to iommu group 6
[    0.304607] pci 0000:00:16.0: Adding to iommu group 7
[    0.304614] pci 0000:00:17.0: Adding to iommu group 8
[    0.304630] pci 0000:00:1a.0: Adding to iommu group 9
[    0.304641] pci 0000:00:1b.0: Adding to iommu group 10
[    0.304651] pci 0000:00:1c.0: Adding to iommu group 11
[    0.304662] pci 0000:00:1c.1: Adding to iommu group 12
[    0.304671] pci 0000:00:1c.2: Adding to iommu group 13
[    0.304681] pci 0000:00:1c.3: Adding to iommu group 14
[    0.304692] pci 0000:00:1c.4: Adding to iommu group 15
[    0.304703] pci 0000:00:1d.0: Adding to iommu group 16
[    0.304721] pci 0000:00:1f.0: Adding to iommu group 17
[    0.304728] pci 0000:00:1f.3: Adding to iommu group 17
[    0.304735] pci 0000:00:1f.4: Adding to iommu group 17
[    0.304742] pci 0000:00:1f.5: Adding to iommu group 17
[    0.304750] pci 0000:01:00.0: Adding to iommu group 18
[    0.304758] pci 0000:02:00.0: Adding to iommu group 19
[    0.304765] pci 0000:03:00.0: Adding to iommu group 20
[    0.304781] pci 0000:04:00.0: Adding to iommu group 21
[    0.304791] pci 0000:05:00.0: Adding to iommu group 22
[    0.304801] pci 0000:06:00.0: Adding to iommu group 23
[    0.304810] pci 0000:07:00.0: Adding to iommu group 24
[    0.304834] pci 0000:08:00.0: Adding to iommu group 25
[    0.304845] pci 0000:09:00.0: Adding to iommu group 26
[    0.304857] pci 0000:0a:00.0: Adding to iommu group 27
[    0.304866] pci 0000:0b:00.0: Adding to iommu group 28
[    4.659395] pci 0000:00:02.1: DMAR: Skip IOMMU disabling for graphics
[    4.659438] pci 0000:00:02.1: Adding to iommu group 29
[    4.664441] pci 0000:00:02.2: DMAR: Skip IOMMU disabling for graphics
[    4.664479] pci 0000:00:02.2: Adding to iommu group 30
[    4.667692] pci 0000:00:02.3: DMAR: Skip IOMMU disabling for graphics
[    4.667727] pci 0000:00:02.3: Adding to iommu group 31
[    4.671096] pci 0000:00:02.4: DMAR: Skip IOMMU disabling for graphics
[    4.671129] pci 0000:00:02.4: Adding to iommu group 32
[    4.673545] pci 0000:00:02.5: DMAR: Skip IOMMU disabling for graphics
[    4.673572] pci 0000:00:02.5: Adding to iommu group 33
[    4.676357] pci 0000:00:02.6: DMAR: Skip IOMMU disabling for graphics
[    4.676402] pci 0000:00:02.6: Adding to iommu group 34
[    4.679192] pci 0000:00:02.7: DMAR: Skip IOMMU disabling for graphics
[    4.679221] pci 0000:00:02.7: Adding to iommu group 35
```

- Enable Virtual GPU for `Windows 11` VM in Proxmox

Add a PCI device for `Windows 11` VM, and choose one Virtual GPU:

![Proxmox - Windows 11, PCI device](/img/Proxmox%20-%20Windows%2011,%20PCI%20device.png "Proxmox - Windows 11, PCI device")

Enable `Primary GPU` and `PCI Express` in options:

![Proxmox - Windows 11, Primary GPU and PCI Express](/img/Proxmox%20-%20Windows%2011,%20Primary%20GPU%20and%20PCI%20Express.png "Proxmox - Windows 11, Primary GPU and PCI Express")

Choose `none` in Display and `host` in Processors options for `Windows 11` VM:

![Proxmox - Windows 11, Display](/img/Proxmox%20-%20Windows%2011,%20Display.png "Proxmox - Windows 11, Display")

![Proxmox - Windows 11, Display none](/img/Proxmox%20-%20Windows%2011,%20Display%20none.png "Proxmox - Windows 11, Display none")

Start `Windows 11` VM, login with `Microsoft Remote Desktop` _https://apps.microsoft.com/detail/9wzdncrfj3ps_ and a Virtual GPU is available now. Run `Task Manager` and check CPU and GPU load:

![Proxmox - Windows 11, Performance](/img/Proxmox%20-%20Windows%2011,%20Performance.png "Proxmox - Windows 11, Performance")

- Enable Virtual GPU for `Ubuntu` VM in Proxmox

Ubuntu version 22.04.4 for desktop.

Add a PCI device for `Ubuntu` VM, and choose one Virtual GPU; Enable `Primary GPU` and `PCI Express` in options; Choose `none` in Display and `host` in Processors options:

![Proxmox - Ubuntu](/img/Proxmox%20-%20Ubuntu.png "Proxmox - Ubuntu")

Setup remote desktop connection to Ubuntu:

```
root@nucleus:~# apt install ubuntu-desktop

root@nucleus:~# apt install xrdp

root@nucleus:~# systemctl enable xrdp
```

![Proxmox - Remote Desktop](/img/Proxmox%20-%20Remote%20Desktop.png "Proxmox - Remote Desktop")

- Fix Remote Desktop audio over HDMI issue with the script, enable the sound redirection:

```
terrence@nucleus:~$ ./xrdp-installer-1.5.1.sh -s
```

then reboot VM.

![Proxmox - Ubuntu Remote Desktop](/img/Proxmox%20-%20Ubuntu%20Remote%20Desktop.png "Proxmox - Ubuntu Remote Desktop")

Now Audio device becomes xrdp input / output.

- Now can remote desktop access Ubuntu and Windows 11 VMs both run in Proxmox:

![Proxmox - Ubuntu and Windows 11](/img/Proxmox%20-%20Ubuntu%20and%20Windows%2011.jpg "Proxmox - Ubuntu and Windows 11")

References
----------

- 最新保姆级 PVE 8 安装教程！虚拟机 PCIE 设备及 SR-IOV 核显直通，最多分配7个虚拟化单独核显！最强虚拟机！_https://v2rayssr.com/pve.html_
- PVE 8.1 下部署 Intel 集显虚拟化驱动 _https://zoe.red/2023/38.html_
- 定制 OVMF 固件实现 PVE 8 环境 UEFI 模式直通 Intel 核显并显示 BIOS 适用于 intel 6-13 代处理器 PVE 8 核显直通画面 Windows 10 and MacOS 系统画面显示教程，可以外接显示器 _https://imacos.top/2023/10/09/152/_
- PVE 下如何启用 PCI 直通显卡 GPU/iGPU/USB/声卡 AUDIO 等硬件直通教程 _https://imacos.top/2023/07/31/pci/_
- Enable & Using vGPU Passthrough _https://gist.github.com/scyto/e4e3de35ee23fdb4ae5d5a3b85c16ed3_
- Proxmox VE: Passthrough with Intel Integrated Graphics Card Alder Lake Architecture | vGPU, VT-d, SR-IOV _https://github.com/kamilllooo/Proxmox_
- Remote Desktop connection from Mac to Ubuntu _https://askubuntu.com/questions/893831/remote-desktop-connection-from-mac-to-ubuntu_
- xRDP – Easy install xRDP on Ubuntu 20.04,22.04,23.XX,24.04 (Script Version 1.5.1) _https://c-nergy.be/blog/?p=19814_