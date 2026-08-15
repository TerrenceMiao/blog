---
title: Unlock GL.iNet GL-MT3600BE Beryl 7 Global firmware
date: 2026-08-14 20:12:50
tags:
---

GL.iNet GL-MT3600BE Beryl 7 Dual-Band Wi-Fi 7 Travel Router, coming with Chinese/CN firmware. 

![GL.iNet GL-MT3600BE Beryl 7 Dual-Band Wi-Fi 7 Travel Router](/img/GL.iNet%20GL-MT3600BE%20Beryl%207.jpg "GL.iNet GL-MT3600BE Beryl 7 Dual-Band Wi-Fi 7 Travel Router")

Start up router, and go to _http://192.168.8.1/_ and login.

![GL.iNet GL-MT3600BE Dashboard](/img/GL.iNet%20GL-MT3600BE%20Dashboard.png "GL.iNet GL-MT3600BE Dashboard")

- SSH into router

```
$ ssh -l root 192.168.8.1
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
root@192.168.8.1's password:

BusyBox v1.33.2 (2026-08-06 10:25:08 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt 21.02-SNAPSHOT,
 -----------------------------------------------------
 ```

- Identify the Factory Partition

```
root@GL-MT3600BE:~# cat /proc/mtd
dev:    size   erasesize  name
mtd0: 20000000 00040000 "spi1.0"
mtd1: 00100000 00040000 "BL2"
mtd2: 00080000 00040000 "u-boot-env"
mtd3: 00400000 00040000 "Factory"
mtd4: 00200000 00040000 "FIP"
mtd5: 00040000 00040000 "log"
mtd6: 00040000 00040000 "CFG"
mtd7: 1d800000 00040000 "ubi"
```

The partition named **"Factory"** is **mtd3**, so the partition is **mtdblock3**.

- Find the "CN" (Chinese) Offset

```
root@GL-MT3600BE:~# hexdump -v -e '"%_ad: " 16/1 "%02X " "\n"' /dev/mtdblock3 | grep "43 4E"
16512: 00 00 00 00 00 00 00 00 43 4E 00 00 00 00 00 00
```

- Verify the "CN" Offset

```
root@GL-MT3600BE:~# dd if=/dev/mtdblock3 bs=1 skip=16520 count=2 2>/dev/null
CN
```

- Overwrite region from "CN" to "US"

```
root@GL-MT3600BE:~# echo -n "US" | dd of=/dev/mtdblock3 bs=1 seek=16520 count=2 conv=notrunc
2+0 records in
2+0 records out
```

- Sycn and Reboot

```
root@GL-MT3600BE:~# sync

root@GL-MT3600BE:~# reboot
root@GL-MT3600BE:~# Connection to 192.168.8.1 closed by remote host.
Connection to 192.168.8.1 closed.
```

- Verify the "US" Offset

```
root@GL-MT3600BE:~# hexdump -v -e '"%_ad: " 16/1 "%02X " "\n"' /dev/mtdblock3 | grep ^16512:
16512: 00 00 00 00 00 00 00 00 55 53 00 00 00 00 00 00

root@GL-MT3600BE:~# dd if=/dev/mtdblock3 bs=1 skip=16520 count=2 2>/dev/null
US
```

![GL.iNet GL-MT3600BE Applications](/img/GL.iNet%20GL-MT3600BE%20Applications.png "GL.iNet GL-MT3600BE Applications")

![GL.iNet GL-MT3600BE Toggle Button Settings](/img/GL.iNet%20GL-MT3600BE%20Toggle%20Button.png "GL.iNet GL-MT3600BE Toggle Button Settings")

![GL.iNet GL-MT3600BE VPN](/img/GL.iNet%20GL-MT3600BE%20VPN.png "GL.iNet GL-MT3600BE VPN")

- After the change on GL.iNet Mobile

![GL.iNet GL-MT3600BE Plugins](/img/GL.iNet%20GL-MT3600BE%20Plugins.png "GL.iNet GL-MT3600BE Plugins")

![GL.iNet GL-MT3600BE Toggle Button Settings Mobile](/img/GL.iNet%20GL-MT3600BE%20Toggle%20Button%20Mobile.png "GL.iNet GL-MT3600BE Toggle Button Settings Mobile")

> **_NOTE:_** Disable **IPv6** when install iStoreOS, e.g. from GL-iNet 系列路由器一键 iStoreOS 风格化脚本 https://github.com/wukongdaily/gl-inet-onescript

```
$ sh -c "$(curl -fsSL https://cafe.cpolar.cn/wkdaily/gl/raw/branch/main/mt3600.sh)"
```


References
----------

- [Guide] Region Unlock GL-MT3600BE (Beryl 7) CN to Global _https://www.reddit.com/r/GlInet/comments/1qfeva7/guide_region_unlock_glmt3600be_beryl_7_cn_to/_
- yksten - GL.iNet 国行版和国际版，我越对比越不对劲 _https://www.youtube.com/watch?v=n5qruWaG-ok_
- WukongDaily - MT3600BE脚本使用指南 零基础完整版 _https://www.youtube.com/watch?v=MTtEUsi4V8o_
- Cloud Run Files Builder 工作流同步各位大佬项目里最新编译的 ipk 文件，生成适用于 OpenWrt 用的 run 自解压包 _https://github.com/wkccd/CloudRunFilesBuilder_
