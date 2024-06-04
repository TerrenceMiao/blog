---
title: How to upgrade Synology NAS network from 1Gbps to 2.5Gbps
date: 2024-05-17 20:25:45
tags:
---

Synology NAS DS920+ with two 1Gbps ethernet adapters. There is an affordable and easy upgrading its gigabytes network path to 2.5Gbps.

Login Synology NAS Admin UI and run **Control Panel** -> **Network** -> **Network Interface**

![Installation before](/img/Synology%20NAS%20network%20upgrade%20-%20installation%20before.png "Installation before")

- Get a USB 3.0 Ethernet Adapter 2.5Gbps with **Realtek RTL8156 / RTL8156B / RTL8156BG**  chipset, e.g., UGREEN 2.5Gbps USB-C Ethernet Adapter:

![UGREEN 2.5Gbps USB-C Ethernet Adapter](img/UGREEN%202.5Gbps%20USB-C%20Ethernet%20Adapter.jpg "UGREEN 2.5Gbps USB-C Ethernet Adapter")

- Find out the architecture name of CPU in NAS. For example, Synology DS920+ is equipped with Intel Celeron J4125 CPU. The architecture name of this processor is `Geminilake`.

- Go to driver releases site _https://github.com/bb-qq/r8152/releases_ and download the latest version e.g. `r8152-geminilake-2.17.1-1_7.2.spk`, Synology DSM 7.2 and above, use packages with the suffix **_7.2**.

- Login Synology Admin UI, then go to **Package Center** -> **Manual Install** and choose a driver package downloaded from above step.

![Package installation](/img/Synology%20NAS%20network%20upgrade%20-%20package%20installation.png "Package installation")

![Installation warning](/img/Synology%20NAS%20network%20upgrade%20-%20installation%20warning.png "Installation warning")

![Installation confirmation](/img/Synology%20NAS%20network%20upgrade%20-%20installation%20confirmation.png "Installation confirmation")

- The installation will fail at the very first time.

![Installation failed](/img/Synology%20NAS%20network%20upgrade%20-%20installation%20failed.png "Installation failed")

- Then `ssh` into the NAS, and run the following command:

```
$ sudo install -m 4755 -o root -D /var/packages/r8152/target/r8152/spk_su /opt/sbin/spk_su
```

and also enable multiple identical USB devices, which SAME products have the SAME serial number:

```
$ sudo bash /var/packages/r8152/scripts/install-udev-rules
```

![Installation fix](/img/Synology%20NAS%20network%20upgrade%20-%20installation%20fix.png "Installation fix")

```
$ sudo bash /var/packages/r8152/scripts/install-udev-rules
Updating Hardware Database Index...
UDEV rules have been installed to /usr/lib/udev/rules.d
lrwxrwxrwx 1 root root 50 May 24 17:13 /usr/lib/udev/rules.d/51-usb-r8152-net.rules -> /var/packages/r8152/scripts/51-usb-r8152-net.rules
```

and continue / retry the installation .

- Reboot NAS.

- Login Synology Admin UI, **Package Center** -> **Installed** -> **RTL8152/RTL8153 driver** and check new installed Realtek network adapter driver is running:

![Running](/img/Synology%20NAS%20network%20upgrade%20-%20running.png "Running")

- **Control Panel** -> **Network** -> **Network Interface** and check the new network interface `LAN 3` and `Lan 4` have been turned on, with MTU / jumbo frame enabled **9000**:

![New network interface](/img/Synology%20NAS%20network%20upgrade%20-%20new%20network%20interface.png "New network interface")

Bind the USB network adapter and run `iperf3` network performance test:

```
$ iperf3 -c 192.168.0.244 -B 192.168.0.229
Connecting to host 192.168.0.244, port 5201
[  5] local 192.168.0.229 port 46171 connected to 192.168.0.244 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   281 MBytes  2.36 Gbits/sec    0    450 KBytes
[  5]   1.00-2.00   sec   281 MBytes  2.35 Gbits/sec    0    450 KBytes
[  5]   2.00-3.00   sec   280 MBytes  2.35 Gbits/sec    0    450 KBytes
[  5]   3.00-4.00   sec   281 MBytes  2.35 Gbits/sec    0    450 KBytes
[  5]   4.00-5.00   sec   281 MBytes  2.35 Gbits/sec    0    450 KBytes
[  5]   5.00-6.00   sec   281 MBytes  2.35 Gbits/sec    0    450 KBytes
[  5]   6.00-7.00   sec   281 MBytes  2.35 Gbits/sec    0    450 KBytes
[  5]   7.00-8.00   sec   280 MBytes  2.35 Gbits/sec    0    450 KBytes
[  5]   8.00-9.00   sec   281 MBytes  2.36 Gbits/sec    0    450 KBytes
[  5]   9.00-10.00  sec   281 MBytes  2.36 Gbits/sec    0    670 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  2.74 GBytes  2.35 Gbits/sec    0             sender
[  5]   0.00-10.05  sec  2.74 GBytes  2.34 Gbits/sec                  receiver

iperf Done.
```


References
----------

- DSM driver for realtek RTL8152/RTL8153/RTL8156 based USB Ethernet adapters, _https://github.com/bb-qq/r8152_
- Hot plugging does not work, _https://github.com/bb-qq/r8152/wiki/Troubleshooting#hot-plugging-does-not-work_
- Multiple identical devices do not work, _https://github.com/bb-qq/r8152/wiki/Troubleshooting#multiple-identical-devices-do-not-work_