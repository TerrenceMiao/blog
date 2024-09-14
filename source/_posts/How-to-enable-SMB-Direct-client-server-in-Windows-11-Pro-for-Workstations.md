---
title: How to enable SMB Direct client/server in Windows 11 Pro for Workstations
date: 2024-09-14 17:52:08
tags:
---


In `Windows 11 Pro Station`:

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

References
----------

- SMB Direct, _https://learn.microsoft.com/en-us/windows-server/storage/file-server/smb-direct_
