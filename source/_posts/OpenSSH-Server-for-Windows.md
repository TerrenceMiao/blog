---
title: OpenSSH Server for Windows
date: 2023-11-24 15:59:14
tags:
---

Want to run `OpenSSH` Server on Windows e.g. Windows 10. From Windows 10, it natively supports `OpenSSH`.

**NOTE:** The *beta* and *nightly build* of `OpenSSH` Server for Windows have a lot of runtime issues.

Check `OpenSSH` installation:

```shell
PS C:\ProgramData\ssh> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent
```

Install the missing `OpenSSH` Server:

```shell
PS C:\ProgramData\ssh> Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
>>

Path          :
Online        : True
RestartNeeded : False


PS C:\ProgramData\ssh> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : Installed
```

Check `OpenSSH` for Windows version, check Windows Operating System version:

```shell
PS C:\ProgramData\ssh> ((Get-Item (Get-Command sshd).Source).VersionInfo.FileVersion)
8.1.0.1

PS C:\ProgramData\ssh> ((Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows nt\CurrentVersion\" -Name ProductName).ProductName)
Windows 10 Enterprise
```

Check Windows Domain information:

```shell
PS C:\ProgramData\ssh> dsregcmd /status

+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : CORP
               Device Name : WINDOWS.corp.paradise.local
...
```

Check `OpenSSH` Server for Windows run as a service:

![OpenSSH SSH Server service](/img/OpenSSH%20SSH%20Server%20service.png "OpenSSH SSH Server service")

Make sure OpenSSH SSH Server firewall inbound rule allows **ALL** profiles:

![OpenSSH SSH Server firewall inbound rule](/img/OpenSSH%20SSH%20Server%20firewall%20inbound%20rule.png "OpenSSH SSH Server firewall inbound rule")

The default `C:\ProgramData\ssh\sshd_config` file doesn't work for Windows Domain users authentication, and does't support `.ssh\authorized_keys` public key authentication. Error **lookup_principal_name: User principal name lokup failed for user 'corp\darling'** in `OpenSSH` Server `C:\ProgramData\ssh\logs\ssd` log file. A work around solution is to comment out lines:

```
#Match Group administrators
#       AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys
```

A complete `sshd_config` example file:

```
# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

#Port 22
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

#HostKey __PROGRAMDATA__/ssh/ssh_host_rsa_key
#HostKey __PROGRAMDATA__/ssh/ssh_host_dsa_key
#HostKey __PROGRAMDATA__/ssh/ssh_host_ecdsa_key
#HostKey __PROGRAMDATA__/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
SyslogFacility LOCAL0
LogLevel DEBUG3

# Authentication:

#LoginGraceTime 2m
#PermitRootLogin prohibit-password
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10

#PubkeyAuthentication yes

# The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2
# but this is overridden so installations will only check .ssh/authorized_keys
AuthorizedKeysFile	.ssh/authorized_keys

#AuthorizedPrincipalsFile none

# For this to work you will also need host keys in %programData%/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes
#PermitEmptyPasswords no

# GSSAPI options
#GSSAPIAuthentication no

#AllowAgentForwarding yes
#AllowTcpForwarding yes
#GatewayPorts no
#PermitTTY yes
#PrintMotd yes
#PrintLastLog yes
#TCPKeepAlive yes
#UseLogin no
#PermitUserEnvironment no
#ClientAliveInterval 0
#ClientAliveCountMax 3
#UseDNS no
#PidFile /var/run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none

# override default of no subsystems
Subsystem	sftp	sftp-server.exe

# Example of overriding settings on a per-user basis
#Match User anoncvs
#	AllowTcpForwarding no
#	PermitTTY no
#	ForceCommand cvs server

#Match Group administrators
#       AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys
```

Now run ssh client and log on SSH Server:

```shell
$ sshpass -f ~/.ssh/windows.passwd ssh -l darling windows.local
Microsoft Windows [Version 10.0.19044.2965]
(c) Microsoft Corporation. All rights reserved.

corp\darling@WINDOWS C:\Users\darling>
```

References
==========

- The Ultimate Guide to Installing OpenSSH on Windows, _https://petri.com/the-ultimate-guide-to-installing-openssh-on-windows_
- Installing SFTP/SSH Server on Windows using OpenSSH, _https://winscp.net/eng/docs/guide_windows_openssh_server_