---
title: Proxmox and SR-IOV support for Intel GPU and Mellanox network adapter
date: 2024-08-04 11:56:12
tags:
---

- Enable `VT-d`(Intel Virtualization Technology for Directed I/O), for `IOMMU`(Input Output Memory Management Unit) services, and `SR-IOV` (Single Root IO Virtualization), a technology that allows a physical PCIe device to present itself multiple times through the PCIe bus, in motherboard BIOS in Chipset, e.g. _ASRock Z790 Riptide WiFi_ & _Mini PC with Intel Alder Lake-N i3-N305_.

- Enable `SR-IOV` for Mellonax network adapter e.g. _Mellanox ConnectX-4 MCX455A-ECAT PCIe x16 3.0 100GBe VPI EDR IB_ in the same motherboard BIOS.

- `SR-IOV` is default enabled for _Intel 82599ES 10G optical ports network card_.

- Add Proxmox No Subscription by running `Proxmox VE Helper-Scripts` _https://community-scripts.github.io/ProxmoxVE/scripts_:

```
root@pve:~# cat /etc/apt/sources.list
deb http://deb.debian.org/debian bookworm main contrib
deb http://deb.debian.org/debian bookworm-updates main contrib
deb http://security.debian.org/debian-security bookworm-security main contrib
```
and run packages update:

```
root@pve:~# apt update
```

and install all build tools:

```
root@pve:~# apt install build-* dkms git sysfsutils intel-gpu-tools mokutil -y
```

- Set/Pin Proxmox kernel version:

```
root@pve:~# proxmox-boot-tool kernel pin 6.8.4-2-pve
Setting '6.8.4-2-pve' as grub default entry and running update-grub.
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-6.8.4-2-pve
Found initrd image: /boot/initrd.img-6.8.4-2-pve
Found memtest86+ 64bit EFI image: /boot/memtest86+x64.efi
Adding boot menu entry for UEFI Firmware Settings ...
done
```

and verify Proxmox kernal version:

```
root@pve:~# proxmox-boot-tool kernel list
Manually selected kernels:
None.

Automatically selected kernels:
6.8.4-2-pve

Pinned kernel:
6.8.4-2-pve
```

- <s>Install Proxmox kernel headers source code package:</s>

```
root@pve:~# uname -r
6.8.4-2-pve

root@pve:~# apt install proxmox-headers-$(uname -r)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  proxmox-headers-6.8.4-2-pve
0 upgraded, 1 newly installed, 0 to remove and 39 not upgraded.
Need to get 13.7 MB of archives.
After this operation, 97.0 MB of additional disk space will be used.
Get:1 http://download.proxmox.com/debian/pve bookworm/pve-no-subscription amd64 proxmox-headers-6.8.4-2-pve amd64 6.8.4-2 [13.7 MB]
Fetched 13.7 MB in 1s (23.8 MB/s)
Selecting previously unselected package proxmox-headers-6.8.4-2-pve.
(Reading database ... 70448 files and directories currently installed.)
Preparing to unpack .../proxmox-headers-6.8.4-2-pve_6.8.4-2_amd64.deb ...
Unpacking proxmox-headers-6.8.4-2-pve (6.8.4-2) ...
Setting up proxmox-headers-6.8.4-2-pve (6.8.4-2) ...
```

Upgrade Linux Kernel i915 firmware to the latest version:

```
root@pve:~# mkdir firmware && cd firmware

root@pve:~/firmware# wget -r -nd -e robots=no -A '*.bin' --accept-regex '/plain/' https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/
--2025-05-18 13:41:09--  https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/
Resolving git.kernel.org (git.kernel.org)... 172.236.150.65
Connecting to git.kernel.org (git.kernel.org)|172.236.150.65|:443... connected.
...

root@pve:~/firmware# mv *.bin /lib/firmware/i915/
```

- <s>Download `Linux i915 driver with SR-IOV` support for Linux kernel:</s>

```
root@pve:~# git clone https://github.com/strongtz/i915-sriov-dkms
```

