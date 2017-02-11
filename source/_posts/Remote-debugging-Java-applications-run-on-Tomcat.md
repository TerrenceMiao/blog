---
title: Remote debugging Java applications run on Tomcat
date: 2017-02-11 13:52:26
tags:
---

Enable JVM option to attach a remote debugger:

``` bash
$ export JAVA_OPTS=-agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=5005
```

"suspend" set to "y" is to let remote debugger start loading the application. 

Now, start running Tomcat. JVM debugging port is bound on port 5005i on the machine runs on Tomcat. 

Next, set up SSH tunnel mirror remotei host (ip-10-213-79-77.ap-southeast-2.compute.internal) 5005 port to localhost on port 5005. For example:

``` bash
$ ssh -L 5005:ip-10-213-79-77.ap-southeast-2.compute.internal:5005 -l ec2-user ip-10-213-79-77.ap-southeast-2.compute.internal
```

You can start remote debugging in IDE like IntelliJ and debug the code since.

