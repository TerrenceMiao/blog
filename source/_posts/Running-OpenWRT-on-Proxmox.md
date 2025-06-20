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

![OpenWRT - VM Memory](/img/OpenWRT%20-%20VM%20Memory.png "OpenWRT - VM Memory")

![OpenWRT - VM Network](/img/OpenWRT%20-%20VM%20Network.png "OpenWRT - VM Network")

This is the **LAN**, and Firewall is **DISABLED**.

![OpenWRT - VM Confirm](/img/OpenWRT%20-%20VM%20Confirm.png "OpenWRT - VM Confirm")

Add **WAN** into VM, and Firewall is **DISABLED**.

Detach and delete current **Hard Disk**.

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

Start up VM; change the user **root** password; set LAN ip address temporarily to **192.168.2.3** (Default: **192.168.1.1**):

![OpenWRT - Console](/img/OpenWRT%20-%20Console.png "OpenWRT - Console")

Login `OpenWRT` at _http://192.168.2.3/_, via LAN port, and land on dashboard:

![OpenWRT - Login](/img/OpenWRT%20-%20Login.png "OpenWRT - Login")

![OpenWRT - Dashboard](/img/OpenWRT%20-%20Dashboard.png "OpenWRT - Dashboard")


- Themes

**SSH** login:

```
$ ssh -l root 192.168.2.3
The authenticity of host '192.168.2.3 (192.168.2.3)' can't be established.
ED25519 key fingerprint is SHA256:AggWAL1oU8+r1f84KoqpvcsYUylZOTfN0sXwHSby3b0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.2.3' (ED25519) to the list of known hosts.
root@192.168.2.3's password:
  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt 23.05.5, r24106-10cc5fcd00
 -----------------------------------------------------

BusyBox v1.36.1 (2024-09-23 12:34:46 UTC) built-in shell (ash)
```

`OpenWRT` packages configuration:

```
root@OpenWrt:~# ls -al /etc/opkg
drwxr-xr-x    3 root     root          4096 Dec  6 02:46 .
drwxr-xr-x   23 root     root          4096 Dec  6 02:41 ..
-rw-r--r--    1 root     root           103 Sep 23 12:34 customfeeds.conf
-rw-r--r--    1 root     root           555 Dec  6 02:46 distfeeds.conf
drwxr-xr-x    2 root     root          4096 Sep 23 12:34 keys

root@OpenWrt:~# ls -al /var/opkg-lists/
drwxr-xr-x    2 root     root           320 Dec  6 03:15 .
drwxrwxrwt   17 root     root           440 Dec  6 03:15 ..
-rw-r--r--    1 root     root         72705 Dec  6 03:15 openwrt_base
-rw-r--r--    1 root     root           142 Dec  6 03:15 openwrt_base.sig
-rw-r--r--    1 root     root         10039 Dec  6 03:15 openwrt_core
-rw-r--r--    1 root     root           142 Dec  6 03:15 openwrt_core.sig
-rw-r--r--    1 root     root        100416 Dec  6 03:15 openwrt_kmods
-rw-r--r--    1 root     root           142 Dec  6 03:15 openwrt_kmods.sig
-rw-r--r--    1 root     root        197242 Dec  6 03:15 openwrt_luci
-rw-r--r--    1 root     root           142 Dec  6 03:15 openwrt_luci.sig
-rw-r--r--    1 root     root        516243 Dec  6 03:15 openwrt_packages
-rw-r--r--    1 root     root           142 Dec  6 03:15 openwrt_packages.sig
-rw-r--r--    1 root     root          9918 Dec  6 03:15 openwrt_routing
-rw-r--r--    1 root     root           142 Dec  6 03:15 openwrt_routing.sig
-rw-r--r--    1 root     root         58240 Dec  6 03:15 openwrt_telephony
-rw-r--r--    1 root     root           142 Dec  6 03:15 openwrt_telephony.sig
```

There is some issue with **IPv6** support in `OpenWRT` when download update. Errors thrown when **wan** connected to a **IPv6** router:

```
root@OpenWrt:~# opkg update
Downloading https://downloads.openwrt.org/releases/23.05.5/targets/x86/64/packages/Packages.gz
*** Failed to download the package list from https://downloads.openwrt.org/releases/23.05.5/targets/x86/64/packages/Packages.gz
...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/telephony/Packages.gz
*** Failed to download the package list from https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/telephony/Packages.gz

Collected errors:
 * opkg_download: Failed to download https://downloads.openwrt.org/releases/23.05.5/targets/x86/64/packages/Packages.gz, wget returned 4.
 * opkg_download: Check your network settings and connectivity.
...
 * opkg_download: Failed to download https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/telephony/Packages.gz, wget returned 4.
 * opkg_download: Check your network settings and connectivity.
```

Turn off **IPv6** - navigate through OpenWRT UI, **Network** -> **Interfaces**, select **Devices** tab. For device `br-lan` click on **Configure ...**, choose **disabled**  option for **Enable IPv6** in **General device options** tab.

