---
title: Run ssh and scp with AWS Session Manager
date: 2019-11-22 12:09:05
tags:
---

New AWS Systems Manager, including Session Manager is another step enhance security on Cloud. Here are step by step how to set up.

- You have `ec2-user` account on AWS EC2 instance. On localhost:

```console
𝜆 cat .aws/config
[default]
output = json
region = ap-southeast-2

𝜆 cat .aws/credentials
[default]
aws_access_key_id = ASIANPOWERHOUSEBLAHBLAH
aws_secret_access_key = aGLMountainLionPIEXL0UK0TunalNB61Kt+GuavaVm4tAD

𝜆 ssh -i .ssh/aws-key.pem -l ec2-user ec2-3-121-69-96.ap-southeast-2.compute.amazonaws.com
Last login: Fri Nov 22 01:17:33 2019 from 155.144.114.41

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/

```

- Install all the dependencies
  1. latest Systems Manager Agent on your EC2 instance; enabled “**Agent auto update**” under Managed Instances in AWS Systems Manager
  2. latest AWS CLI on localhost
  3. lastest Session Manager Plugin on localhost

- Update SSH config file on localhost to proxy commands through the AWS Session Manager for any EC2 instance id

```console
𝜆 cat .ssh/config

# SSH over Session Manager
host i-* mi-*
    ProxyCommand sh -c "aws ssm start-session --target %h --document-name AWS-StartSSHSession --parameters 'portNumber=%p'"
```

- Generate session token

```console
𝜆 date
Thu 21 Nov 2019 04:33:03 UTC

𝜆 aws sts get-session-token --duration-seconds 129600
{
    "Credentials": {
        "SecretAccessKey": "I7brpY8XWDWYwwyUdp5PLq7cxpskuMSHyBtPjPNE",
        "SessionToken": "FwoGZXIvYXdzENL//////////wEaDKKdWTVmCrwKRiMbOSKCAbkQr ... YiNntciJszsZVRypXz1HTfa3gbcKoNXHon8=",
        "Expiration": "2019-11-22T16:33:12Z",
        "AccessKeyId": "ASIASZPQ3TMDVJVIGM7H"
    }
}
```

If AWS user `ec2-user` has MFA enabled, generate session token like this:

```console
𝜆 aws sts get-session-token --duration-seconds 129600 --serial-number arn:aws:iam::910218657901234:mfa/root-account-mfa-device --token-code 251556
```

- Add session token in AWS credentials file on localhost, and test

```console
𝜆 cat .aws/credentials
[default]
aws_access_key_id = ASIANPOWERHOUSEBLAHBLAH
aws_secret_access_key = aGLMountainLionPIEXL0UK0TunalNB61Kt+GuavaVm4tAD

[session]
aws_access_key_id = ASIASZPQ3TMDVJVIGM7H
aws_secret_access_key = I7brpY8XWDWYwwyUdp5PLq7cxpskuMSHyBtPjPNE
aws_session_token = FwoGZXIvYXdzENL//////////wEaDKKdWTVmCrwKRiMbOSKCAbkQr ... YiNntciJszsZVRypXz1HTfa3gbcKoNXHon8=

𝜆 aws ssm start-session --target i-e2f189dashfdf65weqfwda2 --profile session

Starting session with SessionId: ec2-user-094c34172bdc6fc22
sh-4.2$ whoami
ssm-user

𝜆 aws ssm send-command --instance-ids "i-e2f189dashfdf65weqfwda2" --document-name "AWS-RunShellScript" --comment "IP config" --parameters commands=ifconfig --output text
COMMAND	39e80533-376e-46fa-bb11-8daf040fe80f	IP config	0	0	AWS-RunShellScript		0	1574377262.9	50	0			1574370062.9		Pending	Pending	1
CLOUDWATCHOUTPUTCONFIG		False
INSTANCEIDS	i-e2f189dashfdf65weqfwda2
NOTIFICATIONCONFIG
COMMANDS	ifconfig

𝜆 aws ssm list-command-invocations --command-id 39e80533-376e-46fa-bb11-8daf040fe80f --details --profile personal
{
    "CommandInvocations": [
        {
            "Comment": "IP config",
            "Status": "Success",
            "CommandPlugins": [
                {
                    "Status": "Success",
                    "ResponseStartDateTime": 1574370063.609,
                    "StandardErrorUrl": "",
                    "OutputS3BucketName": "",
                    "OutputS3Region": "ap-southeast-2",
                    "OutputS3KeyPrefix": "",
                    "ResponseCode": 0,
                    "Output": "eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001\n        inet 172.31.9.155  netmask 255.255.240.0  broadcast 172.31.15.255\n        inet6 fe80::69:c9ff:fe76:91ae  prefixlen 64  scopeid 0x20<link>\n        ether 02:69:c9:76:91:ae  txqueuelen 1000  (Ethernet)\n        RX packets 60339  bytes 17254584 (16.4 MiB)\n        RX errors 0  dropped 0  overruns 0  frame 0\n        TX packets 56971  bytes 13112880 (12.5 MiB)\n        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0\n\nlo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536\n        inet 127.0.0.1  netmask 255.0.0.0\n        inet6 ::1  prefixlen 128  scopeid 0x10<host>\n        loop  txqueuelen 1000  (Local Loopback)\n        RX packets 0  bytes 0 (0.0 B)\n        RX errors 0  dropped 0  overruns 0  frame 0\n        TX packets 0  bytes 0 (0.0 B)\n        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0\n\n",
                    "ResponseFinishDateTime": 1574370063.634,
                    "StatusDetails": "Success",
                    "StandardOutputUrl": "",
                    "Name": "aws:runShellScript"
                }
            ],
            "ServiceRole": "",
            "CloudWatchOutputConfig": {
                "CloudWatchLogGroupName": "",
                "CloudWatchOutputEnabled": false
            },
            "InstanceId": "i-e2f189dashfdf65weqfwda2",
            "DocumentName": "AWS-RunShellScript",
            "NotificationConfig": {
                "NotificationArn": "",
                "NotificationEvents": [],
                "NotificationType": ""
            },
            "DocumentVersion": "",
            "StatusDetails": "Success",
            "StandardOutputUrl": "",
            "StandardErrorUrl": "",
            "InstanceName": "",
            "CommandId": "39e80533-376e-46fa-bb11-8daf040fe80f",
            "RequestedDateTime": 1574370062.995
        }
    ]
}
```

- Monitor AWS Session Manager log `/var/log/amazon/ssm/amazon-ssm-agent.log` on EC2 instance

- Test `ssh` command with session token

```console
𝜆 ssh -i .ssh/aws-key.pem -l ec2-user i-e2f189dashfdf65weqfwda2
Last login: Fri Nov 22 00:19:32 2019 from localhost

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
```

- Test `scp` command with session token

```console
𝜆 scp -i .ssh/aws-key.pem /tmp/adobegc.log ec2-user@i-e2f189dashfdf65weqfwda2:/tmp
stack-overflow.log                                                                 100%   67KB 437.0KB/s   00:00
```

References
----------

- Troubleshooting Systems Manager Run Command, _https://docs.aws.amazon.com/systems-manager/latest/userguide/troubleshooting-remote-commands.html#systems-manager-ssm-agent-log-files_