<s>and change into the cloned repository and run:</s>

```
root@pve:~/i915-sriov-dkms# cat dkms.conf
PACKAGE_NAME="i915-sriov-dkms"
PACKAGE_VERSION="2024.07.24"

BUILT_MODULE_NAME[0]="i915"
DEST_MODULE_LOCATION[0]=/updates
MAKE[0]="make -C ${kernel_source_dir} M=${dkms_tree}/${PACKAGE_NAME}/${PACKAGE_VERSION}/build"

BUILT_MODULE_NAME[1]="kvmgt"
DEST_MODULE_LOCATION[1]=/updates
CLEAN="make -C ${kernel_source_dir} M=${dkms_tree}/${PACKAGE_NAME}/${PACKAGE_VERSION}/build clean"

AUTOINSTALL=yes
BUILD_EXCLUSIVE_KERNEL="^6\.([8-9]|1[0-5])"

root@pve:~/i915-sriov-dkms# dkms add .
Creating symlink /var/lib/dkms/i915-sriov-dkms/2024.07.24/source -> /usr/src/i915-sriov-dkms-2024.07.24
```

<s>and build, install `i915-sriov-dkms` Linux kernel module:</s>

```
root@pve:~/i915-sriov-dkms# GUCFIRMWARE_MINOR=13 dkms install -m $(grep PACKAGE_NAME= dkms.conf | awk -F'"' '{print $2}') -v $(grep PACKAGE_VERSION= dkms.conf | awk -F'"' '{print $2}') --force --kernelsourcedir /usr/src/linux-headers-$(uname -r)
Sign command: /lib/modules/6.8.4-2-pve/build/scripts/sign-file
Signing key: /var/lib/dkms/mok.key
Public certificate (MOK): /var/lib/dkms/mok.pub
Certificate or key are missing, generating self signed certificate for MOK...

Building module:
Cleaning build area...
make -j20 KERNELRELEASE=6.8.4-2-pve -C /lib/modules/6.8.4-2-pve/build M=/var/lib/dkms/i915-sriov-dkms/2024.07.24/build.......
Signing module /var/lib/dkms/i915-sriov-dkms/2024.07.24/build/i915.ko
Cleaning build area...

i915.ko:
Running module version sanity check.
 - Original module
 - Installation
   - Installing to /lib/modules/6.8.4-2-pve/updates/dkms/
depmod...
```

Download `i915-sriov-dkms` deb release package:

```
root@pve:~# wget -O /tmp/i915-sriov-dkms_2026.02.09_amd64.deb "https://github.com/strongtz/i915-sriov-dkms/releases/download/2026.02.09/i915-sriov-dkms_2026.02.09_amd64.deb"
```

Install the deb package with `dpkg`: 

