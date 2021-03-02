---
title: A Modern Architecture Application
date: 2021-03-02 16:55:13
tags:
---

RAD (Rapid Application Development) of a Serverless application "Notification Service" on modern technologies, e.g. `AWS CDK` & `SAM`, `AWS Step Functions`, `TypeScript`, `VS Code`, Open API Top Down Design and Test Driven Development, in order to rapidly build a prototype, or a POC, verify and test some technologies and approaches.

Request Handler => Step Functions (orchestration for Lambda functions, represents a single centralized executable business process, outsources low level operations like **retry** / **exception catch and handle**. Another choice is SNS) => Service Providers

Have experienced of `Terraform`, `Serverless`, `AWS SAM` ... now this time based on code over configuration principle, what you get is flexibility, predictability and more control. You focus on code you tell the tools what steps it has to complete directly. At the end of day, it is a simple matter of separation of concerns and single responsibility principle.

• `VS Code` for API Spec editing

• `Postman` API, Environment and Mock server, for QA team, then switch to real service in DEV/TEST environment

```
𝜆 npm run openapi
```

• `openapi-generator` generates model classes; `typescript-json-validator` generates JSON Schema and validator

```
𝜆 openapi-generator generate -g typescript-node -i Notification\ API\ openapi.json -o Notification\ API\ generated
𝜆 npx typescript-json-validator notificationRequest.ts NotificationRequest
```

• Onboard on Kong / API Manager, _https://konghq.com/kong/_

• `CDK`, is based on CloudFormation but abstract layer on the top of it. It can generates CloudFormation template file `template.yaml`

```
𝜆 cdk synth --no-staging > template.yaml
```

• Demo of local run and debug lambda, with background TSC watch process

```
𝜆 npm run watch

𝜆 sam local invoke RequestNotification9F9F3C31 -e samples/api-gateway-notification-event.json
𝜆 sam local invoke RequestNotification9F9F3C31 -e samples/api-gateway-notification-event.json -d 5858
```

Data validation to make data integrity unbreachable will take a lot time.

`ajv` framework and performance benchmark, _https://github.com/ebdrup/json-schema-benchmark_

• Code lint with `eslint` and `prettier` and automatically correction

• Code commit rule enforcement

• Change code and deploy AWS stack by `CDK`

```
𝜆 cdk deploy --require-approval never --profile dev-cicd
```

• Behavior Driven Test Framework Jest, _https://github.com/facebook/jest_, 2x / 3x faster than Karma, with code coverage, easy mocking

```
𝜆 npm t
```

• Automatically generate application changelog and release notes

```
𝜆 npm run release:minor
```

• Automatically generate application document

```
𝜆 npm run docs
```

• AWS resources created by `CDK`

• Not Mono Repo app, which multiple projects all under one giant Repo

• ONE `AWS Layers` put all dependent NPM libs and shared code into; size of Lambda functions, readability

• `AWS EventBridge` tro trigger and send event to Request Handler, for scheduling task

• Health Check, with Service Monitoring Dashboard, verify dependencies at the endpoints, keep Lambda warming up

```
𝜆 curl https://c81234xdae8w1a9.execute-api.ap-southeast-2.amazonaws.com/health
```

Cloud computing and Serverless architecture let developers in fast lane for Application Development. Right now, there are so many low hanging fruit to pick up.

As developers, we should not always think about our comfort zone, we need to think about people who take over your work, think about BAU team to support the application. The codebase is not about you, but about the value that your code brings to others, and the organization that you work for.
