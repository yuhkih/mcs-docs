---
title: 3. ROSA HCP Cluster の作成
weight: 1
---

## 1. Terraform を使用した ROSA を install する AWS Network の作成

HCP ROSA は、ユーザーが既にもっているネットワークにデプロイする事が前提になります。
ここでは Terraform を使用して AWS　上に Network を作成します。

この手順は、公式ドキュメントの「<a href="https://docs.openshift.com/rosa/rosa_hcp/rosa-hcp-sts-creating-a-cluster-quickly.html#rosa-hcp-creating-vpc" target="_blank">Creating a Vritual Private Cloud for your ROSA with HCP clusters</a>
」をベースにしています。


### 1.1. Terraform を使用した AWS Network リソースの作成

サンプルで提供されている terraform のテンプレートを使って、AWS の VPC、Subnet、NAT Gateway 等の必要なリソースを作成します。

```tpl
git clone https://github.com/openshift-cs/terraform-vpc-example
```

```tpl
cd terraform-vpc-example
```

```tpl
terraform init
```

変数を準備しておきます。

```tpl
export CLUSTER_NAME=myhcpcluster
```
```tpl
export REGION=ap-northeast-1
```

Terraform の plan を作成します。

Single AZ の Network 構成をデプロイするか、Multi AZ の Network をデプロイするか、どちらかを選びます。


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


Plan を apply して Network を作成します。

```tpl
terraform apply rosa.tfplan
```

作成された AWS のサブネットIDを変数にセットしておきます。カンマ区切りで6つのサブネットIDが変数にセットされます。

```tpl
export SUBNET_IDS=$(terraform output -raw cluster-subnets-string)
```

### 1.3. 作成された Subnet と NAT Gateway の確認

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

## 2.ROSA HCP Cluster の 作成

クラスターを作成するには、Red Hat Customer ポータルの User アカウントが必要です。無料で作成できます。Red Hat Customer ポータルの Userを作成した後、以下のコマンドでログインします。

```tpl
rosa login
```


必要な IAM Role を作成します。いろいろ聞かれますが、全てデフォルトで大丈夫です。

```tpl
rosa create account-roles --hosted-cp
```

必要な変数が全てセットされているか再確認します。もしセットされてない場合は、以前の手順に戻ってセットして下さい。

```tpl
echo $CLUSTER_NAME
```
```tpl
echo $REGION
```
```tpl
echo $SUBNET_IDS
```

Cluster の作成を開始します。いろいろ聞かれますが、全てデフォルトで大丈夫です。

```tpl
rosa create cluster --cluster-name=$CLUSTER_NAME --sts --hosted-cp  --region=$REGION --subnet-ids=$SUBNET_IDS
```

Cluster の作成を開始した後に Operator Role を作成します。

```tpl
rosa create operator-roles --cluster $CLUSTER_NAME -m auto --yes
```

OIDC Provider を作成します。

```tpl
rosa create oidc-provider --cluster  $CLUSTER_NAME -m auto --yes
```

ROSA のクラスターができるまで以下のコマンドでモニターします。大体 10分ほどかかるはずです。

```tpl
rosa logs install -c $CLUSTER_NAME --watch
```



## 3.ROSA HCP Cluster へのアクセス確認

インストールが完了したら管理者ユーザーを作成します。
ログインコマンド (`oc login`) パスワード付きで標準出力に表示されます。これはコマンドが終了してから、数分待つ必要があります。

```tpl
rosa create admin --cluster=$CLUSTER_NAME
```
{{< expand "コマンド実行例" >}}

```tpl
$ rosa create admin --cluster=$CLUSTER_NAME
I: Admin account has been added to cluster 'my-hpc-cluster'.
I: Please securely store this generated password. If you lose this password you can delete and recreate the cluster admin user.
I: To login, run the following command:

   oc login https://api.my-hpc-cluster.rc4b.p3.openshiftapps.com:443 --username cluster-admin --password abc123-XYZZH-1dNpZ-DBVjg

I: It may take several minutes for this access to become active.
$
```
{{< /expand >}}

数分待ってから、上の出力で現れた api server の URL、password を使ってログインコマンド(`oc login`) を実行します。
(準備ができるまで 401 Unauthorized が出ます) 

```tpl
oc login <API Server> --username cluster-admin --password <password>
```

{{< expand "コマンド実行例" >}}
```
$  oc login https://api.my-hpc-cluster.rc4b.p3.openshiftapps.com:443 --username cluster-admin --password abc123-XYZZH-1dNpZ-DBVjg
Login successful.

You have access to 77 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
$ 
```
{{< /expand >}}

`oc get nodes` コマンドで compute node ができたか確認します。Worker node が 3本表示されるはずです。
(まれに node の作成に時間がかかる場合があります。何も node が表示されない場合は、さらに10分程度待ってく見てください)

```tpl
oc get nodes
```
{{< expand "コマンド実行例" >}}
```
$ oc get nodes
NAME                                       STATUS   ROLES    AGE     VERSION
ip-10-0-0-72.us-east-2.compute.internal    Ready    worker   9m44s   v1.27.6+b49f9d1
ip-10-0-1-195.us-east-2.compute.internal   Ready    worker   70s     v1.27.6+b49f9d1
ip-10-0-2-242.us-east-2.compute.internal   Ready    worker   9m28s   v1.27.6+b49f9d1
$
```
{{< /expand >}}

## 4.構成を探って見る

`rosa list machinepool` コマンドで、AZ毎に `machinepool` が出来ている事を確認します。`machinepool`単位で Node 数を増やす事ができます。

```tpl
$ rosa list machinepool -c $CLUSTER_NAME
```

{{< expand "コマンド実行例" >}}
```tpl
$ rosa list machinepool -c $CLUSTER_NAME
ID         AUTOSCALING  REPLICAS  INSTANCE TYPE  LABELS    TAINTS    AVAILABILITY ZONE  SUBNET                    VERSION  AUTOREPAIR  
workers-0  No           1/1       m5.xlarge                          us-east-2b         subnet-084bb65941bee3d24  4.14.3   Yes         
workers-1  No           1/1       m5.xlarge                          us-east-2a         subnet-0f0b7ebc07df35c69  4.14.3   Yes         
workers-2  No           1/1       m5.xlarge                          us-east-2c         subnet-0fdeb4dc0c5415267  4.14.3   Yes         
$ 
```
{{< /expand >}}


`rosa list ingress` コマンドで Cluster と一緒に作成された ingress を確認してみます。default の Load Balancer には NLB が使われているはずです。`LB-TYPE` を確認します。
この ingress 経由で、HTTP/HTTPS アプリケーションが公開されます。

```tpl
rosa list ingress -c $CLUSTER_NAME
```
{{< expand "コマンド実行例" >}}
```tpl
$ rosa list ingress -c $CLUSTER_NAME
ID    APPLICATION ROUTER                                          PRIVATE  DEFAULT  ROUTE SELECTORS  LB-TYPE  EXCLUDED NAMESPACE  WILDCARD POLICY  NAMESPACE OWNERSHIP  HOSTNAME  TLS SECRET REF
m3x6  https://apps.rosa.my-hpc-cluster.rc4b.p3.openshiftapps.com  no       yes                       nlb                                                                          
$
```
{{< /expand >}}

