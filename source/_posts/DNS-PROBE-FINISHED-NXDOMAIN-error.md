---
title: DNS_PROBE_FINISHED_NXDOMAIN error
date: 2026-04-21 10:41:11
tags:
---

From time to time, not always, getting **DNS_PROBE_FINISHED_NXDOMAIN** error in browser especially when visit Chinese AI sites e.g. https://chat.qwen.ai/ and https://chat.z.ai/

The root reason is because some ISPs have slower or inconsistent DNS resolution. 

The fix is switching to a faster public resolver like Google (8.8.8.8/8.8.4.4) or Cloudflare (1.1.1.1/1.0.0.1) in network settings.

This is how to change DNS settings in `OpenWRT`.

![](/img/OpenWRT%20-%20DNS%20Forwards.png "OpenWRT - DNS Forwards")
