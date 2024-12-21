---
title: How to install and run ZeroTier client on OpenWrt
date: 2024-12-21 11:08:18
tags:
---

Install `ZeroTier` package **zerotier** from `OpenWrt` console:

![OpenWrt - ZeroTier](/img/OpenWrt%20-%20ZeroTier.png "OpenWrt - ZeroTier")

Enable `ZeroTier` service in `OpenWrt`:

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

root@SenseWrt:~# cat /etc/config/zerotier 

config zerotier 'global'
	# Sets whether ZeroTier is enabled or not
	option enabled 1
	# Sets the ZeroTier listening port (default 9993; set to 0 for random)
	#option port '9993'
	# Client secret (leave blank to generate a secret on first run)
	option secret ''
	# Path of the optional file local.conf (see documentation at
	# https://docs.zerotier.com/config#local-configuration-options)
	#option local_conf_path '/etc/zerotier.conf'
	# Persistent configuration directory (to perform other configurations such
	# as controller mode or moons, etc.)
	#option config_path '/etc/zerotier'
	# Copy the contents of the persistent configuration directory to memory
	# instead of linking it, this avoids writing to flash
	#option copy_config_path '1'

# Network configuration, you can have as many configurations as networks you
# want to join (the network name is optional)
config network 'earth'
	# Identifier of the network you wish to join
	option id '8ca917257083e297'
	# Network configuration parameters (all are optional, if not indicated the
	# default values are set, see documentation at
	# https://docs.zerotier.com/config/#network-specific-configuration)
	option allow_managed '1'
	option allow_global '0'
	option allow_default '0'
	option allow_dns '0'

# Example of a second network (unnamed as it is optional)
#config network
#	option id '1234567890123456'
#	option allow_managed '1'
#	option allow_global '0'
#	option allow_default '0'
#	option allow_dns '0'
```

Restart `ZeroTier` service:

```
$ root@SenseWrt:~# /etc/init.d/zerotier restart
Generating secret - please wait... done.
```

Verify `ZeroTier` client status, which **ztks555nye** is the virtual network interface created on `OpenWrt`:

```
root@SenseWrt:~# zerotier-cli listnetworks
200 listnetworks <nwid> <name> <mac> <status> <type> <dev> <ZT assigned ips>
200 listnetworks 8ca917257083e297 igloo.studio 9a:09:7b:41:3d:26 OK PRIVATE ztks555nye 192.168.196.176/24
```

Enable and authorise the new `ZeroTier` client `OpenWrt` in _https://my.zerotier.com/_.

Now add `ZeroTier` virtual network as a new interface in `OpenWrt`:

![OpenWrt - ZeroTier Network Interface](/img/OpenWrt%20-%20ZeroTier%20Network%20Interface.png "OpenWrt - ZeroTier Network Interface")

Create firewall for `ZeroTier` virtual network interface in `OpenWrt`:

![OpenWrt - ZeroTier Firewall](/img/OpenWrt%20-%20ZeroTier%20Firewall.png "OpenWrt - ZeroTier Firewall")

Configure firewall for `ZeroTier` virtual network interface in `OpenWrt`:

![OpenWrt - ZeroTier Firewall General Settings](/img/OpenWrt%20-%20ZeroTier%20Firewall%20General%20Settings.png "OpenWrt - ZeroTier Firewall General Settings")

**NOTE:** **opt** network is for the downstream DHCP clients.

Add **Allow-ZeroTier** firewall rule:

![OpenWrt - ZeroTier Firewall Rule](/img/OpenWrt%20-%20ZeroTier%20Firewall%20Rule.png "OpenWrt - ZeroTier Firewall Rule")

Restart `ZeroTier` service again:

```
$ root@SenseWrt:~# /etc/init.d/zerotier restart
Generating secret - please wait... done.
```

Then all `OpenWrt` clients can access `ZeroTier` network now.


References
----------

- 韩风 Talk - 内网穿透工具 ZeroTier，从简单到复杂的玩法，无保留，一期全放送 _https://www.youtube.com/watch?v=pa0tkch3lss_
- ZeroTier Terminology - Planet server, Moon server and client node (LEAF) _https://www.wlink-tech.com/art/about-zerotier_ 
