---
title: Fix network object name already existed issue in Windows
date: 2024-09-15 21:18:03
tags:
---

When rename a network adapter in `Windows`:

```
PS C:\> Rename-NetAdapter -Name Ethernet -NewName Mellanox
```

an error **Rename-NetAdapter : {Object Exists} An attempt was made to create an object and the object name already existed** thrown.

Work around solution is:

1. Open **Device Manager** in Windows Control Panel
2. Under menu **View** enable **Show hidden devices**
3. Uninstall the old network adapter with the old name
4. Then rename the network adapter again

![Rename Network in Windows](/img/Rename%20Network%20in%20Windows.png "Rename Network in Windows")


References
----------

- Registry mismatch Get-NetAdapter, _https://stackoverflow.com/questions/42220423/registry-mismatch-get-netadapter_
