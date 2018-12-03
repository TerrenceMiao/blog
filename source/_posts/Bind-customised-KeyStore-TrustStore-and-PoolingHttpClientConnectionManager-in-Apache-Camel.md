---
title: Bind customised KeyStore,TrustStore and PoolingHttpClientConnectionManager in Apache Camel
date: 2018-12-03 18:54:16
tags:
---

In Apache HTTPClient 4.x, PoolingHttpClientConnectionManager and BasicHttpClientConnectionManager can be used as Connection Managers for HTTP Client. By default, above two Connection Managers load **cacerts** trust store, from $JRE_HOME/lib/security directory, not programmatically specified.

Since Apache Camel starts supporting HTTP4 Component, camel-http4 is using Apache HTTPClient 4.x. The following is a solution how to customise PoolingHttpClientConnectionManager and SSLContext to use specified KeyStore and TrustStore.

<script src="https://gist.github.com/TerrenceMiao/fefc0d10359146c3612fde33046b95d0.js"></script>

In **application.properties** file defined environment specified KeyStore and TrustStore:

``` Console
...
keystore.location=classpath:certificates/client-certs.jks
keystore.password=client123
truststore.location=classpath:certificates/trusted-cacerts.jks
truststore.password=changeit
...
```

Without Connection Managers like PoolingHttpClientConnectionManager and BasicHttpClientConnectionManager in Apache HTTPClient 4.x, Apache Camel Route can be written in this way, binding programmatically specified KeyStore and TrustStore.

<script src="https://gist.github.com/TerrenceMiao/ecdb7cb674887a7f82199ac880987224.js"></script>

References
----------

- Apache Camel HTTP4 Component, _http://camel.apache.org/http4.html_
- Java SSL with Multiple KeyStores, _http://codyaray.com/2013/04/java-ssl-with-multiple-keystores_
- Multiple Keystores in Apache Camel/HttpClient, _http://codyaray.com/2013/05/multiple-keystores-in-apache-camelhttpclient_
