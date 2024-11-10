---
title: 4. Cluster 用の Network の作成
weight: 1
---

## 1. Terraform を使用した ROSA を install する AWS Network の作成

HCP ROSA は、ユーザーが既にもっているネットワークにデプロイする事が前提になります。
ここでは Terraform を使用して AWS　上に Network を作成します。

この手順は、公式ドキュメントの「<a href="https://docs.openshift.com/rosa/rosa_hcp/rosa-hcp-sts-creating-a-cluster-quickly.html#rosa-hcp-creating-vpc" target="_blank">Creating a Vritual Private Cloud for your ROSA with HCP clusters</a>
」をベースにしています。


### 1.1. Terraform を使用した AWS Network リソースの作成

サンプルで提供されている terraform のテンプレートを使って、AWS の VPC、Subnet、NAT Gateway 等の必要なリソースを作成します。

1. Repository からサンプルの terraform をダウンロードし初期化します。

```tpl
git clone https://github.com/openshift-cs/terraform-vpc-example
```

この Network 作成用の terraform は、単純にネットワークを作成するだけだく、[こちら](https://repost.aws/knowledge-center/eks-vpc-subnet-discovery) に記載されいているサブネットに付けるタグもあからじめ付けてくれるようになっています。

ダウンロードしたディレクトリに移動します。

```tpl
cd terraform-vpc-example
```
初期化します。

```tpl
terraform init
```

2. 変数を準備しておきます。

`CLUSTER_NAME` は自分の好きなクラスター名で大丈夫です。この名前は Subnet のタグ名で使われます。この変数は、後で ROSA Cluster を作成する時にも使われます。

```tpl
export CLUSTER_NAME=myhcpcluster
```

`REGION` には、`ap-northeast-1` にクラスターを指定します。もちろん他のリージョンを指定しても大丈夫です。この変数は後で、ROSA Cluster の作成先を指定する時にも使われます。

```tpl
export REGION=ap-northeast-1
```

3. Terraform の plan を作成します。

Single AZ の Network 構成をデプロイするか、Multi AZ の Network を作成するか、どちらかを選びます。


{{< tabs "deply network type" >}}
# Single AZ Network
{{< tab "Single AZ Network" >}}

```tpl
terraform plan -out rosa.tfplan -var region=$REGION -var cluster_name=$CLUSTER_NAME 
```


{{< /tab >}}


# Multi AZ Network
{{< tab "Multi AZ Network" >}}

```tpl
terraform plan -out rosa.tfplan -var region=$REGION -var cluster_name=$CLUSTER_NAME -var single_az_only=false
```
{{< /tab >}}

{{< /tabs >}}


4. Plan を apply して Network を作成します。

```tpl
terraform apply rosa.tfplan
```

実際に VPC や Subnet、NAT Gateway 等が作成されるので数分、時間がかかります。

5. 作成された AWS のサブネットIDを変数にセットしておきます。カンマ区切りで2つ(Single AZ) もしくは6つ(Multi AZ) のサブネットIDが変数にセットされます。

```tpl
export SUBNET_IDS=$(terraform output -raw cluster-subnets-string)
```

{{< hint info >}}
この手順ではインターネットに公開されたクラスタ (`Public Cluster`)を作成するため、Network も `NAT Gateway`を持った  `Public Subnet` と `Private Subnet`の両方を作成しています。

`Public Cluster` を作った後に、インターネットに公開された `API Server` 、`Ingress` を非公開(`Private Subnet` からのみアクセス可能にする)に設定する事も可能です。そのためほとんどの要件は `Public Cluster` でカバーできると思います。

一方、あらかじめ非公開のクラスタ(`Private Cluster`)を作る方法もあります。この場合は、AWS の `Pubilc Subnet` は必要はありません。ただし、後から ROSA 側の機能で `Ingress` をインターネットに公開する形に変更することはできません。`Private Cluster` については、Network 構成として少し難易度が高い構成になるので、ここでは取り扱いません。
{{< /hint >}}

以上で Network の準備は完了です。

### 1.2. 作成された Subnet と NAT Gateway の確認 (オプショナル)


ROSA の構築で一番のはまりポイントは、AWS Network の構成です。この手順では terraform で Network を構成するので、嵌まる事はまずありませんが、手動で AWS GUI から作成した場合などはきちんと構成できてない事がありデバッグ用に CLI を覚えて置くと便利です。

ここでは CLI を使って作成した Network の情報を確認する方法をご紹介します。

{{< expand "AWS CLI 実行例" >}}

terraform で apply した時のログにも出ていますが、ここでは AWS CLI の練習も兼ねて、AWS CLI を使用して作成された VPC と Subnet 等を確認します。

VPC をリストします。
```tpl
aws ec2 describe-vpcs | jq -r '.Vpcs[] | [.CidrBlock, .VpcId, .State] | @csv'
```

Subnet をリストします。
```tpl
aws ec2 describe-subnets | jq -r '.Subnets[] | [ .CidrBlock, .SubnetId, .AvailabilityZone, .Tags[].Value ] | @csv'
```

NAT Gateway をリストします。
```tpl
aws ec2 describe-nat-gateways | jq -r '.NatGateways[] | [.NatGatewayId, .State] | @csv'
```
{{< /expand >}}


