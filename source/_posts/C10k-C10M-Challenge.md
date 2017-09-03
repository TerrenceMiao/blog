---
title: C10k / C10M Challenge
date: 2017-09-03 14:38:45
tags:
---

C10k / C10M Challenge 挑战

History
-------

The term was coined in 1999 by Dan Kegel, citing the Simtel FTP host, cdrom.com, serving 10,000 clients at once over 1 gigabit per second Ethernet in that year. The term has since been used for the general issue of large number of clients, with similar numeronyms for larger number of connections, most recently C10M in the 2010s.

By the early 2010s millions of connections on a single commodity 1U server became possible: over 2 million connections (WhatsApp, 24 cores, using Erlang on FreeBSD), 10–12 million connections (MigratoryData, 12 cores, using Java on Linux).

C10k（concurrently handling 10k connections）是一个在 1999 年被提出来的技术挑战，如何在一颗 1GHz CPU，2G 内存，1Gbps 网络环境下，让单台服务器同时为 1 万个客户端提供 FTP 服务。而到了 2010 年后，随着硬件技术的发展，这个问题被延伸为 C10M，即如何利用 8 核心 CPU，64G 内存，在 10Gbps 的网络上保持 1000 万并发连接，或是每秒钟处理 100 万的连接。（两种类型的计算机资源在各自的时代都约为 1200 美元）。

C10k / C10M 问题则是从技术角度出发挑战软硬件极限。C10k / C10M 问题得解，成本问题和效率问题迎刃而解。

![Network data flow through kernel](https://wiki.linuxfoundation.org/images/1/1c/Network_data_flow_through_kernel.png "Network data flow through kernel")

References
----------

* C10k problem, https://en.wikipedia.org/wiki/C10k_problem
* The C10K problem, http://www.kegel.com/c10k.html
* The Secret To 10 Million Concurrent Connections - The Kernel Is The Problem Not The Solution, http://highscalability.com/blog/2013/5/13/the-secret-to-10-million-concurrent-connections-the-kernel-i.html
* Inside NGINX: How We Designed for Performance & Scale, https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/
* 架构师实践日｜从C10K到C10M高性能网络的探索与实践, http://blog.qiniu.com/archives/4941
