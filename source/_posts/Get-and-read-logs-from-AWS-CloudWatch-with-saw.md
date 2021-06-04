---
title: Get and read logs from AWS CloudWatch with saw
date: 2021-06-04 13:49:02
tags:
---

For all the people painfully read logs on AWS CloudWatch console, [saw](https://github.com/TylerBrock/saw) is your friend.

Get CloudWatch log groups start with `paradise-api`:

```console
𝜆 saw groups --profile ap-prod --prefix paradise-api
paradise-api-CloudFormationLogs-mwwmzgYOtbcB
```

Get last 2 hours logs for `paradise-api` from CloudWatch, with [saw](https://github.com/TylerBrock/saw):

```console
𝜆 saw get --profile ap-prod --start -2h paradise-api-CloudFormationLogs-mwwmzgYOtbcB --prefix docker | jq .log | sed 's/\\\n"$//; s/^"//'
```
