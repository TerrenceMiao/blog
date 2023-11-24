---
title: Setup ssh ProxyCommand/proxyJump on multiple jump hosts
date: 2023-10-25 22:04:00
tags:
---

**NOTE:** Some SSH Server doesn't allow **public key authentication**. Then `sshpass` is a friend here for you.

Install `sshpass` in MacOS:

```shell
$ brew install esolitos/ipa/sshpass
```

Test `sshpass`:

```
$ ssh -oProxyCommand="sshpass -f ~/.ssh/windows.passwd ssh -W %h:%p jumphost" -l darling jumphost-npe.paradise.net
```

Setup `.ssh/config` file:

```
## Keeping SSH Sessions Alive
Host *
 ServerAliveInterval 15

Host jumphost.mac
    Hostname mac.local
    IdentityFile ~/.ssh/id_rsa
    User darling

Host jumphost.windows
    Hostname windows.local
    IdentityFile ~/.ssh/id_rsa
    User darling

Host jumphost-npe
    Hostname jumphost-npe.paradise.net
    User darling
    IdentityFile ~/.ssh/id_rsa
    ProxyCommand sshpass -f ~/.ssh/windows.passwd ssh -W %h:%p jumphost.windows
    IdentitiesOnly yes
    StrictHostKeyChecking no
    UserKnownHostsFile=/dev/null
    ServerAliveInterval 60
    ServerAliveCountMax 5

## DEVELOPMENT hosts in AWS
Host ip-10-212-*.ap-southeast-2.compute.internal
    ProxyCommand ssh -W %h:%p jumphost-npe
    IdentityFile ~/.ssh/dev-stack.pem

## PTEST hosts in AWS
Host ip-10-213-*.ap-southeast-2.compute.internal
    ProxyCommand ssh -W %h:%p jumphost-npe
    IdentityFile ~/.ssh/test-stack.pem

## STEST hosts in AWS
Host ip-10-214-*.ap-southeast-2.compute.internal
    ProxyCommand ssh -W %h:%p jumphost-npe
    IdentityFile ~/.ssh/test-stack.pem

Host jumphost-prod
    HostName jumphost-prod.paradise.net
    User darling
    IdentityFile ~/.ssh/id_rsa.prod
    ProxyCommand sshpass -f ~/.ssh/windows.passwd ssh -W %h:%p jumphost.windows
    IdentitiesOnly yes
    StrictHostKeyChecking no
    UserKnownHostsFile=/dev/null
    ServerAliveInterval 60
    ServerAliveCountMax 5

## PROD hosts in AWS
Host ip-10-208-*.ap-southeast-2.compute.internal
    ProxyCommand ssh -W %h:%p jumphost-prod
    IdentityFile ~/.ssh/prod-ddc-stack.pem

## SSH over Session Manager
host i-* mi-*
    ProxyCommand sh -c "aws ssm start-session --target %h --document-name AWS-StartSSHSession --parameters 'portNumber=%p'"
```
