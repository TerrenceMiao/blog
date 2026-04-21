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

`Ghostty` uses **TERM=xterm-ghostty** which requires a terminfo entry on the remote system. Older Linux servers/macOS servers simply don't have that entry in their terminfo database, so the other terminal utilities throw this "**unknown terminal type**" error.

SAME issue also happens when run `asitop` in `Ghostty`. **Error** thrown:

```
'xterm-ghostty': unknown terminal type.
/opt/homebrew/Cellar/asitop/0.0.24/libexec/lib/python3.14/site-packages/blessed/terminal.py:186: UserWarning: Failed to setupterm(kind='xterm-ghostty'): setupterm: could not find terminal
```

This is because `asitop` (via the blessed library) doesn't recognize `Ghostty`'s terminal type **xterm-ghostty**.

Either run as:

```
TERM=xterm-256color sudo asitop
```

or, add an alias to the shell config (~/.zshrc or ~/.bashrc):

```
alias asitop='TERM=xterm-256color sudo asitop'
```

`Ghostty` uses **xterm-ghostty** as its **$TERM** value, which isn't in the terminfo database that `blessed` checks against. Falling back to **xterm-256color** gives it a fully compatible terminal description while keeping all the color/formatting support `asitop` needs.