```
root@pve:~# dpkg -i /tmp/i915-sriov-dkms_2026.02.09_amd64.deb
Selecting previously unselected package i915-sriov-dkms.
(Reading database ... 97011 files and directories currently installed.)
Preparing to unpack .../i915-sriov-dkms_2026.02.09_amd64.deb ...
Unpacking i915-sriov-dkms (2026.02.09) ...
Setting up i915-sriov-dkms (2026.02.09) ...
Removing old i915-sriov-dkms/2026.02.09 DKMS files...
Deleting module i915-sriov-dkms/2026.02.09 completely from the DKMS tree.
Loading new i915-sriov-dkms/2026.02.09 DKMS files...
Building for 6.17.9-1-pve

Building initial module i915-sriov-dkms/2026.02.09 for 6.17.9-1-pve
Sign command: /lib/modules/6.17.9-1-pve/build/scripts/sign-file
Signing key: /var/lib/dkms/mok.key
Public certificate (MOK): /var/lib/dkms/mok.pub
Certificate or key are missing, generating self signed certificate for MOK...

Building module(s)....................................................... done.
Signing module /var/lib/dkms/i915-sriov-dkms/2026.02.09/build/compat/intel_sriov_compat.ko
Signing module /var/lib/dkms/i915-sriov-dkms/2026.02.09/build/drivers/gpu/drm/i915/i915.ko
Signing module /var/lib/dkms/i915-sriov-dkms/2026.02.09/build/drivers/gpu/drm/i915/kvmgt.ko
Signing module /var/lib/dkms/i915-sriov-dkms/2026.02.09/build/drivers/gpu/drm/xe/xe.ko
Installing /lib/modules/6.17.9-1-pve/updates/dkms/intel_sriov_compat.ko
Found pre-existing /lib/modules/6.17.9-1-pve/kernel/drivers/gpu/drm/i915/i915.ko, archiving for uninstallation
Installing /lib/modules/6.17.9-1-pve/updates/dkms/i915.ko
Found pre-existing /lib/modules/6.17.9-1-pve/kernel/drivers/gpu/drm/i915/kvmgt.ko, archiving for uninstallation
Installing /lib/modules/6.17.9-1-pve/updates/dkms/kvmgt.ko
Found pre-existing /lib/modules/6.17.9-1-pve/kernel/drivers/gpu/drm/xe/xe.ko, archiving for uninstallation
Installing /lib/modules/6.17.9-1-pve/updates/dkms/xe.ko
Running depmod... done.
update-initramfs: Generating /boot/initrd.img-6.17.9-1-pve
Running hook script 'zz-proxmox-boot'..
Re-executing '/etc/kernel/postinst.d/zz-proxmox-boot' in new private mount namespace..
No /etc/kernel/proxmox-boot-uuids found, skipping ESP sync.
update-initramfs: Generating /boot/initrd.img-6.17.2-1-pve
Running hook script 'zz-proxmox-boot'..
Re-executing '/etc/kernel/postinst.d/zz-proxmox-boot' in new private mount namespace..
No /etc/kernel/proxmox-boot-uuids found, skipping ESP sync.
```

and enable `i915-sriov-dkms` module with upto maximum **7** VFS (Virtual File System) in Linux kernel:

```
root@pve:~# cat /etc/default/grub | grep GRUB_CMDLINE_LINUX_DEFAULT
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt i915.enable_guc=3 i915.max_vfs=7 module_blacklist=xe"
```

enable SR-IOV Configuration:

```
root@pve:~# echo "devices/pci0000:00/0000:00:02.0/sriov_numvfs = 7" >> /etc/sysfs.conf
```

```
root@pve:~# update-grub

root@pve:~# update-initramfs -u -k all
update-initramfs: Generating /boot/initrd.img-6.8.4-2-pve
Running hook script 'zz-proxmox-boot'..
Re-executing '/etc/kernel/postinst.d/zz-proxmox-boot' in new private mount namespace..
No /etc/kernel/proxmox-boot-uuids found, skipping ESP sync.
...
```

- Reboot Proxmox:

```
root@pve:~# lspci | grep VGA
00:02.0 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.1 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
...
00:02.6 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
00:02.7 VGA compatible controller: Intel Corporation Raptor Lake-S GT1 [UHD Graphics 770] (rev 04)
```

First VGA `00:02.0` is the **REAL** GPU. Other **7** are **Virtual** ones.

Verify IOMMU has been enabled:

```
root@pve:~# dmesg | grep -i iommu
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.8.4-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on i915.enable_guc=3 i915.max_vfs=7
[    0.036486] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.8.4-2-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on i915.enable_guc=3 i915.max_vfs=7
[    0.036515] DMAR: IOMMU enabled
[    0.090320] DMAR-IR: IOAPIC id 2 under DRHD base  0xfed91000 IOMMU 1
[    0.244292] pci 0000:00:02.0: DMAR: Skip IOMMU disabling for graphics
[    0.270125] iommu: Default domain type: Translated
[    0.270125] iommu: DMA domain TLB invalidation policy: lazy mode
[    0.303735] DMAR: IOMMU feature fl1gp_support inconsistent
[    0.303735] DMAR: IOMMU feature pgsel_inv inconsistent
[    0.303736] DMAR: IOMMU feature nwfs inconsistent
[    0.303736] DMAR: IOMMU feature dit inconsistent
[    0.303737] DMAR: IOMMU feature sc_support inconsistent
[    0.303737] DMAR: IOMMU feature dev_iotlb_support inconsistent
[    0.304175] pci 0000:00:02.0: Adding to iommu group 0
[    0.304544] pci 0000:00:00.0: Adding to iommu group 1
...
[    0.304866] pci 0000:0b:00.0: Adding to iommu group 28
[    4.659395] pci 0000:00:02.1: DMAR: Skip IOMMU disabling for graphics
...
[    4.679192] pci 0000:00:02.7: DMAR: Skip IOMMU disabling for graphics
[    4.679221] pci 0000:00:02.7: Adding to iommu group 35
```

