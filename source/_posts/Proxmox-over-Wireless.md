---
title: Proxmox over Wireless
date: 2025-05-21 11:16:37
tags:
---

To make `Proxmox` latest version **9.1-1** <s>8.4-1</s> support _WiFi 7_. **Intel® Wi-Fi 7 BE200** network adapter _https://www.intel.com/content/www/us/en/products/sku/230078/intel-wifi-7-be200/specifications.html_ is in action.

Setup wireless network as main network for `Proxmox`. **TP-Link Deco BE85/BE95 Mesh WiFi 7 System** as the Access Point, with DHCP enabled for the clients.

Basically you need to get the latest Linux firmware for Intel Wireless cards driver `iwlwifi` _https://wireless.docs.kernel.org/en/latest/en/users/drivers/iwlwifi.html_, overwriting the the firmware come along with `Proxmox`.

```
root@mini:~# uname -a
Linux mini 6.17.9-1-pve #1 SMP PREEMPT_DYNAMIC PMX 6.17.9-1 (2026-01-12T16:25Z) x86_64 GNU/Linux

root@mini:~# lspci -vnn | grep -i net
01:00.0 Ethernet controller [0200]: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection [8086:10fb] (rev 01)
01:00.1 Ethernet controller [0200]: Intel Corporation 82599ES 10-Gigabit SFI/SFP+ Network Connection [8086:10fb] (rev 01)
02:00.0 Ethernet controller [0200]: Intel Corporation Ethernet Controller I226-V [8086:125c] (rev 04)
03:00.0 Ethernet controller [0200]: Intel Corporation Ethernet Controller I226-V [8086:125c] (rev 04)
04:00.0 Network controller [0280]: Intel Corporation Wi-Fi 7(802.11be) AX1775*/AX1790*/BE20*/BE401/BE1750* 2x2 [8086:272b] (rev 1a)

root@mini:~# dmesg | grep wifi
[    3.587646] iwlwifi 0000:04:00.0: enabling device (0000 -> 0002)
[    3.591932] iwlwifi 0000:04:00.0: Detected crf-id 0x2001910, cnv-id 0x2001910 wfpm id 0x80000000
[    3.591947] iwlwifi 0000:04:00.0: PCI dev 272b/00f4, rev=0x472, rfid=0x112200
[    3.591950] iwlwifi 0000:04:00.0: Detected Intel(R) Wi-Fi 7 BE200 320MHz
[    3.592034] iwlwifi 0000:04:00.0: Direct firmware load for iwlwifi-gl-c0-fm-c0-102.ucode failed with error -2
[    3.592063] iwlwifi 0000:04:00.0: Direct firmware load for iwlwifi-gl-c0-fm-c0-101.ucode failed with error -2
[    3.592086] iwlwifi 0000:04:00.0: Direct firmware load for iwlwifi-gl-c0-fm-c0-100.ucode failed with error -2
[    3.592110] iwlwifi 0000:04:00.0: Direct firmware load for iwlwifi-gl-c0-fm-c0-99.ucode failed with error -2
[    3.592132] iwlwifi 0000:04:00.0: Direct firmware load for iwlwifi-gl-c0-fm-c0-98.ucode failed with error -2
[    3.592134] iwlwifi 0000:04:00.0: no suitable firmware found!
[    3.592136] iwlwifi 0000:04:00.0: minimum version required: iwlwifi-gl-c0-fm-c0-98
[    3.592138] iwlwifi 0000:04:00.0: maximum version supported: iwlwifi-gl-c0-fm-c0-102
[    3.592139] iwlwifi 0000:04:00.0: check git://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git
[    4.071952] Modules linked in: snd_sof_xtensa_dsp(+) snd_sof snd_sof_utils snd_soc_acpi_intel_match snd_soc_acpi_intel_sdca_quirks soundwire_generic_allocation snd_soc_acpi soundwire_bus snd_soc_sdca crc8 snd_soc_avs x86_pkg_temp_thermal intel_powerclamp snd_soc_hda_codec coretemp snd_hda_ext_core sch_fq_codel snd_hda_codec snd_hda_core snd_intel_dspcfg kvm_intel snd_intel_sdw_acpi snd_hwdep i915(+) mei_hdcp mei_pxp snd_soc_core drm_buddy kvm btusb ttm snd_compress btrtl irqbypass ac97_bus snd_pcm_dmaengine drm_display_helper btintel polyval_clmulni ghash_clmulni_intel snd_pcm cec ov13858 iwlwifi intel_pmc_core snd_timer aesni_intel v4l2_fwnode btbcm pmt_telemetry snd v4l2_async cmdlinepart btmtk rapl pmt_discovery intel_cstate rc_core spi_nor pmt_class pcspkr wmi_bmof mei_me videodev input_leds intel_pmc_ssram_telemetry cfg80211 bluetooth mtd soundcore mei i2c_algo_bit igen6_edac mc intel_vsec acpi_pad acpi_tad mac_hid zfs(PO) spl(O) msr vhost_net vhost vhost_iotlb tap efi_pstore nfnetlink dmi_sysfs ip_tables
```

`Proxmox` vanila version doesn't support Intel Wifi. 

