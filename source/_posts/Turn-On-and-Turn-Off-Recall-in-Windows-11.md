---
title: Turn On and Turn Off Recall in Windows 11
date: 2024-10-26 01:15:03
tags:
---

Turn On **Recall** feature in `Windows 11 24H2`, by running Widnows Power Shell as **Administrator**:

```
PS C:\> DISM /Online /Get-FeatureInfo /FeatureName:Recall

Deployment Image Servicing and Management tool
Version: 10.0.26100.1150

Image Version: 10.0.26100.2033

Feature Information:

Feature Name : Recall
Display Name : Recall
Description : Recall application.
Restart Required : Possible
State : Enabled

Custom Properties:

(No custom properties found)

The operation completed successfully.
```

Turn Off **Recall** in `Windows 11 24H2`:


```
PS C:\> DISM /Online /Disable-Feature /FeatureName:Recall

Deployment Image Servicing and Management tool
Version: 10.0.26100.1150

Image Version: 10.0.26100.2033

Disabling feature(s)
[==========================100.0%==========================]
The operation completed successfully.
```