Verify `i915-sriov-dkms` module has been loaded:

```
root@pve:~# dmesg | grep i915
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.8.12-10-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on iommu=pt i915.enable_guc=3 i915.max_vfs=7
[    0.054188] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.8.12-10-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on iommu=pt i915.enable_guc=3 i915.max_vfs=7
               use xe.force_probe='46d0' and i915.force_probe='!46d0'
[    3.593393] i915: module verification failed: signature and/or required key missing - tainting kernel
[    4.270741] i915: You are using the i915-sriov-dkms module, a ported version of the i915 module with SR-IOV support.
[    4.270743] i915: Please file any bug report at https://github.com/strongtz/i915-sriov-dkms/issues/new.
[    4.270745] i915: Module Homepage: https://github.com/strongtz/i915-sriov-dkms
[    4.271143] i915 0000:00:02.0: [drm] Found ALDERLAKE_P/ADL-N (device ID 46d0) display version 13.00 stepping D0
[    4.271176] i915 0000:00:02.0: Running in SR-IOV PF mode
[    4.271727] i915 0000:00:02.0: [drm] VT-d active for gfx access
[    4.364169] i915 0000:00:02.0: vgaarb: deactivate vga console
[    4.364247] i915 0000:00:02.0: [drm] Using Transparent Hugepages
[    4.364664] i915 0000:00:02.0: vgaarb: VGA decodes changed: olddecodes=io+mem,decodes=io+mem:owns=io+mem
[    4.366914] i915 0000:00:02.0: [drm] Finished loading DMC firmware i915/adlp_dmc.bin (v2.20)
[    4.371743] i915 0000:00:02.0: [drm] GT0: GuC firmware i915/tgl_guc_70.bin version 70.36.0
[    4.371750] i915 0000:00:02.0: [drm] GT0: HuC firmware i915/tgl_huc.bin version 7.9.3
[    4.375760] i915 0000:00:02.0: [drm] GT0: HuC: authenticated for all workloads
[    4.376242] i915 0000:00:02.0: [drm] GT0: GUC: submission enabled
[    4.376244] i915 0000:00:02.0: [drm] GT0: GUC: SLPC enabled
[    4.376617] i915 0000:00:02.0: [drm] GT0: GUC: RC enabled
[    4.378631] mei_pxp 0000:00:16.0-fbf6fcf1-96cf-4e2e-a6a6-1bab8cbe36b1: bound 0000:00:02.0 (ops i915_pxp_tee_component_ops [i915])
[    4.378826] i915 0000:00:02.0: [drm] Protected Xe Path (PXP) protected content support initialized
[    4.378831] mei_hdcp 0000:00:16.0-b638ab7e-94e2-4ea2-a552-d1c54b627f04: bound 0000:00:02.0 (ops i915_hdcp_ops [i915])
[    4.444295] [drm] Initialized i915 1.6.0 20230929 for 0000:00:02.0 on minor 1
[    4.508802] snd_hda_intel 0000:00:1f.3: bound 0000:00:02.0 (ops i915_audio_component_bind_ops [i915])
[    4.514543] fbcon: i915drmfb (fb0) is primary device
[    4.514649] i915 [CRTC:80:pipe A] fastset requirement not met in dpll_hw_state
[    4.514652] i915 expected:
[    4.514653] i915 dpll_hw_state: cfgcr0: 0x1001d0, cfgcr1: 0x88, div0: 0x0, mg_refclkin_ctl: 0x0, hg_clktop2_coreclkctl1: 0x0, mg_clktop2_hsclkctl: 0x0, mg_pll_div0: 0x0, mg_pll_div2: 0x0, mg_pll_lf: 0x0, mg_pll_frac_lock: 0x0, mg_pll_ssc: 0x0, mg_pll_bias: 0x0, mg_pll_tdc_coldst_bias: 0x0
[    4.514655] i915 found:
[    4.514655] i915 dpll_hw_state: cfgcr0: 0x1001d0, cfgcr1: 0x488, div0: 0x0, mg_refclkin_ctl: 0x0, hg_clktop2_coreclkctl1: 0x0, mg_clktop2_hsclkctl: 0x0, mg_pll_div0: 0x0, mg_pll_div2: 0x0, mg_pll_lf: 0x0, mg_pll_frac_lock: 0x0, mg_pll_ssc: 0x0, mg_pll_bias: 0x0, mg_pll_tdc_coldst_bias: 0x0
[    4.514658] i915 [CRTC:80:pipe A] fastset requirement not met in infoframes.enable (expected 0x00000010, found 0x00000071)
[    4.683734] i915 0000:00:02.0: [drm] fb0: i915drmfb frame buffer device
[    4.688842] i915 display info: display version: 13
[    4.688854] i915 display info: display stepping: D0
[    4.688859] i915 display info: cursor_needs_physical: no
[    4.688862] i915 display info: has_cdclk_crawl: yes
[    4.688866] i915 display info: has_cdclk_squash: no
[    4.688869] i915 display info: has_ddi: yes
[    4.688872] i915 display info: has_dp_mst: yes
[    4.688874] i915 display info: has_dsb: yes
[    4.688877] i915 display info: has_fpga_dbg: yes
[    4.688880] i915 display info: has_gmch: no
[    4.688883] i915 display info: has_hotplug: yes
[    4.688885] i915 display info: has_hti: no
[    4.688887] i915 display info: has_ipc: yes
[    4.688887] i915 display info: has_overlay: no
[    4.688888] i915 display info: has_psr: yes
[    4.688889] i915 display info: has_psr_hw_tracking: no
[    4.688889] i915 display info: overlay_needs_physical: no
[    4.688890] i915 display info: supports_tv: no
[    4.688891] i915 display info: has_hdcp: yes
[    4.688891] i915 display info: has_dmc: yes
[    4.688892] i915 display info: has_dsc: yes
[    4.688892] i915 display info: rawclk rate: 19200 kHz
[    4.688948] i915 0000:00:02.0: 7 VFs could be associated with this PF
[    5.506941] i915 0000:00:02.0: vgaarb: VGA decodes changed: olddecodes=io+mem,decodes=none:owns=io+mem
               use xe.force_probe='46d0' and i915.force_probe='!46d0'
[    5.507018] i915 0000:00:02.1: enabling device (0000 -> 0002)
[    5.507040] i915 0000:00:02.1: [drm] Found ALDERLAKE_P/ADL-N (device ID 46d0) display version 13.00 stepping D0
[    5.507068] i915 0000:00:02.1: Running in SR-IOV VF mode
[    5.507652] i915 0000:00:02.1: [drm] GT0: GUC: interface version 0.1.17.0
[    5.508623] i915 0000:00:02.1: [drm] VT-d active for gfx access
[    5.508669] i915 0000:00:02.1: [drm] Using Transparent Hugepages
[    5.508928] i915 0000:00:02.1: [drm] GT0: GUC: interface version 0.1.17.0
[    5.509427] i915 0000:00:02.1: [drm] GT0: GUC: interface version 0.1.17.0
[    5.510115] i915 0000:00:02.1: GuC firmware PRELOADED version 0.0 submission:SR-IOV VF
[    5.510119] i915 0000:00:02.1: HuC firmware PRELOADED
[    5.513190] i915 0000:00:02.1: [drm] Protected Xe Path (PXP) protected content support initialized
[    5.513196] i915 0000:00:02.1: [drm] PMU not supported for this GPU.
[    5.513354] [drm] Initialized i915 1.6.0 20230929 for 0000:00:02.1 on minor 0
[    5.513653] i915 0000:00:02.0: vgaarb: VGA decodes changed: olddecodes=none,decodes=none:owns=io+mem
[    5.513656] i915 0000:00:02.1: vgaarb: VGA decodes changed: olddecodes=io+mem,decodes=none:owns=none
               use xe.force_probe='46d0' and i915.force_probe='!46d0'
...
[    5.537903] i915 0000:00:02.7: enabling device (0000 -> 0002)
[    5.537918] i915 0000:00:02.7: [drm] Found ALDERLAKE_P/ADL-N (device ID 46d0) display version 13.00 stepping D0
[    5.537942] i915 0000:00:02.7: Running in SR-IOV VF mode
[    5.538115] i915 0000:00:02.7: [drm] GT0: GUC: interface version 0.1.17.0
[    5.538625] i915 0000:00:02.7: [drm] VT-d active for gfx access
[    5.538648] i915 0000:00:02.7: [drm] Using Transparent Hugepages
[    5.538763] i915 0000:00:02.7: [drm] GT0: GUC: interface version 0.1.17.0
[    5.539078] i915 0000:00:02.7: [drm] GT0: GUC: interface version 0.1.17.0
[    5.539419] i915 0000:00:02.7: GuC firmware PRELOADED version 0.0 submission:SR-IOV VF
[    5.539422] i915 0000:00:02.7: HuC firmware PRELOADED
[    5.541114] i915 0000:00:02.7: [drm] Protected Xe Path (PXP) protected content support initialized
[    5.541119] i915 0000:00:02.7: [drm] PMU not supported for this GPU.
[    5.541217] [drm] Initialized i915 1.6.0 20230929 for 0000:00:02.7 on minor 7
[    5.541403] i915 0000:00:02.0: Enabled 7 VFs
```

