---
title: Increase, decrease and resize Proxmox disk volume
date: 2024-08-07 13:29:22
tags:
---

After a default installation, a tiny disk space given to `Proxmox` root volume. Insufficient disk space issue raised up after several VMs installed and backup made, as iinstallation image files `*.ISO` and backup all put into root volume.

In `storage.cfg`:

```
root@pve:~# cat /etc/pve/storage.cfg
dir: local
        path /var/lib/vz
        content iso,vztmpl,backup

lvmthin: local-lvm
        thinpool data
        vgname pve
        content rootdir,images
```

Run `lvdisplay`:

```
root@pve:~# lvdisplay
  --- Logical volume ---
  LV Name                data
  VG Name                pve
  # open                 0
  LV Size                <3.58 TiB

  --- Logical volume ---
  LV Path                /dev/pve/swap
  LV Name                swap
  VG Name                pve
  LV Status              available
  # open                 2
  LV Size                8.00 GiB

  --- Logical volume ---
  LV Path                /dev/pve/root
  LV Name                root
  VG Name                pve
  LV Status              available
  # open                 1
  LV Size                <112.25 GiB
```

Solution is to decrease the size of `pve/data` volume, as this volume doesn't support reducing thin pools in size yet, and then to increase the size of `pdev/root` volume.

Backup all VMs, then remove `pve/data` volume:

```
root@pve:~# lvremove pve/data
Removing pool pve/data will remove 7 dependent volume(s). Proceed? [y/n]: y
```

Based on the disk space has just released, increase the size of `pdev/root` volume, **20%** for current FREE space in this case:

```
root@pve:~# lvextend -l +20%FREE /dev/pve/root
  Size of logical volume pve/root changed from <112.25 GiB (28735 extents) to <851.09 GiB (217878 extents).
  Logical volume pve/root successfully resized.

root@pve:~# resize2fs /dev/pve/root
resize2fs 1.47.0 (5-Feb-2023)
Filesystem at /dev/pve/root is mounted on /; on-line resizing required
old_desc_blocks = 15, new_desc_blocks = 107
The filesystem on /dev/pve/root is now 223107072 (4k) blocks long.
```

Create a new `pve/data` volume:

```
root@pve:~# lvcreate -L2920G -ndata pve
  Logical volume "data" created.
```

Check free disk space:

```
root@pve:~# vgdisplay
  --- Volume group ---
  VG Name               pve
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  126
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                3
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <3.73 TiB
  PE Size               4.00 MiB
  Total PE              976498
  Alloc PE / Size       967446 / 3.69 TiB
  Free  PE / Size       9052 / <35.36 GiB
  VG UUID               UEsIZR-TBsz-UYlP-u2FO-2AbC-uq4d-vcb35f
```

Create thin pool volume of the metadata, usually size of 1% of `pve/data` volume:

```
root@pve:~# lvconvert --type thin-pool --poolmetadatasize 36G pve/data
  Reducing pool metadata size 36.00 GiB to maximum usable size <15.88 GiB.
  Thin pool volume with chunk size 64.00 KiB can address at most <15.88 TiB of data.
  WARNING: Converting pve/data to thin pool's data volume with metadata wiping.
  THIS WILL DESTROY CONTENT OF LOGICAL VOLUME (filesystem etc.)
Do you really want to convert pve/data? [y/n]: y
  Converted pve/data to thin pool.
```

Verify current disk volumes, `pve/root` disk volume has more space now:

```
root@pve:~# df -h
Filesystem            Size  Used Avail Use% Mounted on
udev                   16G     0   16G   0% /dev
tmpfs                 3.2G  1.8M  3.2G   1% /run
/dev/mapper/pve-root  841G   71G  736G   9% /
tmpfs                  16G   46M   16G   1% /dev/shm
tmpfs                 5.0M     0  5.0M   0% /run/lock
efivarfs               192K  114K   74K  61% /sys/firmware/efi/efivars
/dev/nvme3n1p2       1022M   12M 1011M   2% /boot/efi
/dev/fuse             128M   20K  128M   1% /etc/pve
tmpfs                 3.2G     0  3.2G   0% /run/user/0
```

