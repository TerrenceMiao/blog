---
title: >-
  A blocking connect() to mDNS-resolved IPv6 addresses fails with errno 0 on
  macOS 27
date: 2026-10-11 13:00:50
tags:
---

A blocking connect() to mDNS-resolved IPv6 addresses fails with errno 0 on `macOS 27`, while a non-blocking connect to the same addresses succeeds.

```
$ ssh -l terrence Aorus.local
ssh: connect to host aorus.local port 22: Undefined error: 0

$ type -a ssh
ssh is /usr/bin/ssh
```

However,

```
$ nslookup Aorus.local
Server:  192.168.2.1
Address: 192.168.2.1#53

Name:    Aorus.local
Address: 192.168.2.229

$ nc -vz Aorus.local 22
Connection to Aorus.local port 22 [tcp/ssh] succeeded!
```

`ssh` IP address works fine:

```
$ ssh -l terrence 192.168.2.229
```

`ssh` on hostname without domain also works:

```
$ ssh -l terrence Aorus
```

`ssh` via proxy also works:

```
$ ssh -o ProxyCommand='nc %h %p' -l terrence Aorus.local
```


**Why they differ**: `nslookup` queries router’s DNS server (192.168.2.1) directly and returns the IPv4 address. `ssh` doesn’t use DNS for .local names. It goes through the `macOS` system resolver, which uses **mDNS/Bonjour**. That path can return a different answer, for example an IPv6 link-local address (fe80::...) advertised by Aorus (avahi, it’s Linux).

And local router also resolves a plain **Aorus** name, since that goes through **unicast DNS** and avoids **mDNS** entirely.

`dscacheutil` queries on **Aorus.local** only IPv6 no IPv4 addresses returned:

```
$ dscacheutil -q host -a name Aorus.local
name: aorus.local
ipv6_address: fda5:9d5d: ...
ipv6_address: 2401:d002: ...
ipv6_address: fda5:9d5d: ...
ipv6_address: fda5:9d5d: ...
ipv6_address: 2401:d002: ...
ipv6_address: 2401:d002: ...
```

Connect via IPv6 works fine:

```
$ ping6 -c2 2401:d002: ...

$ ssh -l terrence fda5:9d5d: ...
```

To monitor TCP/IP traffic:

```
$ sudo tcpdump -i en1 -n 'ip6 and tcp port 22'
```

Finally, try **ConnectTimeout** in `ssh` works:

```
$ ssh -o ConnectTimeout=5 -l terrence Aorus.local
```

Add the following configuration in `.ssh/config` file:

```
Host Aorus.local
    ConnectTimeout 10
    User terrence
```

**ConnectTimeout** changes how `ssh` opens the socket. With no timeout set, `ssh` calls a plain blocking `connect()`. With a timeout, it switches the socket to non-blocking mode and waits for it with `poll()`. `nc` connects the non-blocking way too, which is why it worked all along.

So the evidence points to the blocking `connect()` to these mDNS-resolved IPv6 addresses failing on current macOS build, while the non-blocking path works. This could be a bug in this macOS build’s handling of mDNS-resolved scoped addresses, or something in the Local Network privacy layer.

Only the hostname path failed, because **Aorus.local** resolves to IPv6 addresses with an interface scope, and literals and the IPv4 name don't hit that.

**ConnectTimeout** is harmless, since it just makes unreachable hosts fail after 10 seconds instead of the system default. It also covers `scp`, `rsync` and `git` over `ssh`.
