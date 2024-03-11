---
title: 5. Network の Debug
weight: 1
---

HCP ROSA では、ユーザーが AWS 上に作成した Network 上にクラスターを作成します。
そのため、一番多いトラブルが、AWS Network に関するものです。

ここでは、Network の疎通確認ツールである、`rosa verify network` を使用してみます。
  
`rosa verify network` を使用すると ROSA をインストールする VPC / Subnet から外部レジストリー等と必要な疎通が取れているか確認できます。

## 1. AWS network の SubnetId を確認する


AWS の Subnet id を取得します。

```tpl
aws ec2 describe-subnets | jq -r '.Subnets[] | [ .CidrBlock, .SubnetId, .AvailabilityZone, .Tags[].Value ] | @csv'
```

{{< expand "コマンド実行例" >}}

```tpl
$ aws ec2 describe-subnets | jq -r '.Subnets[] | [ .CidrBlock, .SubnetId, .AvailabilityZone, .Tags[].Value ] | @csv'
"10.0.128.0/24","subnet-07098183112673e5e","us-east-2a","true","1","ROSA","my-hpc-cluster-vpc-public-use2-az1","my-hpc-cluster"
"10.0.0.0/24","subnet-06cf09e21d4ab1e8f","us-east-2a","ROSA","1","true","my-hpc-cluster","my-hpc-cluster-vpc-private-use2-az1"
$
```
{{< /expand >}}

この例では ROSA Public Cluster 用に `subnet-07098183112673e5e` (Pulbic Subnet) と `subnet-06cf09e21d4ab1e8f` (Private Sunbet) を作成しています。


## 2. ManagedOpenShift-Installer-Role IAM Role の ARN を確認する。 


作成された IAM Role は以下の方法で確認できます。

```tpl
rosa list account-roles
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa list account-roles
I: Fetching account roles
ROLE NAME                           ROLE TYPE      ROLE ARN                                                           OPENSHIFT VERSION  AWS Managed
ManagedOpenShift-ControlPlane-Role  Control plane  arn:aws:iam::864046375925:role/ManagedOpenShift-ControlPlane-Role  4.14               No
ManagedOpenShift-Installer-Role     Installer      arn:aws:iam::864046375925:role/ManagedOpenShift-Installer-Role     4.14               No
ManagedOpenShift-Support-Role       Support        arn:aws:iam::864046375925:role/ManagedOpenShift-Support-Role       4.14               No
ManagedOpenShift-Worker-Role        Worker         arn:aws:iam::864046375925:role/ManagedOpenShift-Worker-Role        4.14               No
$ 
```
{{< /expand >}}


## 3. 作成した ROSA 用の Network の疎通を検証する

ネットワークの検証を行います。検証したい `subnet id` と、`ManagedOpenShift-Installer-Role` IAM Role の arn が必要になります。

必要な情報を環境変数にセットします。Subnetが複数ある場合は、カンマで区切って並べます。

```tpl
export REGION=ap-northeast-1
export SUBNET_IDS=subnet-07098183112673e5e,subnet-06cf09e21d4ab1e8f
export INSTALL_IAM_ROLE_ARN=arn:aws:iam::864046375925:role/ManagedOpenShift-Installer-Role
```

以下のコマンドを実行する事で ROSA の稼働に必要な疎通が取れているか確認できます。

```tpl
rosa verify network --watch --region $REGION --subnet-ids $SUBNET_IDS  --role-arn $INSTALL_IAM_ROLE_ARN
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa verify network --watch --region $REGION --subnet-ids $SUBNET_IDS  --role-arn $INSTALL_IAM_ROLE_ARN
I: Verifying the following subnet IDs are configured correctly: [subnet-07098183112673e5e subnet-06cf09e21d4ab1e8f]
I: subnet-07098183112673e5e: passed
I: subnet-06cf09e21d4ab1e8f: passed
$
```
{{< /expand >}}