```
root@pve:~# lsblk
NAME                         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                            8:0    0 223.6G  0 disk
├─sda1                         8:1    0   100M  0 part
├─sda2                         8:2    0    16M  0 part
├─sda3                         8:3    0 120.3G  0 part
├─sda4                         8:4    0   625M  0 part
└─sda5                         8:5    0 102.5G  0 part
nvme2n1                      259:0    0   3.6T  0 disk
nvme4n1                      259:1    0   3.6T  0 disk
nvme0n1                      259:2    0   3.6T  0 disk
nvme1n1                      259:3    0   3.6T  0 disk
nvme5n1                      259:4    0   3.6T  0 disk
nvme3n1                      259:5    0   3.7T  0 disk
├─nvme3n1p1                  259:6    0  1007K  0 part
├─nvme3n1p2                  259:7    0     1G  0 part /boot/efi
└─nvme3n1p3                  259:8    0   3.7T  0 part
  ├─pve-swap                 252:0    0     8G  0 lvm  [SWAP]
  ├─pve-root                 252:1    0 854.7G  0 lvm  /
  ├─pve-data_tmeta           252:2    0  15.9G  0 lvm
  │ └─pve-data-tpool         252:4    0   2.9T  0 lvm
  │   ├─pve-data             252:5    0   2.9T  1 lvm
  │   ├─pve-vm--100--disk--0 252:6    0   240G  0 lvm
  │   ├─pve-vm--101--disk--0 252:7    0     4M  0 lvm
  │   ├─pve-vm--101--disk--1 252:8    0   240G  0 lvm
  │   ├─pve-vm--101--disk--2 252:9    0     4M  0 lvm
  │   ├─pve-vm--102--disk--0 252:10   0     4M  0 lvm
  │   ├─pve-vm--102--disk--1 252:11   0   240G  0 lvm
  │   └─pve-vm--102--disk--2 252:12   0     4M  0 lvm
  └─pve-data_tdata           252:3    0   2.9T  0 lvm
    └─pve-data-tpool         252:4    0   2.9T  0 lvm
      ├─pve-data             252:5    0   2.9T  1 lvm
      ├─pve-vm--100--disk--0 252:6    0   240G  0 lvm
      ├─pve-vm--101--disk--0 252:7    0     4M  0 lvm
      ├─pve-vm--101--disk--1 252:8    0   240G  0 lvm
      ├─pve-vm--101--disk--2 252:9    0     4M  0 lvm
      ├─pve-vm--102--disk--0 252:10   0     4M  0 lvm
      ├─pve-vm--102--disk--1 252:11   0   240G  0 lvm
      └─pve-vm--102--disk--2 252:12   0     4M  0 lvm

root@pve:~# lvs
  LV            VG  Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  data          pve twi-aotz--    2.85t             3.19   0.32
  root          pve -wi-ao---- <854.69g
  swap          pve -wi-ao----    8.00g
  vm-100-disk-0 pve Vwi-a-tz--  240.00g data        10.38
  vm-101-disk-0 pve Vwi-a-tz--    4.00m data        14.06
  vm-101-disk-1 pve Vwi-a-tz--  240.00g data        19.50
  vm-101-disk-2 pve Vwi-a-tz--    4.00m data        1.56
  vm-102-disk-0 pve Vwi-a-tz--    4.00m data        14.06
  vm-102-disk-1 pve Vwi-a-tz--  240.00g data        8.98
  vm-102-disk-2 pve Vwi-a-tz--    4.00m data        1.56
```

References
----------

- Reduce size of local-lvm _https://forum.proxmox.com/threads/reduce-size-of-local-lvm.78676/_