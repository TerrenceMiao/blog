---
title: Setup HTTPS access in Nexus Repository Manager OSS 3.x.x
date: 2017-01-28 13:20:47
tags:
---

## Generate Self Signed certificate

On a Mac at home, with Bigpond internet access. Full host name is *silencer.bigpond* and IP Address is *10.0.0.9*.

``` bash
terrence@Silencer /Applications/nexus-3.0.0-03/etc/ssl
00:13:05 𝜆 keytool -genkeypair -keystore keystore.jks -storepass changeit -keypass changeit -alias jetty -keyalg RSA -keysize 2048 -validity 5000 -dname "CN=*.bigpond, O=Sonatype, L=Unspecified, ST=Unspecified, C=AU" -ext "SAN=DNS:silencer.bigpond,IP:10.0.0.9" -ext "BC=ca:true"
```

OR run "nslookup 127.0.0.1" return full domain hostname e.g. "localhost.bigpond".

```bash
terrence@Silencer /Applications/nexus-3.0.0-03/etc/ssl
00:13:05 𝜆 keytool -genkeypair -keystore keystore.jks -storepass changeit -keypass changeit -alias jetty -keyalg RSA -keysize 2048 -validity 5000 -dname "CN=*.bigpond, O=Sonatype, L=Unspecified, ST=Unspecified, C=AU" -ext "SAN=DNS:localhost.bigpond,IP:127.0.0.1" -ext "BC=ca:true"
```

Now, with latest Nexus (version 3.2.0-01) you can use self-signed server certificate without specifying IP address.

```bash
terrence@Silencer /usr/local/nexus-3.2.0-01/etc/ssl
00:13:05 𝜆 keytool -genkeypair -keystore keystore.jks -storepass changeit -keypass changeit -alias jetty -keyalg RSA -keysize 2048 -validity 5000 -dname "CN=*.gateway, O=Sonatype, L=Unspecified, ST=Unspecified, C=AU" -ext "SAN=DNS:silencer.gateway" -ext "BC=ca:true"
```

## Enable HTTPS access

Change jetty-https.xml file:

``` bash
terrence@Silencer /Applications/nexus-3.0.0-03/etc
00:18:59 𝜆 diff jetty-https.xml jetty-https.xml.orig
25,26c25,26
<     <Set name="KeyStorePassword">changeit</Set>
<     <Set name="KeyManagerPassword">changeit</Set>
---
>     <Set name="KeyStorePassword">OBF:1v2j1uum1xtv1zej1zer1xtn1uvk1v1v</Set>
>     <Set name="KeyManagerPassword">OBF:1v2j1uum1xtv1zej1zer1xtn1uvk1v1v</Set>
28c28
<     <Set name="TrustStorePassword">changeit</Set>
---
>     <Set name="TrustStorePassword">OBF:1v2j1uum1xtv1zej1zer1xtn1uvk1v1v</Set>
```

Add SSL port and include *jetty-https.xml* in file:

``` bash
terrence@Silencer /Applications/nexus-3.0.0-03/etc
00:19:06 𝜆 diff org.sonatype.nexus.cfg org.sonatype.nexus.cfg.orig
3d2
< application-port-ssl=8444
5c4
< nexus-args=${karaf.etc}/jetty.xml,${karaf.etc}/jetty-http.xml,${karaf.etc}/jetty-https.xml,${karaf.etc}/jetty-http-redirect-to-https.xml,${karaf.etc}/jetty-requestlog.xml
---
> nexus-args=${karaf.etc}/jetty.xml,${karaf.etc}/jetty-http.xml,${karaf.etc}/jetty-requestlog.xml
```

Retrieve server's certificate

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
```

To get another Source Code Repository server's certificate

``` bash
𝜆 keytool -printcert -rfc -sslserver bitbucket.cd.paradise.org:443 > bitbucket.cd.paradise.org.pem
```

## Test

Restart Nexus and access: https://localhost:8444

## Note

* Use utility tool "KeyStore Explorer" add additional CA certificates into keystore.jks file, especially when Gradle / Maven output error like:

``` bash
> sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
```
