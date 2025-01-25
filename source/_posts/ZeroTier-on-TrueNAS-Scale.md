---
title: ZeroTier on TrueNAS Scale
date: 2025-01-25 13:01:25
tags:
---

Step by steyp to install `ZeroTier` on `TrueNAS Scale`.

Latest version `TrueNAS Scale` **ElectricEel 24.10**:

![ZeroTier - TrueNAS](/img/ZeroTier%20-%20TrueNAS.png "ZeroTier - TrueNAS")

Add `ZeroTier` **Network ID**; enable **Host Network**; leave **Auth Token**, **Identity Public** and **Identity Secret** empty:

![ZeroTier - Configuration](/img/ZeroTier%20-%20Configuration.png "ZeroTier - Configuration")

On `ZeroTier` console, authorise `TrueNAS` access the network:

![ZeroTier - Network](/img/ZeroTier%20-%20Network.png "ZeroTier - Network")

`ZeroTier` is happily running in `TrueNAS`:

![ZeroTier - Running](/img/ZeroTier%20-%20Running.png "ZeroTier - Running")

References
----------

- How to install zerotier on TrueNas Scale? _https://www.reddit.com/r/truenas/comments/1dvdp36/how_to_install_zerotier_on_truenas_scale/_