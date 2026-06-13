---
title: Running remote X Window applications locally on Mac
date: 2026-06-13 20:26:04
tags:
---

On Mac, install `XQuartz` from _https://www.xquartz.org/_

On remote Linux host **Aorus.local**, make sure:

- $DISPLAY

```
$ echo $DISPLAY
localhost:10.0
```

- $XAUTHORITY

```
$ echo $XAUTHORITY
/home/terrence/.Xauthority

$ ls -al /home/terrence/.Xauthority
-rw------- 1 terrence terrence 51 Jun 13 20:11 /home/terrence/.Xauthority
```

- sshd_config

```
$ grep -iE 'X11Forwarding|X11UseLocalhost|XAuthLocation' /etc/ssh/sshd_config
X11Forwarding yes
X11UseLocalhost yes

$ sudo systemctl restart sshd
```

- xauth

```
$ which xauth
/usr/bin/xauth

$ xauth list
Aorus/unix:10  MIT-MAGIC-COOKIE-1  27e55a1edf7347d27957e71637f99ab2

$ xauth add localhost:10 MIT-MAGIC-COOKIE-1 27e55a1edf7347d27957e71637f99ab2
```

Then on Mac run:

```
$ ssh -Y Aorus.local
```

On Aorus.local:

```
$ firefox
```
