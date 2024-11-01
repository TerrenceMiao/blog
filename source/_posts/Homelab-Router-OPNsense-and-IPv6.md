---
title: Homelab Router OPNsense and IPv6
date: 2024-11-01 21:25:50
tags:
---

Current IPv6 setting on TP-Link Router, connecting to **Buddy Telco / Aussie Broadband** ISP.

![OPNsense - TP-Link IPv6](/img/OPNsense%20-%20TP-Link%20IPv6.png "OPNsense - TP-Link IPv6")


Setup OPNsense Optional Port
----------------------------

For example, setup **OPT1** interface after `OPNsense` installed in `Proxmox`.

- Enable **OPT1** Interface
- Select **Static IPv4** for IPv4 Configuration Type
- IPv4 address set to **192.168.2.1/24**

![OPNsense - OPT1 Interface](/img/OPNsense%20-%20OPT1%20Interface.png "OPNsense - OPT1 Interface")

- Enable DHCP server on the OPT1 interface
- Set IP range from **192.168.2.10** to **192.168.2.245**

![OPNsense - OPT1 DHCP](/img/OPNsense%20-%20OPT1%20DHCP.png "OPNsense - OPT1 DHCP")

- Add Firewall Rule
- Set TCP/IP version
- Select **OPT1 net** from dropdown menu as Source

![OPNsense - OPT1 Firewall Rule](/img/OPNsense%20-%20OPT1%20Firewall%20Rule.png "OPNsense - OPT1 Firewall Rule")

- Setup Firewall Rule for **OPT1** interface

![OPNsense - OPT1 Firewall](/img/OPNsense%20-%20OPT1%20Firewall.png "OPNsense - OPT1 Firewall")


IPv6 Test
---------

- _https://test-ipv6.com/_

![OPNsense - Test IPv6](/img/OPNsense%20-%20Test%20IPv6.png "OPNsense - Test IPv6")

- _https://ipv6-test.com/_

![OPNsense - IPv6 Test](/img/OPNsense%20-%20IPv6%20Test.png "OPNsense - Test Test")


References
----------

- Virtualize OPNsense on Proxmox as Your Primary Router, _https://homenetworkguy.com/how-to/virtualize-opnsense-on-proxmox-as-your-primary-router/_
- OPNsense Optional Port Configuration, _https://kb.protectli.com/kb/opnsense-optional-port-configuration/_
- IPv6 setup, _https://docs.opnsense.org/manual/ipv6.html_
- Aussie Broadband IPv6 deployment journey, _https://2022.apricot.net/assets/files/APNT374/aussie-broadband-ipv6-deployment-journey.pdf_
- IPv6 Chart / IPv4 CIDR Chart, _https://www.ripe.net/media/documents/IPv6_Chart_2015.pdf_

![OPNsense - IPv6 Chart](/img/OPNsense%20-%20IPv6%20Chart.png "OPNsense - IPv6 Chart")

![OPNsense - IPv4 CIDR Chart](/img/OPNsense%20-%20IPv4%20CIDR%20Chart.png "OPNsense - IPv4 CIDR Chart")
