---
title: How to enable SMB Multichannel in Windows 11
date: 2024-05-17 22:38:27
tags:
---

Network adapter requires to support **RSS (Receive Side Scaling)**.

![RSS (Receive Side Scaling)](/img/RSS%20-%20Receive%20Side%20Scaling.png "RSS (Receive Side Scaling)")

- Open PowerShell as administrator in Windows 11, run and enable SMB Multichannel (should be enabled by default):

```powershell
PS C:\> Set-SmbClientConfiguration -EnableMultiChannel $true

Confirm
Are you sure you want to perform this action?
Performing operation 'Modify' on Target 'SMB Client Configuration'.
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Check network interfaces which show "**RSS capable = True**":

```powershell
PS C:\> Get-SmbClientNetworkInterface

Interface Index RSS Capable RDMA Capable Speed   IpAddresses                               Friendly Name
--------------- ----------- ------------ -----   -----------                               -------------
17              True        False        20 Gbps {}                                        X710-1-WFP Native MAC Layer LightWeight Filter-0000
8               False       False        10 Gbps {}                                        X710-1
13              False       False        10 Gbps {}                                        X710-2
26              True        False        20 Gbps {fe80::923a:90de:dedd:ef44, 192.168.0.98} NIC-Team
```

- Verify there are any active SMB connections:

```powershell
PS C:\> Get-SmbConnection

ServerName ShareName UserName         Credential                               Dialect NumOpens
---------- --------- --------         ----------                               ------- --------
Synology   NAS Drive RIPTIDE\terrence MicrosoftAccount\terrence.miao@mail.net  3.1.1   2
```

- Copy a large file to a SMB device, e.g., Synology NAS which also has SMB Multichannel enabled, then verify the SMB Multichannel is working:

```powershell
PS C:\> Get-SmbMultichannelConnection -IncludeNotSelected

Server Name Selected Client IP    Server IP       Client Interface Index Server Interface Index Client RSS Capable Client RDMA Capable
----------- -------- ---------    ---------       ---------------------- ---------------------- ------------------ -------------------
Synology    True     192.168.0.98 192.168.0.112   26                     5                      False              False
Synology    False    192.168.0.98 192.168.0.34    26                     4                      False              False
Synology    False    192.168.0.98 192.168.196.140 26                     7                      False              False
```

`192.168.0.98` is Windows 11 network address, after Network Teaming; `192.168.0.112` and `192.168.0.34` are Synology NAS network addresses.


References
----------

- HOW TO: Enable SMB Multichannel for > 125 MB between Synology and Win 10, _https://www.reddit.com/r/synology/comments/rrb4r1/how_to_enable_smb_multichannel_for_125_mb_between/_