- Enable Virtual GPU for `Windows 11` VM in Proxmox

Add a PCI device for `Windows 11` VM, and choose one Virtual GPU:

![Proxmox - Windows 11, PCI device](/img/Proxmox%20-%20Windows%2011,%20PCI%20device.png "Proxmox - Windows 11, PCI device")

Enable `Primary GPU` and `PCI Express` in options:

![Proxmox - Windows 11, Primary GPU and PCI Express](/img/Proxmox%20-%20Windows%2011,%20Primary%20GPU%20and%20PCI%20Express.png "Proxmox - Windows 11, Primary GPU and PCI Express")

Choose `none` in Display and `host` in Processors options for `Windows 11` VM:

![Proxmox - Windows 11, Display](/img/Proxmox%20-%20Windows%2011,%20Display.png "Proxmox - Windows 11, Display")

![Proxmox - Windows 11, Display none](/img/Proxmox%20-%20Windows%2011,%20Display%20none.png "Proxmox - Windows 11, Display none")

Start `Windows 11` VM, login with `Microsoft Remote Desktop` _https://apps.microsoft.com/detail/9wzdncrfj3ps_ and a Virtual GPU is available now. Run `Task Manager` and check CPU and GPU load:

![Proxmox - Windows 11, Performance](/img/Proxmox%20-%20Windows%2011,%20Performance.png "Proxmox - Windows 11, Performance")

