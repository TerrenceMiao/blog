---
title: AWS access key id and secret access key
date: 2018-03-10 10:28:06
tags:
---

There are some secret of AWS credentials, i.e., its Access Key ID and Secret Access Key, which let you connect AWS services withouth authentication.

In a quintessential project environment, you have dev, test, prod environment setup in AWS. Your local AWS_PROFILE is something like this so you can switch to different targeting end services while run your application on localhost:

```bash
𝜆 cat ~/.aws/credentials
[default]
aws_access_key_id=dev-aws_access_key_id
aws_secret_access_key=dev-aws_secret_access_key

[dev]
aws_access_key_id=dev-aws_access_key_id
aws_secret_access_key=dev-aws_secret_access_key

[test]
aws_access_key_id=test-aws_access_key_id
aws_secret_access_key=test-aws_secret_access_key

[prod]
aws_access_key_id=prod-aws_access_key_id
aws_secret_access_key=prod-aws_secret_access_key
```

When test Jest client in Test environment, a AWS ElasticSearch client library, error thrown:

```bash
2018-02-27 15:18:19 INFO  org.paradise.search.routes.ElasticSearchRoute  - Body: io.searchbox.core.Index@4804b850[uri=orders/order/4SMK1UmbtqIAAAFhmYAFt9V7,method=PUT] message: searchIndexRoute - updating search index
2018-02-27 15:18:19 INFO  org.apache.camel.processor.interceptor.Tracer  - >>> (searchIndexRoute) org.paradise.search.routes.ElasticSearchRoute$$Lambda$137/819330075@119b837 --> org.paradise.search.routes.ElasticSearchRoute$$Lambda$138/2035788375@2acacdf <<< Pattern:InOnly, BodyType:io.searchbox.core.Index
2018-02-27 15:18:19 ERROR org.paradise.search.routes.ElasticSearchRoute  - Error while updating search index. 403 Forbidden {"Message":"User: arn:aws:iam::123456789012:user/svcbamboo is not authorized to perform: es:ESHttpPut on resource: paradise-esv5-test-esd"} at 'orders/order/4SMK1UmbtqIAAAFhmYAFt9V7'
```

It turns out that WRONG Dev AWS_PROFILE applied in Test environment.

Replacing "aws_access_key_id" and "aws_secret_access_key" from the default profile with "aws_access_key_id" and "aws_secret_access_key" from test profile:

```bash
𝜆 cat ~/.aws/credentials
[default]
aws_access_key_id=test-aws_access_key_id
aws_secret_access_key=test-aws_secret_access_key

[dev]
aws_access_key_id=dev-aws_access_key_id
aws_secret_access_key=dev-aws_secret_access_key

[test]
aws_access_key_id=test-aws_access_key_id
aws_secret_access_key=test-aws_secret_access_key

[prod]
aws_access_key_id=prod-aws_access_key_id
aws_secret_access_key=prod-aws_secret_access_key
```

Rerun the test and in log:

```bash
2018-02-27 15:23:54 INFO  org.paradise.search.routes.ElasticSearchRoute  - Body: io.searchbox.core.Index@62b18125[uri=orders/order/RVwK1UIBXmoAAAFhdJkFo9WA,method=PUT] message: searchIndexRoute - updating search index
2018-02-27 15:23:54 INFO  org.apache.camel.processor.interceptor.Tracer  - >>> (searchIndexRoute) org.paradise.search.routes.ElasticSearchRoute$$Lambda$137/1716909005@a809a62 --> org.paradise.search.routes.ElasticSearchRoute$$Lambda$138/612681832@4b2713b1 <<< Pattern:InOnly, BodyType:io.searchbox.core.Index
2018-02-27 15:23:55 INFO  org.paradise.search.routes.ElasticSearchRoute  - Finished updating search index at 'orders/order/RVwK1UIBXmoAAAFhdJkFo9WA'.
```
