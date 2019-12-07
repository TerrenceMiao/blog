---
title: 'AWS EKS on Fargate, with eksctl'
date: 2019-12-07 22:33:38
tags:
---

AWS EKS, with eksctl
--------------------

Second try with AWS EKS on Fargate. This time with `eksctl`.

Create EKS cluster:

```console
𝜆 eksctl create cluster --name sandpit --version 1.14 --region us-east-2 --fargate
[ℹ]  eksctl version 0.11.1
[ℹ]  using region us-east-2
[ℹ]  setting availability zones to [us-east-2b us-east-2a us-east-2c]
[ℹ]  subnets for us-east-2b - public:192.168.0.0/19 private:192.168.96.0/19
[ℹ]  subnets for us-east-2a - public:192.168.32.0/19 private:192.168.128.0/19
[ℹ]  subnets for us-east-2c - public:192.168.64.0/19 private:192.168.160.0/19
[ℹ]  using Kubernetes version 1.14
[ℹ]  creating EKS cluster "sandpit" in "us-east-2" region with Fargate profile
[ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-east-2 --cluster=sandpit'
[ℹ]  CloudWatch logging will not be enabled for cluster "sandpit" in "us-east-2"
[ℹ]  you can enable it with 'eksctl utils update-cluster-logging --region=us-east-2 --cluster=sandpit'
[ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "sandpit" in "us-east-2"
[ℹ]  1 task: { create cluster control plane "sandpit" }
[ℹ]  building cluster stack "eksctl-sandpit-cluster"
[ℹ]  deploying stack "eksctl-sandpit-cluster"
[✔]  all EKS cluster resources for "sandpit" have been created
[✔]  saved kubeconfig as "/Users/terrence/.kube/config"
[ℹ]  creating Fargate profile "fp-default" on EKS cluster "sandpit"
[ℹ]  created Fargate profile "fp-default" on EKS cluster "sandpit"
[ℹ]  "coredns" is now schedulable onto Fargate
[ℹ]  "coredns" is now scheduled onto Fargate
[ℹ]  "coredns" pods are now scheduled onto Fargate
[ℹ]  kubectl command should work with "/Users/terrence/.kube/config", try 'kubectl get nodes'
[✔]  EKS cluster "sandpit" in "us-east-2" region is ready
```

![AWS EKS on Fargate, with eksctl - Cluster](/blog/img/AWS%20EKS%20on%20Fargate,%20with%20eksctl%20-%20Cluster.png "AWS EKS on Fargate, with eksctl - Cluster")

Create and add EKS mansged node group:

```console
𝜆 eksctl create nodegroup --cluster sandpit --name workers --node-type t3a.medium --ssh-access --ssh-public-key aws-us-key --managed
[ℹ]  eksctl version 0.11.1
[ℹ]  using region us-east-2
[ℹ]  will use version 1.14 for new nodegroup(s) based on control plane version
[ℹ]  using EC2 key pair %!!(MISSING)q(*string=<nil>)
[ℹ]  1 nodegroup (workers) was included (based on the include/exclude rules)
[ℹ]  will create a CloudFormation stack for each of 1 managed nodegroups in cluster "sandpit"
[ℹ]  1 task: { 1 task: { create managed nodegroup "workers" } }
[ℹ]  building managed nodegroup stack "eksctl-sandpit-nodegroup-workers"
[ℹ]  deploying stack "eksctl-sandpit-nodegroup-workers"
[✔]  created 0 nodegroup(s) in cluster "sandpit"
[ℹ]  nodegroup "workers" has 2 node(s)
[ℹ]  node "ip-192-168-47-175.us-east-2.compute.internal" is ready
[ℹ]  node "ip-192-168-87-98.us-east-2.compute.internal" is ready
[ℹ]  waiting for at least 2 node(s) to become ready in "workers"
[ℹ]  nodegroup "workers" has 2 node(s)
[ℹ]  node "ip-192-168-47-175.us-east-2.compute.internal" is ready
[ℹ]  node "ip-192-168-87-98.us-east-2.compute.internal" is ready
[✔]  created 1 managed nodegroup(s) in cluster "sandpit"
[ℹ]  checking security group configuration for all nodegroups
[ℹ]  all nodegroups have up-to-date configuration
```