- Enable Virtual GPU for `Ubuntu` VM in Proxmox

Ubuntu version 22.04.4 for desktop.

Add a PCI device for `Ubuntu` VM, and choose one Virtual GPU; Enable `Primary GPU` and `PCI Express` in options; Choose `none` in Display and `host` in Processors options:

![Proxmox - Ubuntu](/img/Proxmox%20-%20Ubuntu.png "Proxmox - Ubuntu")

Setup remote desktop connection to Ubuntu:

```
root@nucleus:~# apt install ubuntu-desktop

root@nucleus:~# apt install xrdp

root@nucleus:~# systemctl enable xrdp
```

![Proxmox - Remote Desktop](/img/Proxmox%20-%20Remote%20Desktop.png "Proxmox - Remote Desktop")

- Fix Remote Desktop audio over HDMI issue with the script, enable the sound redirection:

```
terrence@nucleus:~$ ./xrdp-installer-1.5.1.sh -s
```

then reboot VM.

![Proxmox - Ubuntu Remote Desktop](/img/Proxmox%20-%20Ubuntu%20Remote%20Desktop.png "Proxmox - Ubuntu Remote Desktop")

Now Audio device becomes xrdp input / output.


- Windows Server 2022

Windows Server 2022 is similar to Windows 11 setup in Proxmox. A few issues like **GPU**:

