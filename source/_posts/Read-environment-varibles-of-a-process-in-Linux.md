---
title: Read environment variables of a process in Linux
date: 2021-04-30 22:44:39
tags:
---

When try to get the content of any /proc/*PID*/environ file in more readable format, you can:

```console
/proc/[pid]/environ
      This file contains the environment for the process.  The entries
      are separated by null bytes ('\0'), and there may be a null byte
      at the end.
```

A simple way is to apply `xargs -0 -L1 -a` on it:

* `-0` - read null-delimited lines,
* `-L1` - read one line per execution of command
* `-a` - file read lines from file

```console
# ps -aef
10101     3629  3589  0 Apr27 ?        00:00:00 /bin/bash bin/start
10101     3670  3629  0 Apr27 ?        00:00:00 /bin/bash bin/start-tomcat
10101     3671  3670  0 Apr27 ?        00:07:36 /usr/lib/jvm/java-11-amazon-corretto.x86_64/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/

# cat /proc/3629/environ
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/binHOSTNAME=27c44e8a5c7cJAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64HOME=/usr/local/tomcat

# xargs -0 -L1 -a /proc/3629/environ
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=27c44e8a5c7c
JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64
HOME=/usr/local/tomcat
```
