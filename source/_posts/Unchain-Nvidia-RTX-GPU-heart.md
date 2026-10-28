---
title: Unchain Nvidia RTX GPU heart
date: 2026-10-28 15:41:56
tags:
---

Unlock Nvidia RTX 4080 GPU's all power potentials.

Install and confirm the stock NVIDIA driver first. Most test reports used NVIDIA's **open** kernel modules, so the `-open` package is the safest choice:
  
  ```sh
$ sudo ubuntu-drivers list
nvidia-driver-595-open, (kernel modules provided by linux-modules-nvidia-595-open-generic-hwe-26.04)
nvidia-driver-580-open, (kernel modules provided by linux-modules-nvidia-580-open-generic-hwe-26.04)
nvidia-driver-615-open, (kernel modules provided by linux-modules-nvidia-615-open-generic-hwe-26.04)
...

$ sudo apt install nvidia-driver-595-open # pick the version listed
```

![GPU - Driver Comparison](/img/GPU%20-%20Driver%20Comparison.png "GPU - Driver Comparison")

Find GPU's PCI address and card information:

```sh
$ lspci -Dnn | grep -i nvidia
0000:06:00.0 VGA compatible controller [0300]: NVIDIA Corporation AD103 [GeForce RTX 4080] [10de:2704] (rev a1)
0000:06:00.1 Audio device [0403]: NVIDIA Corporation AD103 High Definition Audio Controller [10de:22bb] (rev a1)

$ lspci -nnk -s 0000:06:00.0
06:00.0 VGA compatible controller [0300]: NVIDIA Corporation AD103 [GeForce RTX 4080] [10de:2704] (rev a1)
	Subsystem: ASUSTeK Computer Inc. Device [1043:88c8]
	Kernel driver in use: nvidia
	Kernel modules: nvidiafb, nouveau, nvidia_drm, nvidia
```

Install build dependencies:

```sh
$ sudo apt update
$ sudo apt install git build-essential dpkg-dev debhelper dkms dh-dkms python3 zstd linux-headers-$(uname -r)
```

Build and isntall the packages:

```sh
$ git clone https://github.com/JustAResearcher/gpu-unlock-ada.git

$ cd gpu-unlock-ada

$ make install 
```

This downloads the pinned NVIDIA **610.43.03** inputs, checks their SHA-256 hashes, generates the firmware locally, and builds and installs `gpu-unlock-dkms` and `gpu-unlock-firmware`.

The package's post-install step runs **update-initramfs -u -k all**, so the module and firmware go into the initramfs. Can confirm that:

```sh
$ sudo lsinitramfs /boot/initrd.img-$(uname -r) | grep -E 'gpu_unlock|gpu-unlock'
usr/lib/firmware/gpu-unlock
usr/lib/firmware/gpu-unlock/610.43.03
usr/lib/firmware/gpu-unlock/610.43.03/booter_load_ad102.bin
usr/lib/firmware/gpu-unlock/610.43.03/booter_load_ga100.bin
usr/lib/firmware/gpu-unlock/610.43.03/booter_load_ga102.bin
usr/lib/firmware/gpu-unlock/610.43.03/booter_load_tu102.bin
usr/lib/firmware/gpu-unlock/610.43.03/falcon_loader.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_ad102.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_desc_ad102.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_desc_ga100.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_desc_ga102.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_desc_tu102.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_ga100.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_ga102.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_boot_tu102.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_ga10x.bin
usr/lib/firmware/gpu-unlock/610.43.03/gsp_tu10x.bin
usr/lib/firmware/gpu-unlock/610.43.03/sec2_bl_tu102.bin
usr/lib/modules/7.0.0-38-generic/updates/dkms/gpu_unlock.ko.zst
```

Print the exact boot option:

```sh
$ python3 tools/print_boot_option.py
RTX 4080 (experimental) at 0000:06:00.0 [10de:2704]:
gpu_unlock.targets=0000:06:00.0 gpu_unlock.rtx4080_experimental=1
```

Add above options to GRUB: **GRUB_CMDLINE_LINUX_DEFAULT="quiet splash gpu_unlock.targets=0000:06:00.0 gpu_unlock.rtx4080_experimental=1**. Then appl the change:

```sh
$ sudo update-grub
```

Cold boot:

