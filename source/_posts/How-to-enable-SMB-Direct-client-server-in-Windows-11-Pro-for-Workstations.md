---
title: How to enable SMB Direct client/server in Windows 11 Pro for Workstations
date: 2024-09-14 17:52:08
tags:
---

In `Windows 11 Pro Station`, a _Mellanox ConnectX-4 MCX455A-ECAT PCIe x16 3.0 100GBe VPI EDR IB network adatper_, goes to support **SMB Direct**, client and server side **SMB Multichannel** and **RDMA** (Remote Direct Memory Access):

![Windows 11 Pro for Workstations](/img/Windows%2011%20Pro%20for%20Workstations.png "Windows 11 Pro for Workstations")

Open Windows Terminal as Administrator.

Enable SMB Direct:

```
PS C:\> Enable-WindowsOptionalFeature -Online -FeatureName SMBDirect
Path          :
Online        : True
RestartNeeded : False
```

Enable SMB Multichannel on the client-side:

```
PS C:\> Set-SmbClientConfiguration -EnableMultiChannel $true
Confirm
Are you sure you want to perform this action?
Performing operation 'Modify' on Target 'SMB Client Configuration'.
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):

PS C:\> Get-SmbClientConfiguration
...
EnableMultiChannel                    : True
...
```

Enable SMB Multichannel on the server-side:

```
PS C:\> Set-SmbServerConfiguration -EnableMultiChannel $true
Confirm
Are you sure you want to perform this action?
Performing operation 'Modify' on Target 'SMB Server Configuration'.
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):

PS C:\> Get-SmbServerConfiguration
...
EnableMultiChannel                     : True
...
```

Enable RDMA for a specific interface:

```
PS C:\> Enable-NetAdapterRDMA Mellanox
```

Verify which state of operability SMB Direct is currently configured to:

```
PS C:\> Get-WindowsOptionalFeature -Online -FeatureName SMBDirect
FeatureName      : SmbDirect
DisplayName      : SMB Direct
Description      : Remote Direct Memory Access (RDMA) support for the SMB 3.x file sharing protocol
RestartRequired  : Possible
State            : Enabled
CustomProperties :
```

```
PS C:\> Get-SmbClientNetworkInterface
Interface Index RSS Capable RDMA Capable Speed       IpAddresses                                 Friendly Name
--------------- ----------- ------------ -----       -----------                                 -------------
22              True        True         100 Gbps    {fe80::708:c529:1bcb:2432, 192.168.68.67}   Mellanox

PS C:\> Get-SmbServerNetworkInterface
Scope Name Interface Index RSS Capable RDMA Capable Speed    IpAddress
---------- --------------- ----------- ------------ -----    ---------
*          22              True        True         100 Gbps fe80::708:c529:1bcb:2432
*          22              True        True         100 Gbps 192.168.68.67
```

Have a look `TrueNAS` disk speed benchmark, over a 100Gbps ethernet network, from `Windows 11 Pro for Workstations` with **SMB Direct**, client/server **SMB Multichannel** and **RDMA** enabled:

![TrueNAS disk speed benchmark](/img/TrueNAS%20disk%20speed%20benchmark.png "TrueNAS disk speed benchmark")

In `Windows Server 2022`, with SMB shared folder in **Storage Spaces**:

![Windows Server 2022](/img/Windows%20Server%202022.png "Windows Server 2022")

Run Windows Powershell as **Administrator** user , which **RDMA Capable** are all True for both SMB client/server:

```
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.
Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Users\Administrator> Get-SmbServerNetworkInterface
Scope Name Interface Index RSS Capable RDMA Capable Speed    IpAddress
---------- --------------- ----------- ------------ -----    ---------
*          5               True        True         100 Gbps fe80::9ee0:7f4c:5128:863b
*          5               True        True         100 Gbps 192.168.68.66

PS C:\Users\Administrator> Get-SmbClientNetworkInterface
Interface Index RSS Capable RDMA Capable Speed    IpAddresses                                Friendly Name
--------------- ----------- ------------ -----    -----------                                -------------
5               True        True         100 Gbps {fe80::9ee0:7f4c:5128:863b, 192.168.68.66} Mellanox
```

Have a look `Windows Server 2022` disk speed benchmark, over a 100Gbps ethernet network, from `Windows 11 Pro for Workstations` with **SMB Direct**, client/server **SMB Multichannel** and **RDMA** enabled:

![Windows Server 2022 disk speed benchmark](/img/Windows%20Server%202022%20disk%20speed%20benchmark.png "Windows Server 2022 disk speed benchmark")

References
----------

- SMB Direct, _https://learn.microsoft.com/en-us/windows-server/storage/file-server/smb-direct_
- 100 Gbe & TrueNAS Scale 23.10 iSCSI - Performance Unleashed, _https://forum.level1techs.com/t/100-gbe-truenas-scale-23-10-iscsi-performance-unleashed/206452_
