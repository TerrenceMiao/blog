---
title: Setup network bond in Synology NAS
date: 2024-08-11 15:45:25
tags:
---

Synology NAS DS920+ with two build-in 1Gbps ethernet adapters, and two 2.5Gbps USB 3.0 Ethernet Adapters. Now setup network bond / link aggregation on them.

- Enable Network Link Aggregation Mode

![Synology - Network Link Aggregation Mode](/img/Synology%20-%20Network%20Link%20Aggregation.png "Synology - Network Link Aggregation Mode")

- Pickup network devices into bond

![Synology - Physical devices](/img/Synology%20-%20Physical%20devices.png "Synology - Physical devices")

- Setup network

![Synology - Physical devices network setup](/img/Synology%20-%20Physical%20devices%20network%20setup.png "Synology - Physical devices network setup")

- Accept network interface change after network bond

![Synology - Bond warning message](/img/Synology%20-%20Bond%20warning%20message.png "Synology - Bond warning message")

- Network bonded

![Synology - Network bonded](/img/Synology%20-%20Network%20bonded.png "Synology - Network bonded")

- Network bond service order

![Synology - Network bond service order](/img/Synology%20-%20Network%20bonded.png "Synology - Network bond service order")
