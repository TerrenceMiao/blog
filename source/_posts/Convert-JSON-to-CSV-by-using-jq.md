---
title: Convert JSON to CSV by using jq
date: 2022-05-02 16:20:52
tags:
---

Step by step convert exported JSON data from AWS DynamoDB table into CSV, by using `jq`.

Export all the data from AWS DynamoDB table at first:

```console
𝜆 aws --profile production dynamodb scan --table-name tiles > tiles.json
```

The exported JSON data looks like:

```console
{
    "Items": [
        {
            "last_modified_date": {
                "S": "2021-12-09T01:15:25.335516"
            },
            "valid_from": {
                "S": "2021-12-09T01:00"
            },
            "created_date": {
                "S": "2021-12-09T01:15:25.334965"
            },
            "status": {
                "S": "PUBLISHED"
            },
            "valid_to": {
                "S": "2022-01-31T23:00"
            },
            "id": {
                "S": "b2c60f43-a81c-4363-a68a-dfe7682182d7"
            },
            "description": {
                "S": "Hit the road Jack!"
            },
            "title": {
                "S": "Novated Lease"
            }
        },
...
    ],
    "Count": 223,
    "ScannedCount": 223,
    "ConsumedCapacity": null
}
```

Extract / transform JSON data:

```console
𝜆 cat tiles.json | jq '[.Items[] | { id: .id.S, title: .title.S, description: .description.S, status: .status.S, valid_from: .valid_from.S, valid_to: .valid_to.S }]' > tiles-extracted.json
[
  {
    "id": "b2c60f43-a81c-4363-a68a-dfe7682182d7",
    "title": "Novated Lease",
    "description": "Hit the road Jack!",
    "status": "PUBLISHED",
    "valid_from": "2021-12-09T01:00",
    "valid_to": "2022-01-31T23:00"
  },
...
]
```

Convert JSON data into CSV:

```console
𝜆 cat tiles-extracted.json | jq -r '(.[0] | keys_unsorted) as $keys | $keys, map([.[ $keys[] ]])[] | @csv' > tiles.csv
```

References
----------

* How to convert arbitrary simple JSON to CSV using jq, https://stackoverflow.com/questions/32960857/how-to-convert-arbitrary-simple-json-to-csv-using-jq