---
title: Proxmox over Wireless
date: 2025-05-21 11:16:37
tags:
---

With `Proxmox` latest version **8.4.1** which has _WiFi 7_ support. A **Intel® Wi-Fi 7 BE200** network adapter _https://www.intel.com/content/www/us/en/products/sku/230078/intel-wifi-7-be200/specifications.html_ is in action.

Setup wireless network as main network for `Proxmox`. **TP-Link Deco BE85/BE95 Mesh WiFi 7 System** as the Access Point, with DHCP enabled for the clients.

On `Proxmox` host, PCI devices:

```
root@mini:~# lspci
00:00.0 Host bridge: Intel Corporation Device 4617
00:02.0 VGA compatible controller: Intel Corporation Alder Lake-N [UHD Graphics]
...
00:02.7 VGA compatible controller: Intel Corporation Alder Lake-N [UHD Graphics]
00:0d.0 USB controller: Intel Corporation Alder Lake-N Thunderbolt 4 USB Controller
00:14.0 USB controller: Intel Corporation Alder Lake-N PCH USB 3.2 xHCI Host Controller
00:14.2 RAM memory: Intel Corporation Alder Lake-N PCH Shared SRAM
00:16.0 Communication controller: Intel Corporation Alder Lake-N PCH HECI Controller
00:17.0 SATA controller: Intel Corporation Alder Lake-N SATA AHCI Controller
00:1c.0 PCI bridge: Intel Corporation Alder Lake-N PCI Express Root Port
00:1c.6 PCI bridge: Intel Corporation Alder Lake-N PCI Express Root Port
00:1d.0 PCI bridge: Intel Corporation Alder Lake-N PCI Express Root Port
00:1d.1 PCI bridge: Intel Corporation Alder Lake-N PCI Express Root Port
00:1d.3 PCI bridge: Intel Corporation Alder Lake-N PCI Express Root Port
00:1f.0 ISA bridge: Intel Corporation Alder Lake-N PCH eSPI Controller
00:1f.3 Multimedia audio controller: Intel Corporation Alder Lake-N PCH High Definition Audio Controller
00:1f.4 SMBus: Intel Corporation Alder Lake-N SMBus
00:1f.5 Serial bus controller: Intel Corporation Alder Lake-N SPI (flash) Controller
01:00.0 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
01:00.1 Ethernet controller: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection (rev 01)
01:10.0 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
...
01:10.7 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
01:11.0 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
...
01:11.7 Ethernet controller: Intel Corporation 82599 Ethernet Controller Virtual Function (rev 01)
02:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
03:00.0 Ethernet controller: Intel Corporation Ethernet Controller I226-V (rev 04)
04:00.0 Network controller: Intel Corporation Wi-Fi 7(802.11be) AX1775*/AX1790*/BE20*/BE401/BE1750* 2x2 (rev 1a)
05:00.0 Non-Volatile memory controller: Micron/Crucial Technology T500 NVMe PCIe SSD (rev 01)
```
network interfaces:

```
root@mini:~# cat /etc/network/interfaces
auto lo
iface lo inet loopback

iface enp2s0 inet manual
iface enp3s0 inet manual

iface enp1s0f0 inet manual
iface enp1s0f1 inet manual

iface enp1s0f0v0 inet manual
...
iface enp1s0f0v7 inet manual

iface enp1s0f1v0 inet manual
...
iface enp1s0f1v7 inet manual

auto wlp4s0f0
iface wlp4s0f0 inet dhcp
        wpa-ssid "igloo studio"
        wpa-psk "igloo studio password"

auto vmbr0
iface vmbr0 inet static
        address 10.0.1.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0

        # Enable IP forwarding
        post-up echo 1 > /proc/sys/net/ipv4/ip_forward

        # Allow all forwarding from VM network to WAN
        post-up   iptables -A FORWARD -i vmbr0 -o wlp4s0f0 -j ACCEPT
        post-down iptables -D FORWARD -i vmbr0 -o wlp4s0f0 -j ACCEPT

        # Single NAT rule for all traffic
        post-up   iptables -t nat -A POSTROUTING -s '10.0.1.0/24' -o wlp4s0f0 -j MASQUERADE
        post-down iptables -t nat -D POSTROUTING -s '10.0.1.0/24' -o wlp4s0f0 -j MASQUERADE

auto vmbr1
iface vmbr1 inet manual
        bridge-ports enp1s0f0
        bridge-stp off
        bridge-fd 0

auto vmbr2
iface vmbr2 inet manual
        bridge-ports enp1s0f1
        bridge-stp off
        bridge-fd 0

source /etc/network/interfaces.d/*
```

`dnsmasq` for VMs' DNS and DHCP management:

