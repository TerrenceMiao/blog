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

![Installation fix](/img/Synology%20NAS%20network%20upgrade%20-%20installation%20fix.png "Installation fix")

and continue / retry the installation .

- Reboot NAS.

- Login Synology Admin UI, **Package Center** -> **Installed** -> **RTL8152/RTL8153 driver** and check new installed Realtek network adapter driver is running:

![Running](/img/Synology%20NAS%20network%20upgrade%20-%20running.png "Running")

- **Control Panel** -> **Network** -> **Network Interface** and check the new network interface `LAN 3` has been turned on:

![New network interface](/img/Synology%20NAS%20network%20upgrade%20-%20new%20network%20interface.png "New network interface")


References
----------

- DSM driver for realtek RTL8152/RTL8153/RTL8156 based USB Ethernet adapters, _https://github.com/bb-qq/r8152_