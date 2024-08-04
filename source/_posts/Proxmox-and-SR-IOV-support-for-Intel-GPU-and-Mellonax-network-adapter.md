---
title: Proxmox and SR-IOV support for Intel GPU and Mellonax network adapter
date: 2024-08-04 11:56:12
tags:
---

- Enable `VT-d` which provides IOMMU services, and `SR-IOV` in motherboard BIOS in Chipset, e.g. _ASRock Z790 Riptide WiFi_.

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

- Enable Virtual GPU for `Windows 11` VM in Proxmox

Add a PCI device for `Windows 11` VM, and choose one Virtual GPU:

![Proxmox - Windows 11, PCI device](/img/Proxmox%20-%20Windows%2011,%20PCI%20device.png "Proxmox - Windows 11, PCI device")

Enable `Primary GPU` and `PCI Express` in options:

![Proxmox - Windows 11, Primary GPU and PCI Express](/img/Proxmox%20-%20Windows%2011,%20Primary%20GPU%20and%20PCI%20Express.png "Proxmox - Windows 11, Primary GPU and PCI Express")

Choose `none` in Display option for `Windows 11` VM:

![Proxmox - Windows 11, Display](/img/Proxmox%20-%20Windows%2011,%20Display.png "Proxmox - Windows 11, Display")

![Proxmox - Windows 11, Display none](/img/Proxmox%20-%20Windows%2011,%20Display%20none.png "Proxmox - Windows 11, Display none")

Start `Windows 11` VM, login with `Microsoft Remote Desktop` _https://apps.microsoft.com/detail/9wzdncrfj3ps_ and a Virtual GPU is available now. Run `Task Manager` and check CPU and GPU load:

![Proxmox - Windows 11, Performance](/img/Proxmox%20-%20Windows%2011,%20Performance.png "Proxmox - Windows 11, Performance")

- Enable Virtual GPU for `Ubuntu` VM in Proxmox

Ubuntu version 22.04.4 for desktop.

Add a PCI device for `Ubuntu` VM, and choose one Virtual GPU; Enable `Primary GPU` and `PCI Express` in options; Choose `none` in Display option:

![Proxmox - Ubuntu](/img/Proxmox%20-%20Ubuntu.png "Proxmox - Ubuntu")

Setup remote desktop connection to Ubuntu:

```
root@nucleus:~# apt install ubuntu-desktop

root@nucleus:~# apt install xrdp

root@nucleus:~# systemctl enable xrdp
```

![Proxmox - Remote Desktop](/img/Proxmox%20-%20Remote%20Desktop.png "Proxmox - Remote Desktop")

References
----------

- 最新保姆级 PVE 8 安装教程！虚拟机 PCIE 设备及 SR-IOV 核显直通，最多分配7个虚拟化单独核显！最强虚拟机！_https://v2rayssr.com/pve.html_
- PVE 8.1 下部署 Intel 集显虚拟化驱动 _https://zoe.red/2023/38.html_
- Enable & Using vGPU Passthrough _https://gist.github.com/scyto/e4e3de35ee23fdb4ae5d5a3b85c16ed3_
- Proxmox VE: Passthrough with Intel Integrated Graphics Card Alder Lake Architecture | vGPU, VT-d, SR-IOV _https://github.com/kamilllooo/Proxmox_
- Remote Desktop connection from Mac to Ubuntu _https://askubuntu.com/questions/893831/remote-desktop-connection-from-mac-to-ubuntu_
