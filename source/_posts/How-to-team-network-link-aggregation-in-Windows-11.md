---
title: How to team network (link aggregation) in Windows 11
date: 2024-05-03 21:40:36
tags:
---

Intel Ethernet Converged Network Adapter X710, with two 10Gbps ports. This allows to team the two ports together for link aggregation.

- Install Optional Features `Server Manager` in Windows 11

![Server Manager](/img/Network%20Team%20-%20Server%20Manager.png "Server Manager")

- Open and run Windows Powershell as administrator, then run:

```powershell
PS C:\> New-NetSwitchTeam -Name "NIC-Team" -TeamMembers "X710-1","X710-2"
```

![Network Connections](/img/Network%20Team%20-%20Connections.png "Network Connections")

A new network interface created, with combined speed _**20Gbps**_.

![Network Status](/img/Network%20Team%20-%20Status.png "Network Status")

![Network Details](/img/Network%20Team%20-%20Details.png "Network Details")

To remove network team, run:

```powershell
PS C:\> Remove-NetSwitchTeam -Name "NIC-Team"
```