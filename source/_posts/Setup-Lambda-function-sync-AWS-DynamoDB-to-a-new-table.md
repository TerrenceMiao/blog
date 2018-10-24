---
title: Setup Lambda function sync AWS DynamoDB to a new table
date: 2018-10-24 14:29:00
tags:
---

AWS DynamoDB is a good persistence solution for a specific solution. However, it’s not for a growing and changing application that could need new indexes and queries for its ever expanding features at any time. This time is where the flexibility and speed of a relational database really shined through.

Now, a DynamoDB table with Consumers Preferences need to update:

- RENAME one EXISTING attribute
- ADD a new attribute
- SET value in the new attribute for ALL EXISTING items in DynamoDB

![AWS DynamoDB Table](/blog/img/DynamoDB%20Table.png "AWS DynamoDB Table")

AWS lambda function plays handy here to sync **newly inserted**, **modified / updated**, **deleted** items between existing and new DynamoDB tables.

- Create new DynamoDB table

```console
$ aws dynamodb create-table \
    --table-name userpreferences-ptest-02-USER_PREFERENCESV2 \
    --attribute-definitions AttributeName=id,AttributeType=S AttributeName=preferenceType,AttributeType=S \
    --key-schema AttributeName=id,KeyType=HASH  AttributeName=preferenceType,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=100,WriteCapacityUnits=100

{
    "TableDescription": {
        "TableArn": "arn:aws:dynamodb:ap-southeast-2:123456789012:table/userpreferences-ptest-02-USER_PREFERENCESV2",
        "AttributeDefinitions": [
            {
                "AttributeName": "id",
                "AttributeType": "S"
            },
            {
                "AttributeName": "preferenceType",
                "AttributeType": "S"
            }
        ],
        "ProvisionedThroughput": {
            "NumberOfDecreasesToday": 0,
            "WriteCapacityUnits": 100,
            "ReadCapacityUnits": 100
        },
        "TableSizeBytes": 0,
        "TableName": "userpreferences-ptest-02-USER_PREFERENCESV2",
        "TableStatus": "CREATING",
        "TableId": "d116efdc-1234-5678-90ab-011de3e124fe",
        "KeySchema": [
            {
                "KeyType": "HASH",
                "AttributeName": "id"
            },
            {
                "KeyType": "RANGE",
                "AttributeName": "preferenceType"
            }
        ],
        "ItemCount": 0,
        "CreationDateTime": 1540273906.1059999
    }
}
```

- Enable Stream on the existing DynamoDB table

![AWS DynamoDB Stream Enabled](/blog/img/DynamoDB%20Stream%20Enabled.png "AWS DynamoDB Stream Enabled")

- Add a new trigger for DynamoDB table

![AWS DynamoDB Triggers](/blog/img/DynamoDB%20Triggers.png "AWS DynamoDB Triggers")

- Create a new Lambda function linked to trigger

![AWS DynamoDB Lambda](/blog/img/DynamoDB%20Lambda.png "AWS DynamoDB Lambda")

**userpreferences-migrate.js**

<script src="https://gist.github.com/TerrenceMiao/7346fc986e3b7999ef205dde6f203eaa.js"></script>

- Add AWS DynamoDB Lambda execution role

![AWS DynamoDB Lambda Execution Role](/blog/img/DynamoDB%20Lambda%20Role.png "AWS DynamoDB Lambda Execution Role")

**userpreferences-ptest-02-migrateRole**

<script src="https://gist.github.com/TerrenceMiao/efc13c16267430ee867b239a11fdc486.js"></script>

- Testing

AWS Lambda built-in test can test trigger:

![AWS DynamoDB Lambda Test Event](/blog/img/DynamoDB%20Lambda%20Test%20Event.png "AWS DynamoDB Lambda Test Event")

<script src="https://gist.github.com/TerrenceMiao/f0c9b24265143d03e23e83787a7a102a.js"></script>

- Logging

Lambda function log can be found on AWS CloudWatch:

![AWS DynamoDB CloudWatch Logging](/blog/img/DynamoDB%20CloudWatch%20Logging.png "AWS DynamoDB CloudWatch Logging")

References
----------

- DynamoDB: Changing table schema, _https://www.abhayachauhan.com/2018/01/dynamodb-changing-table-schema/_
- Tutorial: Processing New Items in a DynamoDB Table, _https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.Lambda.Tutorial.html_
