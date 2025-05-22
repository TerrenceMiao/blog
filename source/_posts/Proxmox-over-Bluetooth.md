---
title: Proxmox over Bluetooth
date: 2025-05-22 15:09:27
tags:
---

With `Proxmox` latest version **8.4.1** which has _WiFi 7 / Bluetooth 5.4_ support. A **Intel® Wi-Fi 7 BE200** network adapter _https://www.intel.com/content/www/us/en/products/sku/230078/intel-wifi-7-be200/specifications.html_ is in action.

On `Proxmox` host, **USB** devices:

```
root@mini:~# lsusb
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 006: ID 8087:0036 Intel Corp.
Bus 003 Device 003: ID 0461:4e04 Primax Electronics, Ltd Lenovo Keyboard KB1021
Bus 003 Device 002: ID 17ef:600e Lenovo Optical Mouse
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

![Proxmox Bluetooth - USB mapping](/img/Proxmox%20Bluetooth%20-%20USB%20mapping.png "Proxmox Bluetooth - USB mapping")

![Proxmox Bluetooth - Resource Mappings](/img/Proxmox%20Bluetooth%20-%20Resource%20Mappings.png "Proxmox Bluetooth - Resource Mappings")

![Proxmox Bluetooth - USB Device](/img/Proxmox%20Bluetooth%20-%20USB%20Device.png "Proxmox Bluetooth - USB Device")

![Proxmox Bluetooth - Add Device](/img/Proxmox%20Bluetooth%20-%20Add%20Device.png "Proxmox Bluetooth - Add Device")