```
root@mini:~# git clone git://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git

root@mini:~# rm /lib/firmware/iwlwifi-*.{ucode,pnvm}.xz

root@mini:~# cp linux-firmware/intel/iwlwifi-*.{ucode,pnvm} /lib/firmware/
```

After machine restarted:

```
root@mini:~# dmesg | grep wifi
[    3.564714] iwlwifi 0000:04:00.0: enabling device (0000 -> 0002)
[    3.568230] iwlwifi 0000:04:00.0: Detected crf-id 0x2001910, cnv-id 0x2001910 wfpm id 0x80000000
[    3.568245] iwlwifi 0000:04:00.0: PCI dev 272b/00f4, rev=0x472, rfid=0x112200
[    3.568248] iwlwifi 0000:04:00.0: Detected Intel(R) Wi-Fi 7 BE200 320MHz
[    3.568562] iwlwifi 0000:04:00.0: Direct firmware load for iwlwifi-gl-c0-fm-c0-102.ucode failed with error -2
[    3.574238] iwlwifi 0000:04:00.0: loaded firmware version 101.6ef20b19.0 gl-c0-fm-c0-101.ucode op_mode iwlmld
[    4.008136] Modules linked in: kvm(+) snd_hda_core snd_intel_dspcfg snd_intel_sdw_acpi sch_fq_codel snd_hwdep irqbypass snd_soc_core polyval_clmulni ghash_clmulni_intel iwlmld(+) aesni_intel i915(+) snd_compress mei_pxp mei_hdcp rapl mac80211 libarc4 ac97_bus drm_buddy btusb snd_pcm_dmaengine snd_pcm ov13858 ttm intel_pmc_core snd_timer intel_cstate btrtl v4l2_fwnode iwlwifi drm_display_helper snd pmt_telemetry mei_me btintel pmt_discovery v4l2_async btbcm cec pmt_class cmdlinepart rc_core spi_nor btmtk pcspkr wmi_bmof videodev input_leds intel_pmc_ssram_telemetry cfg80211 mtd soundcore bluetooth mei i2c_algo_bit igen6_edac mc intel_vsec acpi_tad acpi_pad mac_hid zfs(PO) spl(O) msr vhost_net vhost vhost_iotlb tap efi_pstore nfnetlink dmi_sysfs ip_tables x_tables autofs4 btrfs blake2b_generic xor raid6_pq usbmouse hid_generic usbkbd usbhid hid dm_thin_pool dm_persistent_data dm_bio_prison dm_bufio nvme ixgbe nvme_core i2c_i801 xhci_pci nvme_keyring spi_intel_pci i2c_mux xfrm_algo i2c_smbus nvme_auth spi_intel mdio igc
[    4.240699] iwlwifi 0000:04:00.0: Detected RF FM, rfid=0x112200
[    4.349269] iwlwifi 0000:04:00.0: base HW address: e0:8f:4c:b2:58:95
[    4.540839] iwlwifi 0000:04:00.0 wlp4s0f0: renamed from wlan0
```

Install wifi support packages:

```
root@mini:~# apt install wpasupplicant wireless-tools dnsmasq
```

Configure network interfaces:

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

Configure `dnsmasq` for VMs' DNS and DHCP management:

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
192.168.68.50 mini.local mini

# The following lines are desirable for IPv6 capable hosts

::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
```

Update DNS resolver:

```
root@mini:~# cat /etc/resolv.conf 
nameserver 192.168.2.1
nameserver 192.168.68.1
```

then access `Proxmox` web UI via URL **https://192.168.68.50:8006**

Reboot `Proxmox` host:

```
root@mini:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: nic0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:4f brd ff:ff:ff:ff:ff:ff
    altname enxa8b8e005964f
3: nic1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:50 brd ff:ff:ff:ff:ff:ff
    altname enxa8b8e0059650
4: nic2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:4d brd ff:ff:ff:ff:ff:ff
    altname enxa8b8e005964d
5: nic3: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether a8:b8:e0:05:96:4e brd ff:ff:ff:ff:ff:ff
    altname enxa8b8e005964e
6: wlp4s0f0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether e0:8f:4c:b2:58:95 brd ff:ff:ff:ff:ff:ff
    altname wlxe08f4cb25895
    inet 192.168.68.50/22 brd 192.168.71.255 scope global dynamic wlp4s0f0
       valid_lft 7078sec preferred_lft 7078sec
    inet6 fda5:9d5d:ff57:10:e28f:4cff:feb2:5895/64 scope global dynamic mngtmpaddr proto kernel_ra 
       valid_lft forever preferred_lft forever
    inet6 2403:581d:e139:10:e28f:4cff:feb2:5895/64 scope global dynamic mngtmpaddr proto kernel_ra 
       valid_lft 6155sec preferred_lft 2553sec
    inet6 fe80::e28f:4cff:feb2:5895/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
7: vmbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 1e:1e:8a:24:5e:3d brd ff:ff:ff:ff:ff:ff
    inet 10.0.1.1/24 scope global vmbr0
       valid_lft forever preferred_lft forever
    inet6 fe80::1c1e:8aff:fe24:5e3d/64 scope link proto kernel_ll 
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
- Intel drivers in Linux firmware repo _https://gitlab.com/kernel-firmware/linux-firmware/-/tree/main/intel_
- Proxmox Forum - Wifi _https://forum.proxmox.com/tags/wifi/_
