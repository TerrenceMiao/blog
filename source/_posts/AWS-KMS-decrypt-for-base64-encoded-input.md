---
title: AWS KMS decrypt for base64 encoded input
date: 2021-04-30 19:22:55
tags:
---

With AWS CLI version 2:

```console
𝜆 aws --version
aws-cli/2.1.17 Python/3.7.4 Darwin/20.3.0 exe/x86_64 prompt/off
```

Encrypt with AWS KMS key:

```console
𝜆 aws kms encrypt --profile personal \
  --key-id e2695b79-cbe0-4c16-aa5e-b7dbf52df1f9 \
  --plaintext "string-to-encrypt" \
  --output text \
  --query CiphertextBlob \
  --cli-binary-format raw-in-base64-out
AQICAHjbJrIPgME ... lILuBSUdA==
```

Decrypt with AWS KMS key:

```console
𝜆 echo "AQICAHjbJrIPgME ... lILuBSUdA==" | base64 -D | \
  aws kms decrypt --profile personal \
  --ciphertext-blob fileb:///dev/stdin \
  --output text \
  --query Plaintext | base64 -D
```

Reference
---------

- cli_binary_format, _https://docs.aws.amazon.com/credref/latest/refdocs/setting-global-cli_binary_format.html_
