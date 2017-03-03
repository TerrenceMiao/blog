---
title: Set up and run AWS Lambda 'hello' function with serverless
date: 2017-02-26 19:00:26
tags:
---

### serverless

With latest Node.js 6.x.x installed, then install serverless globally:

``` bash
$ npm install serverless -g
```

### AWS Lambda

Create a AWS Lambda skeleton project with serverless:

``` bash
$ mkdir serverless-example && cd $_

$ sls create -t aws-nodejs
Serverless: Generating boilerplate...
 _______                             __
|   _   .-----.----.--.--.-----.----|  .-----.-----.-----.
|   |___|  -__|   _|  |  |  -__|   _|  |  -__|__ --|__ --|
|____   |_____|__|  \___/|_____|__| |__|_____|_____|_____|
|   |   |             The Serverless Application Framework
|       |                           serverless.com, v1.7.0
 -------'

Serverless: Successfully generated boilerplate for template: "aws-nodejs"
Serverless: NOTE: Please update the "service" property in serverless.yml with your service name
```

* **Policies set up for Lambda function**

For AWS user "ec2-user", now need to have some policies with permissions to let "serverless" create role, Lambda function and deployment it ...

![Polices set up for Lambda function](https://raw.githubusercontent.com/TerrenceMiao/AWS/master/serverless-example/Lambda%20Policies.png)

* **Roles for Lambda function**

Lambda function role created after Lambda function added and deployed into AWS.

![Roles for Lambda function](https://raw.githubusercontent.com/TerrenceMiao/AWS/master/serverless-example/Lambda%20Roles.png)

### Deployment

Make sure AWS environment has been set up, including access key, user, group, policies ...

Pack and deploy Lambda example into AWS:

``` bash
$ sls deploy -r ap-southeast-2 -s dev
Serverless: Packaging service...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading service .zip file to S3 (583 B)...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
..................
Serverless: Stack update finished...
Service Informations
service: serverless-example
stage: dev
region: ap-southeast-2
api keys:
  None
endpoints:
  None
functions:
  serverless-example-dev-hello
```

* **Lambda "hello" function**

A "hello" Lambda function has been created in Lambda after it's deployed into AWS by "serverless".

![Lambda "hello" function](https://raw.githubusercontent.com/TerrenceMiao/AWS/master/serverless-example/Lambda%20Hello%20function.png)

* **Events generated during Lambda function deployment**

Deployment events generated during Lambda "hello" function deployed into AWS.

![Events generated during Lambda function deployment](https://raw.githubusercontent.com/TerrenceMiao/AWS/master/serverless-example/Lambda%20CloudFormation.png)

* **Add Lambda Trigger on AWS API Gateway**

Manually create a Lambda Trigger. This time we use AWS API Gateway to trigger / invoke Lambda "hello" function.

![Lambda Trigger created on AWS API Gateway](https://raw.githubusercontent.com/TerrenceMiao/AWS/master/serverless-example/Lambda%20Trigger%20on%20API%20Gateway.png)

* **Exposed Lambda API Gateway**

After Lambda Trigger created, an exposed RESTful interface for Lambda "hello" function.

![Lambda API Gateway](https://raw.githubusercontent.com/TerrenceMiao/AWS/master/serverless-example/Lambda%20API%20Gateway.png)

### Say "hello"

Set up AWS API Gateway trigger for Lambda "hello" function. Go to url, e.g.:

Function "hello" log:

``` json
{
  "message": "Go Serverless v1.0! Your function executed successfully!",
  "input": {
    "resource": "/serverless-example-dev-hello",
    "path": "/serverless-example-dev-hello",
    "httpMethod": "GET",
    "headers": {
      "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
      "Accept-Encoding": "gzip, deflate, sdch, br",
      "Accept-Language": "en-AU,en-GB;q=0.8,en-US;q=0.6,en;q=0.4",
      "CloudFront-Forwarded-Proto": "https",
      "CloudFront-Is-Desktop-Viewer": "true",
      "CloudFront-Is-Mobile-Viewer": "false",
      "CloudFront-Is-SmartTV-Viewer": "false",
      "CloudFront-Is-Tablet-Viewer": "false",
      "CloudFront-Viewer-Country": "AU",
      "Host": "b5dyhej16l.execute-api.ap-southeast-2.amazonaws.com",
      "Referer": "https://ap-southeast-2.console.aws.amazon.com/lambda/home?region=ap-southeast-2",
      "upgrade-insecure-requests": "1",
      "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.95 Safari/537.36",
      "Via": "2.0 6884828476070d32978b45d03c1cc437.cloudfront.net (CloudFront)",
      "X-Amz-Cf-Id": "mvToMffe1AsUJNcMJKUh-Rx26oBJsRBe2n9I1df3xqIAIENPR_ku3A==",
      "X-Amzn-Trace-Id": "Root=1-58aae2ff-0b0c5e4059cc97576211ba4a",
      "X-Forwarded-For": "101.181.175.227, 54.239.202.65",
      "X-Forwarded-Port": "443",
      "X-Forwarded-Proto": "https"
    },
    "queryStringParameters": null,
    "pathParameters": null,
    "stageVariables": null,
    "requestContext": {
      "accountId": "624388274630",
      "resourceId": "5jbqsp",
      "stage": "prod",
      "requestId": "51ba2876-f769-11e6-b507-4b10c8a6886a",
      "identity": {
        "cognitoIdentityPoolId": null,
        "accountId": null,
        "cognitoIdentityId": null,
        "caller": null,
        "apiKey": null,
        "sourceIp": "101.181.175.227",
        "accessKey": null,
        "cognitoAuthenticationType": null,
        "cognitoAuthenticationProvider": null,
        "userArn": null,
        "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.95 Safari/537.36",
        "user": null
      },
      "resourcePath": "/serverless-example-dev-hello",
      "httpMethod": "GET",
      "apiId": "b5dyhej16l"
    },
    "body": null,
    "isBase64Encoded": false
  }
}
```

### References
* serverless framework, https://serverless.com/
* Example source code and artefact, https://github.com/TerrenceMiao/AWS/tree/master/serverless-example

