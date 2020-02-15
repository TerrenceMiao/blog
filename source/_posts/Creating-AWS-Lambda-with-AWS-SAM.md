---
title: Creating AWS Lambda with AWS SAM
date: 2020-02-14 12:12:06
tags:
---

This is a simple Lambda with REST API and SNS enabled. Firstly, have a look the Nodejs script:

<script src="https://gist.github.com/TerrenceMiao/7d60e5c79f9b93c362bb9a5ff40f67c3.js"></script>

AWS SAM template yaml file:

<script src="https://gist.github.com/TerrenceMiao/3512439f22c427e70db8cadc5cc83846.js"></script>

Generate AWS CloudFormation yaml file and package / zip / create an artefact (need to create AWS S3 bucket `hello-world-tub` in advance):

```console
𝜆 sam package --profile personal --template-file template.yml --output-template-file cloudFormation.yml --s3-bucket hello-world-tub
Uploading to 7431f83ac979bfccc26980049807e595  1461 / 1461.0  (100.00%)

Successfully packaged artifacts and wrote output template to file cloudFormation.yml.
Execute the following command to deploy the packaged template
sam deploy --template-file /Users/terrence/Projects/hello-sam/cloudFormation.yml --stack-name <YOUR STACK NAME>
```

Can also create artefact file with `zip` command, and upload zip file into AWS S3 bucket:

```console
𝜆 zip hello-sam.zip README.md index.js template.yml
```

What AWS CloudFormation yaml file looks like:

<script src="https://gist.github.com/TerrenceMiao/a0ffabc5e118b78dfb21b5cee814e44c.js"></script>

Deploy application's AWS CloudFormation stack with AWS SAM command:

```console
𝜆 sam deploy --profile personal --template-file cloudFormation.yml --stack-name hello-sam --capabilities CAPABILITY_IAM

	Deploying with following values
	===============================
	Stack name                 : hello-sam
	Region                     : None
	Confirm changeset          : False
	Deployment s3 bucket       : None
	Capabilities               : ["CAPABILITY_IAM"]
	Parameter overrides        : {}

Initiating deployment
=====================

Waiting for changeset to be created..

CloudFormation stack changeset
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Operation                                                                                     LogicalResourceId                                                                             ResourceType
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
+ Add                                                                                         HelloWorldFunctionHelloWorldApiPermissionProd                                                 AWS::Lambda::Permission
+ Add                                                                                         HelloWorldFunctionRole                                                                        AWS::IAM::Role
+ Add                                                                                         HelloWorldFunction                                                                            AWS::Lambda::Function
+ Add                                                                                         HelloWorldTopic                                                                               AWS::SNS::Topic
+ Add                                                                                         ServerlessRestApiDeployment79454cea13                                                         AWS::ApiGateway::Deployment
+ Add                                                                                         ServerlessRestApiProdStage                                                                    AWS::ApiGateway::Stage
+ Add                                                                                         ServerlessRestApi                                                                             AWS::ApiGateway::RestApi
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Changeset created successfully. arn:aws:cloudformation:ap-southeast-2:123456789012:changeSet/samcli-deploy1581737165/48e53ff2-1b50-45d8-bbfd-97652f20d967


2020-02-15 14:26:10 - Waiting for stack create/update to complete

CloudFormation events from changeset
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
ResourceStatus                                                        ResourceType                                                          LogicalResourceId                                                     ResourceStatusReason
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
CREATE_IN_PROGRESS                                                    AWS::SNS::Topic                                                       HelloWorldTopic                                                       Resource creation Initiated
CREATE_COMPLETE                                                       AWS::SNS::Topic                                                       HelloWorldTopic                                                       -
CREATE_IN_PROGRESS                                                    AWS::IAM::Role                                                        HelloWorldFunctionRole                                                Resource creation Initiated
CREATE_COMPLETE                                                       AWS::IAM::Role                                                        HelloWorldFunctionRole                                                -
CREATE_IN_PROGRESS                                                    AWS::Lambda::Function                                                 HelloWorldFunction                                                    Resource creation Initiated
CREATE_COMPLETE                                                       AWS::Lambda::Function                                                 HelloWorldFunction                                                    -
CREATE_IN_PROGRESS                                                    AWS::ApiGateway::RestApi                                              ServerlessRestApi                                                     Resource creation Initiated
CREATE_COMPLETE                                                       AWS::ApiGateway::RestApi                                              ServerlessRestApi                                                     -
CREATE_IN_PROGRESS                                                    AWS::Lambda::Permission                                               HelloWorldFunctionHelloWorldApiPermissionProd                         Resource creation Initiated
CREATE_IN_PROGRESS                                                    AWS::ApiGateway::Deployment                                           ServerlessRestApiDeployment79454cea13                                 Resource creation Initiated
CREATE_COMPLETE                                                       AWS::ApiGateway::Deployment                                           ServerlessRestApiDeployment79454cea13                                 -
CREATE_IN_PROGRESS                                                    AWS::ApiGateway::Stage                                                ServerlessRestApiProdStage                                            Resource creation Initiated
CREATE_COMPLETE                                                       AWS::ApiGateway::Stage                                                ServerlessRestApiProdStage                                            -
CREATE_COMPLETE                                                       AWS::Lambda::Permission                                               HelloWorldFunctionHelloWorldApiPermissionProd                         -
CREATE_COMPLETE                                                       AWS::CloudFormation::Stack                                            hello-sam                                                             -
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Successfully created/updated stack - hello-sam in None
```

NOTE: The `--capabilities CAPABILITY_IAM` option is necessary to authorise your stack to create IAM roles, which SAM applications do by default.

After application deployed, user subscribes notification will receive email titled - **AWS Notification - Subscription Confirmation** After confirmation, user will receive an email every time API is invoked.

Now log on AWS Console, have a look the resources this Lambda application used in CloudFormation, S3 Bucket, Lambda, IAM, SNS, CloudWatch, API Gateway.

After this Lambda application successfully deployed into AWS, you will receive an email asking whether you want to subscribe the SNS topic. You can also unsubscribe the SNS topic. You can manually test Lambda function in AWS Console.

References
----------

- Creating AWS Lambda Applications With SAM by **Daniel Weibel**, _https://itnext.io/creating-aws-lambda-applications-with-sam-dd13258c16dd_