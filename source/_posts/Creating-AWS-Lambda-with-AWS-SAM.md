---
title: Creating AWS Lambda with AWS SAM
date: 2020-02-14 12:12:06
tags:
---

This is a simple Lambda with REST API and SNS enabled. Firstly, have a look the Nodejs script:

<script src="https://gist.github.com/TerrenceMiao/7d60e5c79f9b93c362bb9a5ff40f67c3.js"></script>

AWS SAM template yaml file:

<script src="https://gist.github.com/TerrenceMiao/3512439f22c427e70db8cadc5cc83846.js"></script>

Generate AWS CloudFormation yaml file and package / zip / create an artefact:

```console
𝜆 sam package --template-file template.yml --output-template-file CloudFormation.yml --s3-bucket hello-world-bucket
```

Can also create artefact file with `zip` command, and upload zip file into AWS S3 bucket:

```console
𝜆 zip hello-world.zip README.md index.js template.yml
```

What AWS CloudFormation yaml file looks like:

<script src="https://gist.github.com/TerrenceMiao/a0ffabc5e118b78dfb21b5cee814e44c.js"></script>

Deploy application's AWS CloudFormation stack with AWS SAM command:

```console
𝜆 sam deploy --template-file CloudFormation.yml --stack-name hello-world --capabilities CAPABILITY_IAM
```

NOTE: The `--capabilities CAPABILITY_IAM` option is necessary to authorise your stack to create IAM roles, which SAM applications do by default.

Now log on AWS Console, have a look the resources this Lambda application used in CloudFormation, S3 Bucket, Lambda, IAM, SNS, CloudWatch, API Gateway.

After this Lambda application successfully deployed into AWS, you will receive an email asking whether you want to subscribe the SNS topic. You can also unsubscribe the SNS topic. You can manually test Lambda function in AWS Console.

References
----------

- Creating AWS Lambda Applications With SAM by **Daniel Weibel**, _https://itnext.io/creating-aws-lambda-applications-with-sam-dd13258c16dd_