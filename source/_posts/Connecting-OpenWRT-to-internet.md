---
title: Connecting OpenWRT to internet
date: 2024-12-05 14:19:34
tags:
---

`OpenWRT` is running on `Proxmox`. Now setting up it to connect internet.

Add new network interface **OPT** on **eth2** adapter:

![OpenWRT - OPT Interface](/img/OpenWRT%20-%20OPT%20Interface.png "OpenWRT - OPT Interface")

![OpenWRT - OPT Advanced Settings](/img/OpenWRT%20-%20OPT%20Advanced%20Settings.png "OpenWRT - OPT Advanced Settings")

![OpenWRT - OPT Firewall Settings](/img/OpenWRT%20-%20OPT%20Firewall%20Settings.png "OpenWRT - OPT Firewall Settings")

![OpenWRT - OPT DHCP Server General Setup](/img/OpenWRT%20-%20OPT%20DHCP%20Server%20General%20Setup.png "OpenWRT - OPT DHCP Server General Setup")

![OpenWRT - OPT DHCP Server Advanced Settings](/img/OpenWRT%20-%20OPT%20DHCP%20Server%20Advanced%20Settings.png "OpenWRT - OPT DHCP Server Advanced Settings")

![OpenWRT - OPT DHCP Server IPv6 Settings](/img/OpenWRT%20-%20OPT%20DHCP%20Server%20IPv6%20Settings.png "OpenWRT - OPT DHCP Server IPv6 Settings")

![OpenWRT - OPT DHCP Server IPv6 RA Settings](/img/OpenWRT%20-%20OPT%20DHCP%20Server%20IPv6%20RA%20Settings.png "OpenWRT - OPT DHCP Server IPv6 RA Settings")

![OpenWRT - OPT](/img/OpenWRT%20-%20OPT.png "OpenWRT - OPT")

Add a new Firewall Zone, from network **opt** to **wan**:

![OpenWRT - Firewall Zone General Settings](/img/OpenWRT%20-%20Firewall%20Zone%20General%20Settings.png "OpenWRT - Firewall Zone General Settings")

![OpenWRT - Firewall Zone Advanced Settings](/img/OpenWRT%20-%20Firewall%20Zone%20Advanced%20Settings.png "OpenWRT - Firewall Zone Advanced Settings")

![OpenWRT - Firewall Zone Conntrack Settings](/img/OpenWRT%20-%20Firewall%20Zone%20Conntrack%20Settings.png "OpenWRT - Firewall Zone Conntrack Settings")

![OpenWRT - Firewall Zone](/img/OpenWRT%20-%20Firewall%20Zone.png "OpenWRT - Firewall Zone")

Add a new Firewall Traffic Rule for network **opt**:

![OpenWRT - Firewall Traffic Rule General Settings](/img/OpenWRT%20-%20Firewall%20Traffic%20Rule%20General%20Settings.png "OpenWRT - Firewall Traffic Rule General Settings")

![OpenWRT - Firewall Traffic Rule Advanced Settings](/img/OpenWRT%20-%20Firewall%20Traffic%20Rule%20Advanced%20Settings.png "OpenWRT - Firewall Traffic Rule Advanced Settings")

![OpenWRT - Firewall Traffic Rule Time Restrictions](/img/OpenWRT%20-%20Firewall%20Traffic%20Rule%20Time%20Restrictions.png "OpenWRT - Firewall Traffic Rule Time Restrictions")

![OpenWRT - Firewall Traffic Rule](/img/OpenWRT%20-%20Firewall%20Traffic%20Rule.png "OpenWRT - Firewall Traffic Rule")

Setup DHCP clients' static IP addresses:

```
$ ssh -l root 192.168.2.1
root@192.168.2.1's password:

BusyBox v1.36.1 (2024-09-23 12:34:46 UTC) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 OpenWrt 23.05.5, r24106-10cc5fcd00
 -----------------------------------------------------

root@OpenWRT:~# cat /etc/config/dhcp
config dnsmasq
	option domainneeded '1'
	option localise_queries '1'
	option rebind_protection '1'
	option rebind_localhost '1'
	option local '/local/'
	option domain 'local'
	option expandhosts '1'
	option cachesize '1000'
	option authoritative '1'
	option readethers '1'
	option leasefile '/tmp/dhcp.leases'
	option resolvfile '/tmp/resolv.conf.d/resolv.conf.auto'
	option localservice '1'
	option ednspacket_max '1232'
...
config host
	option name 'TL-SX3016F'
	option ip '192.168.2.100'
	option mac '3C:52:A1:47:23:5D'

config host
	option name 'MikroTik'
	option ip '192.168.2.110'
	option mac '78:9A:18:D0:20:ED'

config domain
	option name 'TL-SX3016F'
	option ip '192.168.2.100'

config domain
	option name 'MikroTik'
	option ip '192.168.2.110'

```

![OpenWRT - DHCP and DNS](/img/OpenWRT%20-%20DHCP%20and%20DNS.png "OpenWRT - DHCP and DNS")
