---
title: 2. rosa コマンド
weight: 1
---
# rosa コマンド
`rosa` コマンドは、OpenShift Cluster が設置される AWS 周りのインフラを管理するために OpenShift に追加されたコマンドです。
また `oc` コマンドや `kubectl` コマンドと違い、複数のクラスターを管理する前提で作られています。そのため、通常、対象となるクラスター名を引数に指定します。


## 1. 作成されたクラスターのリスト

アカウントに紐付いている ROSA クラスターの一覧を表示します。

```tpl
rosa list clusters
```

{{< expand "コマンド実行例" >}}

```tpl
$ rosa list clusters
ID                                NAME            STATE  TOPOLOGY
27vfb6mckkl9ndle98vvcgchc2nlc54m  my-hpc-cluster  ready  Hosted CP
$ 
```
{{< /expand >}}

## 2. machinepool のリスト

machinepool は、ROSA で用いられてる Worker Node をグループ化した概念です。
同じサイズのインスタンスで構成されています。

```tpl
rosa list machinepools -c $CLUSTER_NAME
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa list machinepools -c $CLUSTER_NAME
ID         AUTOSCALING  REPLICAS  INSTANCE TYPE  LABELS    TAINTS    AVAILABILITY ZONE  SUBNET                    VERSION  AUTOREPAIR  
workers-0  No           1/1       m5.xlarge                          us-east-2c         subnet-00e5abea4e74f6852  4.14.4   Yes         
workers-1  No           1/1       m5.xlarge                          us-east-2b         subnet-047bd69e1ae06c742  4.14.4   Yes         
workers-2  No           1/1       m5.xlarge                          us-east-2a         subnet-0c52bfe790deb6429  4.14.4   Yes         
$ 
```
{{< /expand >}}

## 3. ingress のリスト

ROSA クラスター内に作成されいてる ingress を表示します。

```tpl
rosa list ingress -c $CLUSTER_NAME
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa list ingress -c $CLUSTER_NAME
ID    APPLICATION ROUTER                                          PRIVATE  DEFAULT  ROUTE SELECTORS  LB-TYPE  EXCLUDED NAMESPACE  WILDCARD POLICY  NAMESPACE OWNERSHIP  HOSTNAME  TLS SECRET REF
c7n0  https://apps.rosa.my-hpc-cluster.pd80.p3.openshiftapps.com  no       yes                       nlb                                                                          
$ 
```
{{< /expand >}}

## 4. ROSA が使用可能なリージョンの表示

ROSA がサポートされている AWS リージョンを表示します。

```tpl
rosa list regions 
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa list regions
ID                NAME                        MULTI-AZ SUPPORT    HOSTED-CP SUPPORT
ap-northeast-1    Asia Pacific, Tokyo         true                false
ap-northeast-2    Asia Pacific, Seoul         true                false
ap-northeast-3    Asia Pacific (Osaka)        true                false
ap-south-1        Asia Pacific, Mumbai        true                false
ap-southeast-1    Asia Pacific, Singapore     true                false
ap-southeast-2    Asia Pacific, Sydney        true                false
ca-central-1      Canada, Central             true                false
eu-central-1      EU, Frankfurt               true                true
eu-north-1        EU, Stockholm               true                false
eu-west-1         EU, Ireland                 true                true
eu-west-2         EU, London                  true                false
eu-west-3         EU, Paris                   true                false
sa-east-1         South America, São Paulo    true                false
us-east-1         US East, N. Virginia        true                true
us-east-2         US East, Ohio               true                true
us-west-1         US West, N. California      false               false
us-west-2         US West, Oregon             true                true
$ 
```
{{< /expand >}}

## 5. インスタンスタイプの表示

ROSA がサポートしている AWS の EC2インスタンスタイプを表示します。

```tpl
 rosa list instance-types
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa list instance-types
ID                 CATEGORY               CPU_CORES  MEMORY  
dl1.24xlarge       accelerated_computing  96         768.0 GiB
g4dn.12xlarge      accelerated_computing  48         192.0 GiB
g4dn.16xlarge      accelerated_computing  64         256.0 GiB
g4dn.2xlarge       accelerated_computing  8          32.0 GiB
g4dn.4xlarge       accelerated_computing  16         64.0 GiB
g4dn.8xlarge       accelerated_computing  32         128.0 GiB

<省略>

m5d.4xlarge        storage_optimized      16         64.0 GiB
m5d.8xlarge        storage_optimized      32         128.0 GiB
m5d.xlarge         storage_optimized      4          16.0 GiB
$ 
```
{{< /expand >}}

## 6. ROSA Cluster 情報の表示

ROSA Cluster の使用している `OpenShift` の `Version` や、割り当てられている `domain name`、使用している AWS サブネット等の基本的な情報を表示します。

```tpl
rosa describe cluster --cluster=$CLUSTER_NAME
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa describe cluster --cluster=$CLUSTER_NAME

Name:                       myhcpcluster
Display Name:               myhcpcluster
ID:                         2a3d7b6j9d98mqlrp0b5che2656ah2tc
External ID:                2c53ac5e-5300-4160-aa6c-5e764a37f555
Control Plane:              ROSA Service Hosted
OpenShift Version:          4.14.16
Channel Group:              stable
DNS:                        myhcpcluster.rc4b.p3.openshiftapps.com
AWS Account:                337001183099
AWS Billing Account:        337001183099
API URL:                    https://api.myhcpcluster.rc4b.p3.openshiftapps.com:443
Console URL:                https://console-openshift-console.apps.rosa.myhcpcluster.rc4b.p3.openshiftapps.com
Region:                     ap-northeast-1
Availability:
 - Control Plane:           MultiAZ
 - Data Plane:              SingleAZ
Nodes:
 - Compute (desired):       2
 - Compute (current):       2
Network:
 - Type:                    OVNKubernetes
 - Service CIDR:            172.30.0.0/16
 - Machine CIDR:            10.0.0.0/16
 - Pod CIDR:                10.128.0.0/14
 - Host Prefix:             /23
 - Subnets:                 subnet-0171ae3ce0b83daae, subnet-0625be1c3ce548538
EC2 Metadata Http Tokens:   optional
Role (STS) ARN:             arn:aws:iam::337001183099:role/ManagedOpenShift-HCP-ROSA-Installer-Role
Support Role ARN:           arn:aws:iam::337001183099:role/ManagedOpenShift-HCP-ROSA-Support-Role
Instance IAM Roles:
 - Worker:                  arn:aws:iam::337001183099:role/ManagedOpenShift-HCP-ROSA-Worker-Role
Operator IAM Roles:
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-openshift-image-registry-installer-cloud-crede
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-openshift-ingress-operator-cloud-credentials
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-kube-system-kube-controller-manager
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-kube-system-capa-controller-manager
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-kube-system-control-plane-operator
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-kube-system-kms-provider
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-openshift-cluster-csi-drivers-ebs-cloud-creden
 - arn:aws:iam::337001183099:role/myhcpcluster-z3s7-openshift-cloud-network-config-controller-clou
Managed Policies:           Yes
State:                      ready 
Private:                    No
Created:                    Mar 19 2024 05:25:04 UTC
User Workload Monitoring:   Enabled
Details Page:               https://console.redhat.com/openshift/details/s/2dtSVRvDHK0u3NXFytuM2t4xdI6
OIDC Endpoint URL:          https://rh-oidc.s3.us-east-1.amazonaws.com/24a7tma9k0v2fnjcg6904pbt4pvv6mgh (Managed)
Audit Log Forwarding:       Disabled

$ 
```
{{< /expand >}}

