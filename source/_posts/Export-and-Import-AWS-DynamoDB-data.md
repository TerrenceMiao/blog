---
title: Export and Import AWS DynamoDB data
date: 2021-07-06 15:07:34
tags:
---

A simple, straightforward way export and import AWS DynamoDB table's data with AWS CLI and a few scripts.

At first, export all the data from AWS DynamoDB table:

```console
𝜆 aws --profile production dynamodb scan --table-name tile-event > tile-event-export.json
```

Convert a list of items/records (DynamoDB JSON) into individual **PutRequest** JSON with `jq`.

```console
𝜆 cat tile-event-export.json | jq '{"Items": [.Items[] | {PutRequest: {Item: .}}]}' > tile-event-import.json
```

Transform the data if necessary:

```console
𝜆 sed 's/tile-images-prod/tile-images-pdev/g' tile-event-import.json > tile-event-import-transformed.json
```

Split all requests into **25** requests per file, with `jq` and `awk` (Note: There are some restriction with AWS DynamoDB batch-write-item request - The BatchWriteItem operation can contain up to **25** individual PutItem and DeleteItem requests and can write up to 16 MB of data. The maximum size of an individual item is 400 KB.)

```console
𝜆 cat tile-event-processed.awk
#!/usr/bin/awk -f

NR%25==1 {
  x="tile-event-import-processed-"++i".json";
  print "{" > x
  print "  \"tile-event\": [" > x
}
{
  printf "    %s", $0 > x;
}
NR%25!=0 {
  print "," > x
}
NR%25==0 {
  print "" > x
  print "  ]" > x
  print "}" > x
}

𝜆 jq -c '.Items[]' tile-event-import-transformed.json | ./tile-event-processed.awk
```

Import all **22** processed JSON files into DynamoDB table:

```console
$ for f in tile-event-import-processed-{1..22}.json; do \
    echo $f; \
    aws --profile development dynamodb batch-write-item --request-items file://$f; \
  done
```