```
root@OpenWRT:~# opkg update
Downloading https://downloads.openwrt.org/releases/23.05.5/targets/x86/64/packages/Packages.gz
Updated list of available packages in /var/opkg-lists/openwrt_core
Downloading https://downloads.openwrt.org/releases/23.05.5/targets/x86/64/packages/Packages.sig
Signature check passed.
...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/telephony/Packages.gz
Updated list of available packages in /var/opkg-lists/openwrt_telephony
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/telephony/Packages.sig
Signature check passed.
```

Install `OpenWRT2020` Theme _https://openwrt.org/docs/guide-user/luci/luci.themes_:

```
root@OpenWRT:~# opkg install luci-theme-openwrt-2020
Installing luci-theme-openwrt-2020 (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-theme-openwrt-2020_git-24.332.79522-a493155_all.ipk
Configuring luci-theme-openwrt-2020.
```

Install Argon Theme _https://github.com/jerrykuku/luci-theme-argon_:

```
root@OpenWRT:~# opkg install luci-compat
Installing luci-compat (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-compat_git-24.332.79522-a493155_all.ipk
Installing liblua5.1.5 (5.1.5-11) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/base/liblua5.1.5_5.1.5-11_x86_64.ipk
Installing lua (5.1.5-11) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/base/lua_5.1.5-11_x86_64.ipk
Installing luci-lib-nixio (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-lib-nixio_git-24.332.79522-a493155_x86_64.ipk
Installing luci-lib-ip (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-lib-ip_git-24.332.79522-a493155_x86_64.ipk
Installing luci-lib-jsonc (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-lib-jsonc_git-24.332.79522-a493155_x86_64.ipk
Installing liblucihttp-lua (2023-03-15-9b5b683f-1) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/liblucihttp-lua_2023-03-15-9b5b683f-1_x86_64.ipk
Installing luci-lib-base (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-lib-base_git-24.332.79522-a493155_all.ipk
Installing libubus-lua (2023-06-05-f787c97b-1) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/base/libubus-lua_2023-06-05-f787c97b-1_x86_64.ipk
Installing ucode-mod-lua (1) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/ucode-mod-lua_1_x86_64.ipk
Installing luci-lua-runtime (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-lua-runtime_git-24.332.79522-a493155_x86_64.ipk
Configuring liblua5.1.5.
Configuring lua.
Configuring luci-lib-nixio.
Configuring luci-lib-ip.
Configuring luci-lib-jsonc.
Configuring liblucihttp-lua.
Configuring luci-lib-base.
Configuring libubus-lua.
Configuring ucode-mod-lua.
Configuring luci-lua-runtime.
Configuring luci-compat.

root@OpenWRT:~# opkg install luci-lib-ipkg
Installing luci-lib-ipkg (git-24.332.79522-a493155) to root...
Downloading https://downloads.openwrt.org/releases/23.05.5/packages/x86_64/luci/luci-lib-ipkg_git-24.332.79522-a493155_all.ipk
Configuring luci-lib-ipkg.

root@OpenWRT:~# wget --no-check-certificate -O luci-theme-argon.ipk https://github.com/jerrykuku/luci-theme-argon/releases/download/v2.3.1/luci-theme-argon_2.3.1_all.ipk
root@OpenWRT:~# wget --no-check-certificate -O luci-app-argon-config.ipk https://github.com/jerrykuku/luci-app-argon-config/releases/download/v0.9/luci-app-argon-config_0.9_all.ipk
```

There are ERRORS:

- _//usr/lib/opkg/info/luci-theme-argon.postinst: .: line 2: can't open '/etc/uci-defaults/30_luci-theme-argon': No such file or directory_
- _//usr/lib/opkg/info/luci-app-argon-config.postinst: .: line 2: can't open '/etc/uci-defaults/luci-argon-config': No such file or directory_

thrown when try to install these packages.

A workaround solution is modify **/lib/functions.sh** file, replace line **282** (OpenWrt version 23.05.5), or line **377** (OpenWrt version 24.10.0):

```
    ( [ -f "$i" ] && cd "$(dirname $i)" && . "$i" ) && rm -f "$i"
```

with:

```
    ( [ -f "$i" ] && cd "$(dirname $i)" && . "$i" ) && echo
```

temporarily. Then run installation:

```
root@OpenWRT:~# opkg install luci-theme-argon.ipk
Installing luci-theme-argon (2.3.1) to root...
Configuring luci-theme-argon.

root@OpenWRT:~# opkg install luci-app-argon-config.ipk
Installing luci-app-argon-config (0.9) to root...
Configuring luci-app-argon-config.
```

- Upgrade

To upgrade all of the `OpenWRT` packages:

```
root@OpenWRT:~# opkg list-upgradable | cut -f 1 -d ' ' | xargs -r opkg upgrade
```


References
----------

- Run an OpenWRT VM on Proxmox VE, _https://i12bretro.github.io/tutorials/0405.html_
- My Mobile HomeLab! (Travel Router with Proxmox, Docker, and OpenWRT), _https://technotim.live/posts/mobile-homelab/_
