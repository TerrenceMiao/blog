---
title: How to install and run Tailscale client on OpenWrt
date: 2024-12-21 14:44:32
tags:
---

Install `Tailscale` package **iptables-nft** and **tailscale** from `OpenWrt` console:

![OpenWrt - Tailscale iptables-nft](/img/OpenWrt%20-%20Tailscale%20iptables-nft.png "OpenWrt - Tailscale iptables-nft")

![OpenWrt - Tailscale](/img/OpenWrt%20-%20Tailscale.png "OpenWrt - Tailscale")

Enable and connect `Tailscale` service in `OpenWrt`:

```
$ ssh -l root SenseWrt
root@SenseWrt's password: 

BusyBox v1.36.1 (2024-12-03 11:41:08 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt 24.10.0-rc2, r28161-ea17e958b9
 -----------------------------------------------------
 
root@SenseWrt:~# tailscale up --netfilter-mode=off --advertise-routes=192.168.88.0/24 --accept-routes
Warning: netfilter=off; configure iptables yourself.
Warning: UDP GRO forwarding is suboptimally configured on eth1, UDP forwarding throughput capability will increase with a configuration change.
See https://tailscale.com/s/ethtool-config-udp-gro

To authenticate, visit:

	https://login.tailscale.com/a/98c452901c4ba

Success.
```

**NOTE:** **192.168.88.0/24** is the ip range of local network setup in `OpenWrt`.

Disable key expiry for `OpenWrt` machine in `Tailscale` console, then enable all `OpenWrt` clients access `Tailscale` network:

![OpenWrt - Tailscale Machines](/img/OpenWrt%20-%20Tailscale%20Machines.png "OpenWrt - Tailscale Machines")

Now add `Tailscale` virtual network as a new interface in `OpenWrt`:

![OpenWrt - Tailscale Network Interface](/img/OpenWrt%20-%20Tailscale%20Network%20Interface.png "OpenWrt - Tailscale Network Interface")

Create firewall for `Tailscale` virtual network interface in `OpenWrt`:

![OpenWrt - Tailscale Firewall](/img/OpenWrt%20-%20Tailscale%20Firewall.png "OpenWrt - Tailscale Firewall")

Configure firewall for `Tailscale` virtual network interface in `OpenWrt`:

![OpenWrt - Tailscale Firewall General Settings](/img/OpenWrt%20-%20Tailscale%20Firewall%20General%20Settings.png "OpenWrt - Tailscale Firewall General Settings")

**NOTE:** **opt** network is for the downstream DHCP clients.


References
----------

- 韩风 Talk - Tailscale 玩法之内网穿透、异地组网、全隧道模式、纯 IP 的双栈 DERP 搭建、Headscale 协调服务器搭建，用一期搞定，看一看不亏吧？_https://www.youtube.com/watch?v=mgDpJX3oNvI_
