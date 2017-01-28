---
title: Setup Docker Private Registry in Nexus Repository OSS 3.x.x
date: 2017-01-28 14:37:50
tags:
---

Make sure Nexus Repository has been setup with Self Signed certificate, certificate for host/server e.g. "silencer.bigpond".

The following instructions have been successfully tested in Nexus version *3.2.0-01*.

## Create Docker Hub repository in Nexus

![Docker Hub repository](https://raw.githubusercontent.com/TerrenceMiao/nexus/master/images/docker-hub%20repository.png)

## Create Docker Internal repository in Nexus

![Docker Internal repository](https://raw.githubusercontent.com/TerrenceMiao/nexus/master/images/docker-internal%20repository.png)

## Create Docker Group repository in Nexus

![Docker Group repository](https://raw.githubusercontent.com/TerrenceMiao/nexus/master/images/docker-group%20repository.png)

## Run Docker with Docker Native

#### Add Docker Private Registry in Insecure Registries

![Docker Native Insecure registries](https://raw.githubusercontent.com/TerrenceMiao/nexus/master/images/docker%20native%20-%20Insecure%20registries.png)

Now this approach supports *docker pull* and *docker push*.

Work around with "*x509: certificate signed by unknown authority*" error by adding "--disable-content-trust" option on *docker push* command line if Docker doesn't accept Self-Signed certificate.

#### Add Docker Private Registry server's certificate into Docker Virtual Machine CA list

``` bash
𝜆 keytool -printcert -rfc -sslserver silencer.bigpond:8444 > silencer.bigpond.pem
-----BEGIN CERTIFICATE-----
MIIDkDCCAnigAwIBAgIEAqo9kTANBgkqhkiG9w0BAQsFADBgMQswCQYDVQQGEwJBVTEUMBIGA1UE
CBMLVW5zcGVjaWZpZWQxFDASBgNVBAcTC1Vuc3BlY2lmaWVkMREwDwYDVQQKEwhTb25hdHlwZTES
MBAGA1UEAwwJKi5iaWdwb25kMB4XDTE2MDYxMjExMzY1M1oXDTMwMDIxOTExMzY1M1owYDELMAkG
A1UEBhMCQVUxFDASBgNVBAgTC1Vuc3BlY2lmaWVkMRQwEgYDVQQHEwtVbnNwZWNpZmllZDERMA8G
A1UEChMIU29uYXR5cGUxEjAQBgNVBAMMCSouYmlncG9uZDCCASIwDQYJKoZIhvcNAQEBBQADggEP
ADCCAQoCggEBAJNO5mDpBDQQ8n4t0P2z8ChWzIFQ3Pf+5U8x6P17O3WtKTfsbuRYobHYmas5tVdV
dnLIqpb4JV9DWIoS+CNG6cRLy3GIWWT7CbjsrpDlOTArslvk3KuzQ0dsZNflRfdd+ughI2LczehE
fhzPJzA+ZU8Am1CadM+VUa+T6MilFQMXpWfjND6BNnV+qr/MX1QQfSjiWt7oWBex0BB0VPv9ooBZ
UqO+8jk5fUY8wEIa/kqLUqIKGxIUx9BMQBwBJwDKZmK93DXSPvAFYbKQjj6/nbV9R1VWmR7fhkLG
+Ixlx5ld2dxv4+xvXmS8s4NanBtKZWUfEYVPp7gUF9HZoW9A1jcCAwEAAaNSMFAwDAYDVR0TBAUw
AwEB/zAhBgNVHREEGjAYghBzaWxlbmNlci5iaWdwb25khwQKAAAJMB0GA1UdDgQWBBQV3WTuC+GI
8lHtH0uL+kYqTG+vczANBgkqhkiG9w0BAQsFAAOCAQEAUwL+qnKVT0ENZEZnDjB+cjPfvkeWOD05
PrGUOn4YB4vllq2S6Cgfm0OaZ+vMt3KMXPf9pIgZ797jdPhOP/s5IVJItldky+u/Hk9gNtUwEjpg
l0MjhSm/PqxR5XoJdkYlvUdtq+PTrU5RU3v3GImeOmlI4mM5PaZ6OT8HC5VMX5s9RawBr/5EbJHR
M7EN8r3g4Y/2109YoHoiWAhnN6TC3RhmCoQqGOiPsS732KHUz3KqXVbq9VTRdA3dXqFj1cUSet1T
XTPisaiehffvbqYm2vrJ5WYgqCwb8TadDg66TToj080qvA8cXAF7qlA8pOImrbVOs7tdANSAs+AO
cqCkiA==
-----END CERTIFICATE-----

𝜆 screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty

Welcome to Moby alpha
Kernel 4.4.12-moby on an x86_64 (/dev/ttyS0)

moby login: root
Welcome to the Moby alpha, based on Alpine Linux.
moby:~#
```

Inside Docker Virtual Machine, follow these steps:

``` bash
$ sudo cat silencer.bigpond.pem >> /etc/ssl/certs/ca-certificates.crt
$ sudo /etc/init.d/docker restart
$ tail -f /var/lib/boot2docker/docker.log
```

## With Docker Toolbox

#### Add Docker Private Registry server's certificate into Docker Virtual Machine CA list

``` bash
𝜆 keytool -printcert -rfc -sslserver silencer.bigpond:8444 > silencer.bigpond.pem
𝜆 docker-machine ssh default
```

Inside Docker Virtual Machine, follow these steps:
``` bash
$ sudo cat silencer.bigpond.pem >> /etc/ssl/certs/ca-certificates.crt
$ sudo /etc/init.d/docker restart
$ tail -f /var/lib/boot2docker/docker.log
```

Press Ctrl + D and Ctrl + D to exist SCREEN program. Type "screen -r" to re-entry SCREEN program.

## Additional Certificates

Download 3rd party Repository server's certificate. Use tool like *KeyStore Explorer* to add 3rd party server certificates into existing *keystore.jks*. Due to a certificate chain that does not exist within the existing Java truststore, Java does not trust the certificate and fails to connect to the application.


## Test

``` bash
𝜆 docker login silencer.bigpond:18443
Username: admin
Password: admin123
Login Succeeded
```

``` bash
𝜆 docker search silencer.bigpond:18443/tomcat
𝜆 docker pull silencer.bigpond:18443/jtech/tomcat:latest
```

``` bash
𝜆 docker login silencer.bigpond:18444
Username: admin
Password: admin123
Login Succeeded
```

``` bash
𝜆 docker build -t jtech/camel-spring:latest .
𝜆 docker tag jtech/camel-spring silencer.bigpond:18444/jtech/camel-spring:latest
𝜆 docker push silencer.bigpond:18444/jtech/camel-spring:latest
```

``` bash
𝜆 docker push --disable-content-trust silencer.bigpond:18444/jtech/camel-spring:latest
```

## Note
* Due to Docker Virtual Machine is immutable, the CA certificate added change made inside VM is ephemeral, and lost after VM is restarted
* Docker Private Registry ONLY supports *HTTPS*, NOT HTTP
* Nexus repository MUST register and use server certificate for host e.g. "silencer.bigpond", NOT "localhost.bigpond" to run Docker Private Registry
* Try with "--disable-content-trust" if error like "Get https://silencer.gateway:18444/v1/_ping: x509: certificate signed by unknown authority"

## Reference
* Private Registry for Docker, _https://books.sonatype.com/nexus-book/3.0/reference/docker.html_
* Using Self-Signed Certificates with Nexus Repository Manager and Docker Daemon, _https://support.sonatype.com/hc/en-us/articles/217542177_
* SSL Certificate Guide, _https://support.sonatype.com/hc/en-us/articles/213465768_
* Setup HTTPS access Nexus Repository, _https://github.com/TerrenceMiao/nexus/wiki/Setup-HTTPS-access-Nexus-Repository-Manager-OSS-3.xx_
* Add Self Signed CA certificate into Docker's CA list, _https://github.com/klippx/inject-docker-certs_
* Adding Self Signed certificate in Docker Native for Mac, _https://forums.docker.com/t/adding-self-signed-certificates/9761_
