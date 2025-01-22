---
title: Xray, X-UI, Reality protocol, Vision flow on Amazon Linux
date: 2025-01-22 09:48:08
tags:
---

AWS EC2 instance, which runs `Amazon Linux`:

```
$ ssh -i .ssh/aws-free-tier.pem -l ec2-user ec2-203-129-56-97.ap-southeast-2.compute.amazonaws.com
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'

$ sudo -i
[root@ip-237-45-6-183 ~]#
```

Install `X-UI`:

```
[root@ip-237-45-6-183 ~]# bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install_en.sh)

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

Change AWS security group, and open EC2 instance port `10080` to admin user's IP address only.

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
根路径[rootPath]: /WMa7/ 
```

In `Firefox`, go to _http://203.129.56.97:10080/WMa7/_ and login as **admin** user:

![X-UI - Login](/img/X-UI%20-%20Login.png "X-UI - Login")

![X-UI - Panel](/img/X-UI%20-%20Panel.png "X-UI - Panel")

Switch to the latest version `Xray` e.g. `v24.12.31`:

![X-UI - Xray](/img/X-UI%20-%20Xray.png "X-UI - Xray")

Add new Inbound:

![X-UI - Inbound](/img/X-UI%20-%20Inbound.png "X-UI - Inbound")

Open up port **32609** to the world **0.0.0.0/0** in AWS security group.

Copy inbound QR link:

![X-UI - QR](/img/X-UI%20-%20QR.png "X-UI - QR")

![X-UI - QR Link](/img/X-UI%20-%20QR%20Link.png "X-UI - QR Link")

and paste to `Xray` Windows client e.g. `v2rayN`:

![X-UI - v2rayN](/img/X-UI%20-%20v2rayN.png "X-UI - v2rayN")

References
----------

- 无敌的 Xray – Reality 协议！Vless + Reality协议 + Vision流控，是否能够摆平一切顾虑！ _https://v2rayssr.com/reality.html_
- 波仔分享 - 无敌的 Xray - Reality 协议！无须域名和证书的 Reality 协议 + Vision 流控，是否能够摆平一切顾虑！Reality 指向网站寻找，Reality 客户端推荐（关联节点搭建/科学上网）_https://www.youtube.com/watch?v=DO1_C9YWOYY_
- x-ui _https://github.com/FranzKafkaYu/x-ui/_
- Qualys. SSL Labs _https://www.ssllabs.com/ssltest/index.html_