![Proxmox - Windows Server 2022, GPU](/img/Proxmox%20-%20Windows%20Server%202022,%20GPU.png "Proxmox - Windows Server 2022, GPU")

just disable GPU then enable it, it will work correctly.

And no sound after installation, but can enable **Windows Audio Service** and choose **Remote Audio**:

![Proxmox - Windows Server 2022, Sound](/img/Proxmox%20-%20Windows%20Server%202022,%20Sound.png "Proxmox - Windows Server 2022, Sound")

then audio over HDMI to remote desktop can work.

In addition, can setup User Auto Logon after Windows Server 2022 startup. And check Windows license by running:

```
PS C:\Users\Administrator> slmgr -dlv
```

Now can remote desktop access Ubuntu, Windows 11 and Windows Server 2022 VMs both run in Proxmox:

![Proxmox - Ubuntu and Windows](/img/Proxmox%20-%20Ubuntu%20and%20Windows.jpg "Proxmox - Ubuntu and Windows")

References
----------

- 最新保姆级 PVE 8 安装教程！虚拟机 PCIE 设备及 SR-IOV 核显直通，最多分配7个虚拟化单独核显！最强虚拟机！_https://v2rayssr.com/pve.html_
- PVE 8.1 下部署 Intel 集显虚拟化驱动 _https://zoe.red/2023/38.html_
- 定制 OVMF 固件实现 PVE 8 环境 UEFI 模式直通 Intel 核显并显示 BIOS 适用于 intel 6-13 代处理器 PVE 8 核显直通画面 Windows 10 and MacOS 系统画面显示教程，可以外接显示器 _https://imacos.top/2023/10/09/152/_
- PVE 下如何启用 PCI 直通显卡 GPU/iGPU/USB/声卡 AUDIO 等硬件直通教程 _https://imacos.top/2023/07/31/pci/_
- Enable & Using vGPU Passthrough _https://gist.github.com/scyto/e4e3de35ee23fdb4ae5d5a3b85c16ed3_
- Proxmox VE: Passthrough with Intel Integrated Graphics Card Alder Lake Architecture | vGPU, VT-d, SR-IOV _https://github.com/kamilllooo/Proxmox_
- Proxmox VE 9.0: Windows 11 vGPU (VT-d) Passthrough with Intel Alder Lake _https://www.derekseaman.com/2024/07/proxmox-ve-8-2-windows-11-vgpu-vt-d-passthrough-with-intel-alder-lake.html_
- Remote Desktop connection from Mac to Ubuntu _https://askubuntu.com/questions/893831/remote-desktop-connection-from-mac-to-ubuntu_
- xRDP – Easy install xRDP on Ubuntu 20.04,22.04,23.XX,24.04 (Script Version 1.5.1) _https://c-nergy.be/blog/?p=19814_
- Autologon User at Startup in Windows Server _https://jc-lan.org/2022/06/02/autologon-user-at-startup-in-windows-server/_
- Windows Server 2022 audio available but not playing _https://answers.microsoft.com/en-us/windowserver/forum/all/windows-server-2022-audio-available-but-not/eca821f1-7b4a-4293-8273-fda49dd0e9d4_
