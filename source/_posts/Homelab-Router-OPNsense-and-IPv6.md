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
- Select **Track Interface** for IPv6 Configuration Type
- IPv4 address set to **192.168.2.1/24**
- IPv6 parent interface set to **WAN**
- IPv6 prefix ID set to **0x1**. **0x0** has been assigned to **LAN**
- Enable **Allow manual adjustment of DHCPv6 and Router Advertisements**

![OPNsense - OPT1 Interface](/img/OPNsense%20-%20OPT1%20Interface.png "OPNsense - OPT1 Interface")

- Enable DHCP server on the OPT1 interface
- Set IP range from **192.168.2.10** to **192.168.2.245**

![OPNsense - OPT1 DHCP](/img/OPNsense%20-%20OPT1%20DHCP.png "OPNsense - OPT1 DHCP")

- Add Firewall Rule
- Set TCP/IP version
- Select **OPT1 net** from dropdown menu as Source

![OPNsense - OPT1 Firewall Rule](/img/OPNsense%20-%20OPT1%20Firewall%20Rule.png "OPNsense - OPT1 Firewall Rule")

- Setup Firewall Rule for IPv4 on **OPT1** interface, then
- Setup Firewall Rule for IPv6 on **OPT1** interface

![OPNsense - OPT1 Firewall](/img/OPNsense%20-%20OPT1%20Firewall.png "OPNsense - OPT1 Firewall")


Setup IPv6
----------

Step by step to enable IPv6 with **Buddy Telco / Aussie Broadband** ISP.

![OPNsense - WAN](/img/OPNsense%20-%20WAN.png "OPNsense - WAN")

- Enable DHCP/DHCPv6 for IPv4/IPv6 Configuration Type
- Set ISP's **Prefix delegation size** to `48`
- Enable **Send prefix hint**

![OPNsense - Router Advertisements](/img/OPNsense%20-%20Router%20Advertisements.png "OPNsense - Router Advertisements")

- Set **Router Advertisements** to `Unmanaged`
- Set **Router Priority** to `Normal`


After all the change made above, reboot `OPNsense` instance.

![OPNsense - Dashboard](/img/OPNsense%20-%20Dashboard.png "OPNsense - Dashboard")


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
- Anyone have IPv6 working with OPNSense? _https://www.reddit.com/r/AussieBroadband/comments/10qldin/anyone_have_ipv6_working_with_opnsense/_, _https://f001.backblazeb2.com/b2api/v1/b2_download_file_by_id?fileId=4_zd344e4de4b297a128d680f16_f1019c6ea32f55c8f_d20240526_m042719_c001_v0001101_t0001_u01716697639553_
- Aussie Broadband IPv6 deployment journey, _https://2022.apricot.net/assets/files/APNT374/aussie-broadband-ipv6-deployment-journey.pdf_
- IPv6 Chart / IPv4 CIDR Chart, _https://www.ripe.net/media/documents/IPv6_Chart_2015.pdf_

![OPNsense - IPv6 Chart](/img/OPNsense%20-%20IPv6%20Chart.png "OPNsense - IPv6 Chart")

![OPNsense - IPv4 CIDR Chart](/img/OPNsense%20-%20IPv4%20CIDR%20Chart.png "OPNsense - IPv4 CIDR Chart")
