---
title: AWS CloudWatch Metrics Example
date: 2020-10-13 23:29:39
tags:
---

AWS CloudWatch Metrics
----------------------

The interface of Metrics in AWS CloudWatch console:

![AWS CloudWatch - Metrics](/blog/img/AWS%20CloudWatch%20-%20Metrics.png "AWS CloudWatch - Metrics")

The URL:

```console
https://ap-southeast-2.console.aws.amazon.com/cloudwatch/home?region=ap-southeast-2#metricsV2:graph=~(metrics~(~(~'AWS*2fRoute53Resolver~'InboundQueryVolume)~(~'.~'OutboundQueryVolume))~view~'timeSeries~stacked~false~region~'ap-southeast-2~stat~'Sum~period~86400~start~'-P28D~end~'P0D);query=~'*7bAWS*2fRoute53Resolver*7d
```

Metrics source:

```console
{
    "metrics": [
        [ "AWS/Route53Resolver", "InboundQueryVolume" ],
        [ ".", "OutboundQueryVolume" ]
    ],
    "view": "timeSeries",
    "stacked": false,
    "region": "ap-southeast-2",
    "stat": "Sum",
    "period": 86400,
    "title": "Test"
}
```