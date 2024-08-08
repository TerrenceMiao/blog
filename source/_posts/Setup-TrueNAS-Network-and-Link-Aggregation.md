---
title: Setup TrueNAS Network and Link Aggregation
date: 2024-08-09 01:03:40
tags:
---

Two network adapters have been setup with DHCP allocated addresses:

1. `ens18` 192.168.0.246
2. `ens19` 192.168.0.105

- Create a Network Link Aggregation Interface

![TrueNAS - Link Aggregation](/img/TrueNAS%20-%20Link%20Aggregation.png "TrueNAS - Link Aggregation")

After Link Aggregation Interface `bond1` created, original two network adapters `ens18` and `ens19` IP addresses are gone. `bond1` with the ONLY ONE network interface address for `TrueNAS`.

![TrueNAS - Network](/img/TrueNAS%20-%20Network.png "TrueNAS - Network")
