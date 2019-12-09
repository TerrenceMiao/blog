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

Kubernetes Dashboard
--------------------

Install Kubernetes Dashboard in Kubernetes cluster:

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

With AWS managed nodes, on Node EC2 Instance:

![AWS EKS on Fargate, with eksctl - EC2 Instance](/blog/img/AWS%20EKS%20on%20Fargate,%20with%20eksctl%20-%20EC2%20Instance.png "AWS EKS on Fargate, with eksctl - EC2 Instance")

First Docker application
------------------------

Deploy first Docker application `react-typescript`, from Docker Hub _https://hub.docker.com/r/jtech/react-typescript_, into Kubernetes.

```console
𝜆 kubectl run react-typescript --image=jtech/react-typescript --port 3000
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/react-typescript created

𝜆 kubectl describe deployments
Name:                   react-typescript
Namespace:              default
CreationTimestamp:      Mon, 09 Dec 2019 14:56:09 +1100
Labels:                 run=react-typescript
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=react-typescript
Replicas:               1 desired | 1 updated | 1 total | 0 available | 1 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=react-typescript
  Containers:
   react-typescript:
    Image:        jtech/react-typescript
    Port:         3000/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      False   MinimumReplicasUnavailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  <none>
NewReplicaSet:   react-typescript-867c948446 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  71s   deployment-controller  Scaled up replica set react-typescript-867c948446 to 1

𝜆 kubectl describe pods react-typescript
Name:               react-typescript-867c948446-qtvrp
Namespace:          default
Priority:           2000001000
PriorityClassName:  system-node-critical
Node:               fargate-ip-192-168-183-250.us-east-2.compute.internal/192.168.183.250
Start Time:         Mon, 09 Dec 2019 14:56:59 +1100
Labels:             eks.amazonaws.com/fargate-profile=fp-default
                    pod-template-hash=867c948446
                    run=react-typescript
Annotations:        kubernetes.io/psp: eks.privileged
Status:             Running
IP:                 192.168.183.250
Controlled By:      ReplicaSet/react-typescript-867c948446
Containers:
  react-typescript:
    Container ID:   containerd://2ea5f1ea4fb731eb844f0e267581e9e188d29ab7a639b7b8ca50c83cfb12b4c3
    Image:          jtech/react-typescript
    Image ID:       docker.io/jtech/react-typescript@sha256:0951fe4d9a24390123c7aa23612c8cdf1d8191a9d8e7d3cbc8bb4d8d763e0ce5
    Port:           3000/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Mon, 09 Dec 2019 14:57:28 +1100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-knpqq (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-knpqq:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-knpqq
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason   Age   From                                                            Message
  ----    ------   ----  ----                                                            -------
  Normal  Pulling  76s   kubelet, fargate-ip-192-168-183-250.us-east-2.compute.internal  Pulling image "jtech/react-typescript"
  Normal  Pulled   49s   kubelet, fargate-ip-192-168-183-250.us-east-2.compute.internal  Successfully pulled image "jtech/react-typescript"
  Normal  Created  49s   kubelet, fargate-ip-192-168-183-250.us-east-2.compute.internal  Created container react-typescript
  Normal  Started  49s   kubelet, fargate-ip-192-168-183-250.us-east-2.compute.internal  Started container react-typescript
```

Expose service:

```console
𝜆 kubectl expose deployment react-typescript --type="NodePort"
service/react-typescript exposed

𝜆 kubectl describe services react-typescript
Name:                     react-typescript
Namespace:                default
Labels:                   run=react-typescript
Annotations:              <none>
Selector:                 run=react-typescript
Type:                     NodePort
IP:                       10.100.54.37
Port:                     <unset>  3000/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  31799/TCP
Endpoints:                192.168.183.250:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

𝜆 kubectl get services  --all-namespaces
NAMESPACE              NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
default                kubernetes                  ClusterIP   10.100.0.1       <none>        443/TCP          46h
default                react-typescript            NodePort    10.100.54.37     <none>        3000:31799/TCP   4m55s
kube-system            kube-dns                    ClusterIP   10.100.0.10      <none>        53/UDP,53/TCP    46h
kube-system            metrics-server              ClusterIP   10.100.142.106   <none>        443/TCP          45h
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.100.91.78     <none>        8000/TCP         45h
kubernetes-dashboard   kubernetes-dashboard        ClusterIP   10.100.75.0      <none>        443/TCP          45h
```

Run `kubectl proxy` and connect to `react-typscript` application on URL: http://localhost:8001/api/v1/namespaces/default/services/http:react-typescript:3000/proxy/

![AWS EKS on Fargate, with eksctl - React Typescript](/blog/img/AWS%20EKS%20on%20Fargate,%20with%20eksctl%20-%20React%20Typescript.png "AWS EKS on Fargate, with eksctl - React Typescript")

References
----------

- `eksctl`, a simple CLI tool for creating clusters on Amazon’s new managed Kubernetes service for EC2 - EKS. Written in Go, uses CloudFormation, _https://eksctl.io/_
- AWS EKS, _https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html_
- Tutorial: Deploy Kubernetes Dashboard, _https://docs.aws.amazon.com/eks/latest/userguide/dashboard-tutorial.html_