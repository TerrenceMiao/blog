---
title: Xray, X-UI, Reality protocol, Vision flow on Oracle Cloud
date: 2025-01-22 13:34:40
tags:
---

`Oracle Cloud` instance, which runs `Oracle Linux`:

```
$ ssh -i .ssh/id_rsa_ -l opc 19.214.86.113

$ sudo -i
[root@apocalypse ~]#
```

Install `X-UI`:

```
[root@apocalypse ~]# bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install_en.sh)

...

2025-01-21 23:05:50 (100 MB/s) - ‘/usr/bin/x-ui’ saved [25637/25637]

Install/update finished need to modify panel settings out of security
are you continue,if you type n will skip this at this time[y/n]: y
please set up your username: admin
your username will be: admin
please set up your password: password
your password will be: password
please set up the panel port: 10080
your panel port is: 10080
initializing,wait some time here...
set username and password success
account name and password set down!
set port 10080 successpanel port set down!
x-ui v0.3.4.4 install finished,it is working now...

x-ui control menu usages: 
----------------------------------------------
x-ui              - Enter     control menu
x-ui start        - Start     x-ui 
x-ui stop         - Stop      x-ui 
x-ui restart      - Restart   x-ui 
x-ui status       - Show      x-ui status
x-ui enable       - Enable    x-ui on system startup
x-ui disable      - Disable   x-ui on system startup
x-ui log          - Check     x-ui logs
x-ui update       - Update    x-ui 
x-ui install      - Install   x-ui 
x-ui uninstall    - Uninstall x-ui 
x-ui geo          - Update    geo  data
----------------------------------------------
```

Find the root path of `X-UI`:

```
[root@ip-237-45-6-183 ~]# x-ui

  x-ui control menu
  0. exit
————————————————
  1. install   x-ui
  2. update    x-ui
  3. uninstall x-ui
————————————————
  4. reset username
  5. reset panel
  6. reset panel port
  7. check panel info
————————————————
  8. start x-ui
  9. stop  x-ui
  10. restart x-ui
  11. check x-ui status
  12. check x-ui logs
————————————————
  13. enable  x-ui on system startup
  14. disable x-ui on system startup
————————————————
  15. enable bbr 
  16. issuse certs
  17. x-ui cron jobs
 
x-ui status: running
enable on system startup: yes
xray status: running

please input a legal number[0-16],input 7 for checking login info:7
[INF] 当前面板信息[current panel info]:
面板版本[version]: 0.3.4.4:20230717
用户名[username]: admin
密码[userpasswd]: password
监听端口[port]: 10080
根路径[rootPath]: /xui/ 
```

Add new Inbound:

![X-UI - Oracle Cloud Inbound](/img/X-UI%20-%20Oracle%20Cloud%20Inbound.png "X-UI - Oracle Cloud Inbound")

Open `X-UI` panel port and `VLESS` port in `Oracle Cloud` firewall:

```
[root@apocalypse ~]# firewall-cmd --zone=public --permanent --add-port=10080/tcp
success

[root@apocalypse ~]# firewall-cmd --zone=public --permanent --add-port=32854/tcp
success

[root@apocalypse ~]# firewall-cmd --reload
success
```

Open `X-UI` panel port and `VLESS` port in `Oracle Cloud` **Security List Ingress Rules**:

![X-UI - Oracle Cloud](/img/X-UI%20-%20Oracle%20Cloud.png "X-UI - Oracle Cloud")
