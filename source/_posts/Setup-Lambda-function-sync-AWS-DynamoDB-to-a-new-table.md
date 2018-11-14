---
title: Setup Lambda migrate & sync AWS DynamoDB to new table
date: 2018-10-24 14:29:00
tags:
---

AWS DynamoDB is a good persistence solution for a specific solution. However, it’s not for a growing and changing application that could need new indexes and queries for its ever expanding features at any time. At this moment it is where the flexibility and speed of a relational database really shined through.

Now, a DynamoDB table with Consumers Preferences data urgently needs to update:

- RENAME one EXISTING attribute
- ADD a new attribute
- SET a value in the new attribute for ALL EXISTING items in DynamoDB's new table

![AWS DynamoDB Table](/blog/img/DynamoDB%20Table.png "AWS DynamoDB Table")

AWS lambda function plays handy here to migrate data, and sync **newly inserted**, **modified / updated**, **deleted** items between existing and new DynamoDB tables.

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

Migrate Data
------------

- Create Migrate Data Lambda function

![AWS DynamoDB Lambda Migrate](/blog/img/DynamoDB%20Lambda%20Migrate.png "AWS DynamoDB Lambda Migrate")

Increase Memory and Runtime Timeout _https://docs.aws.amazon.com/lambda/latest/dg/limits.html_ in case of execution pre-maturely ended without finishing the migration. Furthermore, in case of overcharging DynamoDB due to its limits _https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html_ add time delay in Lambda function.

**migrate.js**

<script src="https://gist.github.com/TerrenceMiao/1a35793e951786b3fb8998e7d067bb0e.js"></script>

- Add AWS DynamoDB Lambda execution role

![AWS DynamoDB Lambda Execution Role](/blog/img/DynamoDB%20Lambda%20Role.png "AWS DynamoDB Lambda Execution Role")

**userpreferences-ptest-02-migrateRole**

<script src="https://gist.github.com/TerrenceMiao/efc13c16267430ee867b239a11fdc486.js"></script>

- Create a simple test event that can kick off function

```json
{
  "key1": "value1",
  "key2": "value2",
  "key3": "value3"
}
```

Trigger run the function. Should see the data migrated from existing DynamoDB table into new table.

Log can be found at AWS CloudWatch Log Groups **/aws/lambda/userpreferences-ptest-02-migrate**

Sync Data
---------

- Enable Stream on the existing DynamoDB table

![AWS DynamoDB Stream Enabled](/blog/img/DynamoDB%20Stream%20Enabled.png "AWS DynamoDB Stream Enabled")

- Add a new trigger for DynamoDB table

![AWS DynamoDB Triggers](/blog/img/DynamoDB%20Triggers.png "AWS DynamoDB Triggers")

- Create a new Lambda function linked to trigger

![AWS DynamoDB Lambda Sync](/blog/img/DynamoDB%20Lambda%20Sync.png "AWS DynamoDB Lambda Sync")

**sync.js**

<script src="https://gist.github.com/TerrenceMiao/7346fc986e3b7999ef205dde6f203eaa.js"></script>

- Trigger Testing

AWS Lambda built-in test can test trigger:

![AWS DynamoDB Lambda Test Event](/blog/img/DynamoDB%20Lambda%20Test%20Event.png "AWS DynamoDB Lambda Test Event")

<script src="https://gist.github.com/TerrenceMiao/f0c9b24265143d03e23e83787a7a102a.js"></script>

- Logging

Lambda function log can be found on AWS CloudWatch Log Groups **/aws/lambda/userpreferences-ptest-02-sync**

![AWS DynamoDB CloudWatch Logging](/blog/img/DynamoDB%20CloudWatch%20Logging.png "AWS DynamoDB CloudWatch Logging")

Counter Data
------------

This Lambda function can count the number of items in DynamoDB table.

**counter.js**

<script src="https://gist.github.com/TerrenceMiao/3d6af3de8ba07e5bdfbb785667117dc3.js"></script>


Async call, callback and Non-blocking, it's very hard implement so in every applications. In addition, reject promises or async functions, don't handle them with a catch, NodeJS will raise a warning. In a large complex applications with lots of async, having a single unhandled promise or await function terminate NodeJS, or have to handle them with try and catch in every place (spaghetti code again?) would be very bad.

An example of AWS DynamoDB error:

```console
2018-11-14T02:20:50.742Z	715f18fb-e7b3-11e8-b5c4-d75f9089dd50	Error thrown: { ProvisionedThroughputExceededException: The level of configured provisioned throughput for the table was exceeded. Consider increasing your provisioning level with the UpdateTable API.
at Request.extractError (/var/runtime/node_modules/aws-sdk/lib/protocol/json.js:48:27)
at Request.callListeners (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:105:20)
at Request.emit (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:77:10)
at Request.emit (/var/runtime/node_modules/aws-sdk/lib/request.js:683:14)
at Request.transition (/var/runtime/node_modules/aws-sdk/lib/request.js:22:10)
at AcceptorStateMachine.runTo (/var/runtime/node_modules/aws-sdk/lib/state_machine.js:14:12)
at /var/runtime/node_modules/aws-sdk/lib/state_machine.js:26:10
at Request.<anonymous> (/var/runtime/node_modules/aws-sdk/lib/request.js:38:9)
at Request.<anonymous> (/var/runtime/node_modules/aws-sdk/lib/request.js:685:12)
at Request.callListeners (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:115:18)
message: 'The level of configured provisioned throughput for the table was exceeded. Consider increasing your provisioning level with the UpdateTable API.',
code: 'ProvisionedThroughputExceededException',
time: 2018-11-14T02:20:50.687Z,
requestId: 'C38MODOISAJEGTVPI2ISOPFGDBVV4KQNSO5AEMVJF66Q9ASUAAJG',
statusCode: 400,
retryable: true }
```

AWS example doesn't throw the error in the catch block, it returns error instead, so any errors end up in the catch block. And return promises early and use Promise.all() method.

References
----------

- DynamoDB: Changing table schema, _https://www.abhayachauhan.com/2018/01/dynamodb-changing-table-schema/_
- Tutorial: Processing New Items in a DynamoDB Table, _https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.Lambda.Tutorial.html_
- How to escape async/await hell, _https://medium.freecodecamp.org/avoiding-the-async-await-hell-c77a0fb71c4c_
- Node.js 8.10 runtime now available in AWS Lambda, _https://aws.amazon.com/blogs/compute/node-js-8-10-runtime-now-available-in-aws-lambda/_
