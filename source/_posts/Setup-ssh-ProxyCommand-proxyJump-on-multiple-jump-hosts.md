---
title: Setup ssh ProxyCommand/proxyJump on multiple jump hosts
date: 2023-10-25 22:04:00
tags:
---

Setup `.ssh/config` file:

```
## Keeping SSH Sessions Alive
Host *
 ServerAliveInterval 15

Host jumphost
    Hostname ranger.local
    IdentityFile ~/.ssh/id_rsa
    User miaot

Host jumphost-npe
    Hostname jumphost-npe.paradise.net
    User miaot
    IdentityFile ~/.ssh/id_rsa
    ProxyCommand ssh -W %h:%p jumphost
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
    User miaot
    IdentityFile ~/.ssh/id_rsa.prod
    ProxyCommand ssh -W %h:%p jumphost
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