Install Kubernetes Dashboard:

```console
𝜆 kubectl get services  --all-namespaces
NAMESPACE              NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
default                kubernetes                  ClusterIP   10.100.0.1       <none>        443/TCP         70m
kube-system            kube-dns                    ClusterIP   10.100.0.10      <none>        53/UDP,53/TCP   70m
kube-system            metrics-server              ClusterIP   10.100.142.106   <none>        443/TCP         14m
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.100.91.78     <none>        8000/TCP        11m
kubernetes-dashboard   kubernetes-dashboard        ClusterIP   10.100.75.0      <none>        443/TCP         11m

𝜆 kubectl get pods  --all-namespaces
NAMESPACE              NAME                                         READY   STATUS    RESTARTS   AGE
kube-system            aws-node-cnzrv                               1/1     Running   0          40m
kube-system            aws-node-m9tjp                               1/1     Running   0          40m
kube-system            coredns-7f5cccffc-h44mz                      1/1     Running   0          65m
kube-system            coredns-7f5cccffc-hmx7g                      1/1     Running   0          65m
kube-system            kube-proxy-7kn62                             1/1     Running   0          40m
kube-system            kube-proxy-g57ph                             1/1     Running   0          40m
kube-system            metrics-server-7fcf9cc98b-ftl4k              1/1     Running   0          14m
kubernetes-dashboard   dashboard-metrics-scraper-677768c755-mxlmc   1/1     Running   0          11m
kubernetes-dashboard   kubernetes-dashboard-995fd6fb4-xqcj5         1/1     Running   0          11m
```

Connect Kubernetes Dashboard via proxy:

```console
𝜆 cat .kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /Users/terrence/.minikube/ca.crt
    server: https://192.168.99.100:8443
  name: minikube
- cluster:
    certificate-authority-data: LS0tLS1CRUd ... tLS0tLQo=
    server: https://0559DE89F43B8766B56C7FD066C6C50F.yl4.us-east-2.eks.amazonaws.com
  name: sandpit.us-east-2.eksctl.io
contexts:
- context:
    cluster: sandpit.us-east-2.eksctl.io
    user: ADMMiaoT@sandpit.us-east-2.eksctl.io
  name: ADMMiaoT@sandpit.us-east-2.eksctl.io
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: ADMMiaoT@sandpit.us-east-2.eksctl.io
kind: Config
preferences: {}
users:
- name: ADMMiaoT@sandpit.us-east-2.eksctl.io
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1alpha1
      args:
      - token
      - -i
      - sandpit
      command: aws-iam-authenticator
      env:
      - name: AWS_PROFILE
        value: paradise-dev
- name: minikube
  user:
    client-certificate: /Users/terrence/.minikube/client.crt
    client-key: /Users/terrence/.minikube/client.key

𝜆 kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}'
eks-admin-token-s2gf5

𝜆 kubectl -n kube-system describe secret eks-admin-token-s2gf5
Name:         eks-admin-token-s2gf5
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: eks-admin
              kubernetes.io/service-account.uid: fa3cf514-18bc-11ea-bbdd-0a4cd5e8dc70

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIs ... hpY8upQlA2q40g

𝜆 kubectl proxy
```

Visit URL _http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#!/login_

Choose Token, paste the token output from the previous command into the Token field, and choose SIGN IN.

![AWS EKS on Fargate, with eksctl - Nodes](/blog/img/AWS%20EKS%20on%20Fargate,%20with%20eksctl%20-%20Nodes.png "AWS EKS on Fargate, with eksctl - Nodes")

With AWS managed nodes, on Node EC2 instance:

![AWS EKS on Fargate, with eksctl - EC2 instance](/blog/img/AWS%20EKS%20on%20Fargate,%20with%20eksctl%20-%20EC2%20instance.png "AWS EKS on Fargate, with eksctl - EC2 instance")

References
----------

- `eksctl`, a simple CLI tool for creating clusters on Amazon’s new managed Kubernetes service for EC2 - EKS. Written in Go, uses CloudFormation, _https://eksctl.io/_
- AWS EKS, _https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html_