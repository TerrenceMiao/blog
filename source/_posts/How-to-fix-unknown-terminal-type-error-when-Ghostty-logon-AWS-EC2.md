---
title: How to fix 'unknown terminal type' error when Ghostty logon AWS EC2
date: 2026-04-14 15:19:12
tags:
---

Popular `Ghostty` Terminal _https://ghostty.org/_ from it to logon AWS EC2 instance, "**unknown terminal type**" thrown on EC2.

On `Ghostty` Terminal host:

```
$ infocmp -x > /tmp/ghostty.terminfo
```

Then copy `Ghostty` terminal info file across over to EC2 instance:

```
$ scp ghostty.terminfo ec2-user@i-0012f794e5fabcdef:/tmp
```

Logon EC2 instance, and run: 

```
[ec2-user@ip-10-215-240-19 ~]$ tic -x /tmp/ghostty.terminfo
"/tmp/ghostty.terminfo", line 2, col 31, terminal 'xterm-ghostty': older tic versions may treat the description field as an alias

[ec2-user@ip-10-215-240-19 ~]$ pwd
/home/ec2-user

[ec2-user@ip-10-215-240-19 ~]$ find .terminfo/
.terminfo/
.terminfo/x
.terminfo/x/xterm-ghostty
.terminfo/g
.terminfo/g/ghostty
```

### Why This Happens

Ghostty uses **TERM=xterm-ghostty** which requires a terminfo entry on the remote system. Older Linux servers/macOS servers simply don't have that entry in their terminfo database, so the other terminal utilities throw this "**unknown terminal type**" error.
