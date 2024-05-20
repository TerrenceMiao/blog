---
title: The solution making old Intel 10Gbps network adapter work in Windows 11
date: 2024-05-20 22:27:09
tags:
---

Buy some old Intel 10Gbps network adapter, `X520`, `X540` ... from AliExpress _https://aliexpress.com/_, and install old Intel network adapter driver for Windows 10 and make it working in Windows 11. The example is install version 25.0 Intel network adapter driver, _https://www.intel.com/content/www/us/en/download/18293/29648/intel-network-adapter-driver-for-windows-10.html_, to get it to work in Windows 11:

- Intel network adapters supported Operating Systems, _https://www.intel.com/content/www/us/en/support/articles/000025890/ethernet-products.html_

- Buy dual ports 10Gbps network adapter

- Install the network adapter in PCIE slot directly to the CPU

- Enable SMB Multichannel

- Enable Jumbo Frames

<iframe width="560" height="315" src="https://www.youtube.com/embed/WDPblqez00s?si=fs4-wswGT5AzDWMK&amp;start=222" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
