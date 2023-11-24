---
title: Setup Socks/Socks5 proxy and git repo via proxy
date: 2023-10-25 22:23:53
tags:
---

Host `windows.local` has VPN connection which is granted with git repository.


- Setup Socks/Socks5 proxy

```shell
$ ssh-copy-id -i id_rsa.pub darling@windows.local

$ ssh -D 3128 darling@windows.local
```

If `PasswordAuthentication` is enforced, and `pubilc key authentication` in SSH Server is not supported, try:

```shell
$ sshpass -f ~/.ssh/windows.passwd ssh -D 3128 darling@windows.local
```

- Configure `git` with Sock/Socks5 proxy

```shell
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
