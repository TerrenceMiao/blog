---
title: AWS ElasticSearch and Kibana proxy setup
date: 2018-02-17 20:26:41
tags:
---

There are several ways to access Amazon AWS ElasticSearch and Kibana services, which are HTTP based, without inject into HTTP request headers with authentication key ...

## AWS ES Proxy

Install a proxy application - AWS ES/Kibana Proxy. Download it from _https://www.npmjs.com/package/aws-es-kibana_ and install:

```bash
$ npm install -g aws-es-kibana
```

Run AWS ES/Kibana Proxy then connect to ElasticSearch and Kibana services on AWS:

```bash
$ cat ~/.aws/config
[default]
output = json
region = ap-southeast-2

[profile sandy-test]
output = json
region = ap-southeast-2

$ cat ~/.aws/credentials
[default]
aws_access_key_id=Academy
aws_secret_access_key=Dingly Ding

[ap-test]
aws_access_key_id=Abracadbra
aws_secret_access_key=Somewhere Over The Rainbow

$ AWS_PROFILE=ap-test aws-es-kibana search-paradise-esv5-test-01-esd-blah23dlaoed81nz890adle4.ap-southeast-2.es.amazonaws.com
__________       _________    _________________    ________                            ______
___    |_ |     / /_  ___/    ___  ____/_  ___/    ___  __ \________________  ______  ____  /
__  /| |_ | /| / /_____ \     __  __/  _____ \     __  /_/ /_  ___/  __ \_  |/_/_  / / /_  /
_  ___ |_ |/ |/ / ____/ /     _  /___  ____/ /     _  ____/_  /   / /_/ /_>  < _  /_/ / /_/
/_/  |_|___/|__/  /____/      /_____/  /____/      /_/     /_/    \____//_/|_| _\__, / (_)
                                                                               /____/
AWS ES cluster available at http://127.0.0.1:9200
Kibana available at http://127.0.0.1:9200/_plugin/kibana/
```

With Fish Shell:

```fish
𝜆 env AWS_PROFILE=ap-test aws-es-kibana search-paradise-esv5-test-01-esd-blah23dlaoed81nz890adle4.ap-southeast-2.es.amazonaws.com
__________       _________    _________________    ________                            ______
___    |_ |     / /_  ___/    ___  ____/_  ___/    ___  __ \________________  ______  ____  /
__  /| |_ | /| / /_____ \     __  __/  _____ \     __  /_/ /_  ___/  __ \_  |/_/_  / / /_  /
_  ___ |_ |/ |/ / ____/ /     _  /___  ____/ /     _  ____/_  /   / /_/ /_>  < _  /_/ / /_/
/_/  |_|___/|__/  /____/      /_____/  /____/      /_/     /_/    \____//_/|_| _\__, / (_)
                                                                               /____/
AWS ES cluster available at http://127.0.0.1:9200
Kibana available at http://127.0.0.1:9200/_plugin/kibana/
```

## SSH Tunnel

Set up SSH Tunnel for AWS ElasticSearch https://search-paradise-esv5-test-01-esd-blah23dlaoed81nz890adle4.ap-southeast-2.es.amazonaws.com from 443 port to localhost 9200:

``` bash
𝜆 ssh -L 9200:search-paradise-esv5-test-01-esd-blah23dlaoed81nz890adle4.ap-southeast-2.es.amazonaws.com:443 -l ec2-user aws-jump-box
```

Then access AWS ElasticSearch at: https://localhost:9200, AWS Kibana at: https://localhost:9200/_plugin/kibana

## ElasticSearch Head

With plugin ElasticSearch Head, to query ElasticSearch, using URL and index "orders-search-box" e.g. https://localhost:9200/orders-search-test/, and context path "_search"
