---
title: Running MacOS on Proxmox
date: 2024-09-08 11:34:16
tags:
---

Following the instructions to run the latest MacOS, _Sonoma 14.6.1_, on Proxmox - [The Definitive Guide to Running MacOS in Proxmox](https://klabsdev.com/definitive-guide-to-running-macos-in-proxmox).

![Proxmox - MacOS](/img/Proxmox%20-%20MacOS.png "Proxmox - MacOS")

A few more put on notes:

- You can login with `Apple ID`
- Modify `config.plist` file under EFI folder -> OC folder, change the default Screen Resolution from `1080P` **1920x1080@32** to `2K` **2560x1440@32**. Look more information in `Configuration.pdf` file in [**KVM-Opencore** release](https://github.com/thenickdude/KVM-Opencore/releases)

Connect with **Apple Remote Desktop**, so can use exact same Apple Keyboard mapping:

![Proxmox - Apple Remote Desktop](/img/Proxmox%20-%20Apple%20Remote%20Desktop.png "Proxmox - Apple Remote Desktop")

Current [**KVM-Opencore**](https://github.com/thenickdude/KVM-Opencore) hasn't setup support for Audio device and GPU accelerator.

Also try to setup [**Parsec**](https://parsec.app/) in MacOS on Proxmox, without success.

![Proxmox - Parsec](/img/Proxmox%20-%20Parsec.png "Proxmox - Parsec")
