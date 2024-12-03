---
title: Running OpenWRT on Proxmox
date: 2024-12-03 12:46:37
tags:
---

- Create `OpenWRT` VM in `Proxmox`

![OpenWRT - VM General](/img/OpenWRT%20-%20VM%20General.png "OpenWRT - VM General")

![OpenWRT - VM OS](/img/OpenWRT%20-%20VM%20OS.png "OpenWRT - VM OS")

![OpenWRT - VM System](/img/OpenWRT%20-%20VM%20System.png "OpenWRT - VM System")

![OpenWRT - VM Disks](/img/OpenWRT%20-%20VM%20Disks.png "OpenWRT - VM Disks")

![OpenWRT - VM CPU](/img/OpenWRT%20-%20VM%20CPU.png "OpenWRT - VM CPU")

![OpenWRT - VM Network](/img/OpenWRT%20-%20VM%20Network.png "OpenWRT - VM Network")

This is the **LAN**, and Firewall is **DISABLED**.

![OpenWRT - VM Confirm](/img/OpenWRT%20-%20VM%20Confirm.png "OpenWRT - VM Confirm")

Add **WAN** into VM, and Firewall is **DISABLED**.

From `Proxmox` Console, download the latest OpenWRT image:

```
root@sense:~# wget https://downloads.openwrt.org/releases/23.05.5/targets/x86/64/openwrt-23.05.5-x86-64-generic-ext4-combined.img.gz
--2024-12-03 12:20:33--  https://downloads.openwrt.org/releases/23.05.5/targets/x86/64/openwrt-23.05.5-x86-64-generic-ext4-combined.img.gz
Resolving downloads.openwrt.org (downloads.openwrt.org)... 2a04:4e42:13::644, 151.101.82.132
Connecting to downloads.openwrt.org (downloads.openwrt.org)|2a04:4e42:13::644|:443... failed: No route to host.
Connecting to downloads.openwrt.org (downloads.openwrt.org)|151.101.82.132|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 11352149 (11M) [application/octet-stream]
Saving to: ‘openwrt-23.05.5-x86-64-generic-ext4-combined.img.gz’

openwrt-23.05.5-x86-64-generic-ext4-combined.img.gz  100%[===================================================>]  10.83M  --.-KB/s  in 0.1s    
```

Create disk image for VM:

```
root@sense:~# gunzip openwrt-23.05.5-x86-64-generic-ext4-combined.img.gz

root@sense:~# mv openwrt-23.05.5-x86-64-generic-ext4-combined.img openwrt.raw

# increase the raw disk
root@sense:~# qemu-img resize -f raw ./openwrt.raw 1024M
Image resized.
```

Convert `OpenWRT` image to VM disk:

```
# import the raw disk to OpenWRT VM
root@sense:~# qm importdisk 104 openwrt.raw local-lvm
Use of uninitialized value $dev in hash element at /usr/share/perl5/PVE/QemuServer/Drive.pm line 555.
importing disk 'openwrt.raw' to VM 104 ...
  Logical volume "vm-104-disk-0" created.
transferred 0.0 B of 1.0 GiB (0.00%)
...
transferred 1.0 GiB of 1.0 GiB (100.00%)
unused0: successfully imported disk 'local-lvm:vm-104-disk-0'
```

Double click the **Unused Disk**, then click the Add button:

![OpenWRT - VM Unused Disk](/img/OpenWRT%20-%20VM%20Unused%20Disk.png "OpenWRT - VM Unused Disk")

![OpenWRT - VM Hardware](/img/OpenWRT%20-%20VM%20Hardware.png "OpenWRT - VM Hardware")

![OpenWRT - VM Boot Order](/img/OpenWRT%20-%20VM%20Boot%20Order.png "OpenWRT - VM Boot Order")


- Configure `OpenWRT`

Start up VM; change the user **root** password; set LAN ip address temporarily to **192.168.2.3**:

![OpenWRT - Console](/img/OpenWRT%20-%20Console.png "OpenWRT - Console")

Login `OpenWRT` at _http://192.168.2.3/_, via LAN port, and land on dashboard:

![OpenWRT - Login](/img/OpenWRT%20-%20Login.png "OpenWRT - Login")

![OpenWRT - Dashboard](/img/OpenWRT%20-%20Dashboard.png "OpenWRT - Dashboard")


References
----------

- Run an OpenWRT VM on Proxmox VE, _https://i12bretro.github.io/tutorials/0405.html_
- My Mobile HomeLab! (Travel Router with Proxmox, Docker, and OpenWRT), _https://technotim.live/posts/mobile-homelab/_