```
root@mini:~# cat /etc/dnsmasq.conf
...
## Proxmox
# Hosts dnsmasq on vmbr0
interface=vmbr0

# The IP-address range that should be used for the clients (virtual machines/containers):
# dhcp-range=first_available_ip, last_available_ip, net_mask, lease_time (<number>h)
dhcp-range=10.0.1.100, 10.0.1.200, 255.255.255.0, 12h

# Just making sure dnsmasq knows the routers IP-address
# dhcp-option=3,vmbr0_ip
dhcp-option=3,10.0.1.1
```

`Proxmox` host allocated wireless IP e.g. `192.168.68.58`. Set this IP in **/etc/hosts**:

```
root@mini:~# cat /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.68.58 mini.local mini

# The following lines are desirable for IPv6 capable hosts

::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
```

then access `Proxmox` web UI via URL **https://192.168.68.58:8006**.

Reboot `Proxmox` host:

```
root@mini:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: enp2s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:4f brd ff:ff:ff:ff:ff:ff
3: enp3s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:50 brd ff:ff:ff:ff:ff:ff
4: enp1s0f0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr1 state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:4d brd ff:ff:ff:ff:ff:ff
5: enp1s0f1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master vmbr2 state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:4e brd ff:ff:ff:ff:ff:ff
7: enp1s0f0v1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether de:11:ed:5f:a6:d5 brd ff:ff:ff:ff:ff:ff
...
13: enp1s0f0v7: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 3e:45:70:0c:fd:98 brd ff:ff:ff:ff:ff:ff
14: enp1s0f1v0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 06:72:73:c2:7a:fc brd ff:ff:ff:ff:ff:ff
...
21: enp1s0f1v7: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 6a:06:68:af:7a:dc brd ff:ff:ff:ff:ff:ff
22: wlp4s0f0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether e0:8f:4c:b2:58:95 brd ff:ff:ff:ff:ff:ff
    inet 192.168.68.58/22 brd 192.168.71.255 scope global dynamic wlp4s0f0
       valid_lft 3800sec preferred_lft 3800sec
    inet6 fda5:9d5d:ff57:10:e28f:4cff:feb2:5895/64 scope global dynamic mngtmpaddr
       valid_lft forever preferred_lft forever
    inet6 2403:5802:8c44:10:e28f:4cff:feb2:5895/64 scope global dynamic mngtmpaddr
       valid_lft 5371sec preferred_lft 1768sec
    inet6 fe80::e28f:4cff:feb2:5895/64 scope link
       valid_lft forever preferred_lft forever
23: vmbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
    inet 10.0.1.1/24 scope global vmbr0
       valid_lft forever preferred_lft forever
    inet6 fe80::643d:cdff:fe59:f79/64 scope link
       valid_lft forever preferred_lft forever
24: vmbr1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:4d brd ff:ff:ff:ff:ff:ff
    inet6 fda5:9d5d:ff57:10:aab8:e0ff:fe05:964d/64 scope global dynamic mngtmpaddr
       valid_lft forever preferred_lft forever
    inet6 2403:5802:8c44:10:aab8:e0ff:fe05:964d/64 scope global dynamic mngtmpaddr
       valid_lft 5372sec preferred_lft 1768sec
    inet6 fe80::aab8:e0ff:fe05:964d/64 scope link
       valid_lft forever preferred_lft forever
25: vmbr2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether a8:b8:e0:05:96:4e brd ff:ff:ff:ff:ff:ff
    inet6 fda5:9d5d:ff57:10:aab8:e0ff:fe05:964e/64 scope global dynamic mngtmpaddr
       valid_lft forever preferred_lft forever
    inet6 2403:5802:8c44:10:aab8:e0ff:fe05:964e/64 scope global dynamic mngtmpaddr
       valid_lft 5372sec preferred_lft 1768sec
    inet6 fe80::aab8:e0ff:fe05:964e/64 scope link
       valid_lft forever preferred_lft forever
```

`Proxmox` Network settings:

![Proxmox Wireless - Network](/img/Proxmox%20Wireless%20-%20Network.png "Proxmox Wireless - Network")

**Linux/Ubuntu VM** in `Proxmox`:

![Proxmox Wireless - Ubuntu VM](/img/Proxmox%20Wireless%20-%20Ubuntu%20VM.png "Proxmox Wireless - Ubuntu VM")

**Windows VM** in `Proxmox`:

![Proxmox Wireless - Windows VM](/img/Proxmox%20Wireless%20-%20Windows%20VM.png "Proxmox Wireless - Windows VM")

**Windows VM** in `Proxmox`'s network:

![Proxmox Wireless - Windows VM Network](/img/Proxmox%20Wireless%20-%20Windows%20VM%20Network.png "Proxmox Wireless - Windows VM Network")


References
----------

- Setting Up Proxmox on a Laptop Without an Ethernet Port _https://dev.to/varungujarathi9/setting-up-proxmox-on-a-laptop-without-an-ethernet-port-28n8_
- Setting up Proxmox over wifi _https://github.com/ThomasRives/Proxmox-over-wifi_
