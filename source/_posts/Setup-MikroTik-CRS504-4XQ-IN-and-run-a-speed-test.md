---
title: Setup MikroTik CRS504-4XQ-IN and run a speed test
date: 2024-09-02 14:17:37
tags:
---

`MikroTik CRS504-4XQ-IN`, the Cloud Switch can handle FOUR QSFP28 100Gbps ports, equal to 16 x 25Gbps bandwidth.

![MikroTik - Interfaces](/img/MikroTik%20-%20Interfaces.png "MikroTik - Interfaces")

Setup single link mode, only the first QSFP28 sub-interface needs to be configured, while the remaining three sub-interfaces should remain enabled. For example, connect `Mellanox MCX455A-ECAT ConnectX-4 InfiniBand/Ethernet adapter card (EDR IB 100Gbps and 100GbE, single-port QSFP28, PCIe 3.0x16)` using `ONTi DAC QSFP28 100Gbps` cable to the switch.

Change **FEC Mode** to `fec91`.

_Ethernet Forward Error Correction (FEC) is a technique used to improve the reliability of data transmission over Ethernet networks by detecting and correcting errors in data packets. The two most common types of FEC used in Ethernet networks are CL74 and CL91._

_CL74 and CL91 refer to two different types of FEC codes, each with its own characteristics and performance. Here's a brief comparison between the two:_

_Code Rate:_

_CL91 has a higher code rate of 91.6%, which means that only 8.4% of the data transmitted is used for error correction._

In addition, setup the swith port connected to `ONTi QSFP28 40Gbps TO 4SFP+` breakout cable:

```
$ ssh -l admin MikroTik.local

[admin@MikroTik] > /interface ethernet set qsfp28-1-1 auto-negotiation=no speed=10G-baseCR
[admin@MikroTik] > /interface ethernet set qsfp28-1-2 auto-negotiation=no speed=10G-baseCR
[admin@MikroTik] > /interface ethernet set qsfp28-1-3 auto-negotiation=no speed=10G-baseCR
[admin@MikroTik] > /interface ethernet set qsfp28-1-4 auto-negotiation=no speed=10G-baseCR
```

Speed test
----------

In `iperf3` server, run listens to 4 ports to manage connections in parallel:

```
$ iperf3 -s -p 5201 & iperf3 -s -p 5202 & iperf3 -s -p 5203 & iperf3 -s -p 5204 &
```

In a MacBook Pro with WiFi-6 connection, run:

```
$ iperf3 -c MikroTik.local -p 5201 -P 4 -t 1000
```

In a Mac Studio with 10Gbps Ethernet connection, run:

```
$ iperf3 -c MikroTik.local -p 5202 -P 8 -t 1000 -B 192.168.0.104
```

In a Windows 11 PC with 100Gbps Ethernet connection, run:

```
$ iperf3 -c MikroTik.local -p 5203 -P 2 -t 1000
```

Check the speed on switch console:

![MikroTik - Speed](/img/MikroTik%20-%20Speed.png "MikroTik - Speed")

and in graph:

![MikroTik - Performance](/img/MikroTik%20-%20Performance.png "MikroTik - Performance")


References
----------

- MikroTik wired interface compatibility, _https://help.mikrotik.com/docs/display/ROS/MikroTik+wired+interface+compatibility_
- MikroTik RouterOS, _https://help.mikrotik.com/docs/display/ROS/RouterOS_
- MikroTik CRS504-4XQ-IN Quick Guide, _https://help.mikrotik.com/docs/display/QG/Quick+Guide+-+CRS504-4XQ-IN_
- FEC (Forward Error Correction), _https://www.reddit.com/r/fortinet/comments/127z3wd/fec_forward_error_correction/_