---
title: AWS EKS for Fargate
date: 2019-12-05 10:26:58
tags:
---

AWS EKS
-------

After AWS EKS for Fargate annouced in Re:Invent 2019 - [Amazon EKS on AWS Fargate Now Generally Available](https://aws.amazon.com/blogs/aws/amazon-eks-on-aws-fargate-now-generally-available/), I have a quick spin.

General configuration:

![AWS EKS on Fargate - Configuration](/img/AWS%20EKS%20on%20Fargate%20-%20Configuration.png "AWS EKS on Fargate - Configuration")

![AWS EKS on Fargate - Configuration](/img/AWS%20EKS%20on%20Fargate%20-%20Configuration%20II.png "AWS EKS on Fargate - Configuration")

Fargate profile configuration:

![AWS EKS on Fargate - Profile](/img/AWS%20EKS%20on%20Fargate%20-%20Profile.png "AWS EKS on Fargate - Profile")

Fargate roles:

![AWS EKS on Fargate - Roles](/img/AWS%20EKS%20on%20Fargate%20-%20Roles.png "AWS EKS on Fargate - Roles")

`CustomEKSRole` role has `AmazonEKSClusterPolicy` and `AmazonEKSServicePolicy`.

`CustomEKSFargatePodExecutionRole` role has `AmazonEKSFargatePodExecutionRolePolicy`, and Trust relationships:

```console
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "eks-fargate-pods.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

`CustomEKSWorkerNodeRole` role has `AmazonEKSWorkerNodePolicy`, `AmazonEKS_CNI_Policy`, `AmazonEC2ContainerRegistryReadOnly`, and Trust relationships:

```console
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Namespace for Fargate profile Pod Selectors is `default`.

Subnets for Fargate, including private subnets (subnet without Internet Gateway):

![AWS EKS on Fargate - Subnets](/img/AWS%20EKS%20on%20Fargate%20-%20Subnets.png "AWS EKS on Fargate - Subnets")

References
----------

- Getting Started with AWS Fargate on Amazon EKS, https://docs.aws.amazon.com/eks/latest/userguide/fargate-getting-started.html
- Amazon EKS Worker Node IAM Role, https://docs.aws.amazon.com/eks/latest/userguide/worker_node_IAM_role.html
- EKS + Fargate = Extensibility of Kubernetes + Serverless Benefits, https://itnext.io/eks-fargate-extensibility-of-kubernetes-serverless-benefits-77599ac1763