1. Shut down.
2. Switch off or unplug the AC power and wait about 30 seconds.
3. Power on.

The module needs a card that hasn't been initialised since power-on. If the card was already initialised, it refuses to run.

![GPU - MOK](/img/GPU%20-%20MOK.png "GPU - MOK")

Verify:

```sh
$ cat /proc/cmdline
BOOT_IMAGE=/boot/vmlinuz-7.0.0-38-generic root=UUID=0e66f11d-6081-4b8c-9321-821ea4c0a793 ro quiet splash gpu_unlock.targets=0000:06:00.0 gpu_unlock.rtx4080_experimental=1 crashkernel=2G-4G:320M,4G-32G:512M,32G-64G:1024M,64G-128G:2048M,128G-:4096M

$ journalctl -k -b --no-pager | grep -E 'gpu_unlock: unlocked|already unlocked|unlock failed|experimental|post-FLR SEC2'
Oct 27 22:47:22 Aorus kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-7.0.0-38-generic root=UUID=0e66f11d-6081-4b8c-9321-821ea4c0a793 ro quiet splash gpu_unlock.targets=0000:06:00.0 gpu_unlock.rtx4080_experimental=1 crashkernel=2G-4G:320M,4G-32G:512M,32G-64G:1024M,64G-128G:2048M,128G-:4096M
Oct 27 22:47:22 Aorus kernel: Kernel command line: BOOT_IMAGE=/boot/vmlinuz-7.0.0-38-generic root=UUID=0e66f11d-6081-4b8c-9321-821ea4c0a793 ro quiet splash gpu_unlock.targets=0000:06:00.0 gpu_unlock.rtx4080_experimental=1 crashkernel=2G-4G:320M,4G-32G:512M,32G-64G:1024M,64G-128G:2048M,128G-:4096M
Oct 27 22:47:23 Aorus kernel: pci 0000:06:00.0: post-FLR SEC2 reset released, GSP halted, BME=0 pending=0 WPR2=1ffffe00:00000000 SS0=88888888 SS1=00000008
Oct 27 22:47:23 Aorus kernel: pci 0000:06:00.0: gpu_unlock: unlocked

$ nvidia-smi
Tue Oct 27 22:50:13 2026
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 595.99.02              Driver Version: 595.99.02      CUDA Version: 13.2     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 4080        Off |   00000000:06:00.0 Off |                  N/A |
|  0%   26C    P8              4W /  360W |      14MiB /  16376MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A            2896      G   /usr/bin/gnome-shell                      3MiB |
+-----------------------------------------------------------------------------------------+

$ journalctl -k -b | grep -i xid # should print nothing
```

Success requires all three of these:

- A **gpu_unlock: unlocked** line for your card's address.
- A healthy `nvidia-smi`.
- A rate readback showing full rate:

```sh
$ UUID=$(nvidia-smi --query-gpu=uuid --format=csv,noheader)

$ python3 tools/read_sm_rates.py --expect-uuid "$UUID"
{
  "gpu": "GPU-28b6243c-d078-fbba-a6ff-ea24c62d4ccf, NVIDIA GeForce RTX 4080, 00000000:06:00.0, 595.99.02, 360.00, 210, 405",
  "command": "0x20801230",
  "rates": {
    "imla0": 0,
    "fmla16": 0,
    "dp": 0,
    "fmla32": 0,
    "ffma": 0,
    "imla1": 0,
    "imla2": 0,
    "imla3": 0,
    "imla4": 0
  },
  "meaning": "0=full; 1=half; 2=quarter; higher values are further reductions",
  "hardware_changes": false
}
```

Look for **fmla32** changing from **1** (stock) to **0** (unlocked).

Uninstall:

To roll back, remove the **gpu_unlock.*** options from **/etc/default/grub**, run **sudo update-grub**, and power-cycle. 

To remove the software completely:

```sh
$ sudo apt remove gpu-unlock-dkms gpu-unlock-firmware
```

![GPU - File Structure](/img/GPU%20-%20File%20Structure.png "GPU - File Structure")

![GPU - Sequence Diagram](/img/GPU%20-%20Sequence%20Diagram.png "GPU - Sequence Diagram")


References
----------

- GPU Unlock Ada _https://github.com/JustAResearcher/gpu-unlock-ada_
- The Origin of GPU Unlock _https://github.com/Zagitta/gpu-unlock_
