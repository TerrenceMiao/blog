---
title: Setup Socks/Socks5 proxy and git repo via proxy
date: 2023-10-25 22:23:53
tags:
---

Host `ranger.local` has VPN connection which is granted with git repository.


- Setup Socks/Socks5 proxy

```
$ ssh-copy-id -i id_rsa.pub miaot@ranger.local

$ ssh -D 3128 miaot@ranger.local
```

- Configure `git` with Sock/Socks5 proxy

```
$ git config http.proxy 'socks5://localhost:3128'

$ cat .git/config
[user]
	name = Terrence Miao
	email = terrence.miao@paradise.net
    signingkey = EBCEB936
[http]
	proxy = socks5://localhost:3128
```

Then can access git repository via proxy both on command line and in UI client.
