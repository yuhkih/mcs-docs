---
title: 5. ROSA HCP Cluster の作成
weight: 1
---

## 1.ROSA HCP Cluster の 作成

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

クラスターを作成するには、Red Hat Customer ポータルの User アカウントが必要です。無料で作成できます。Red Hat Customer ポータルの Userを作成した後、以下のコマンドでログインします。

```tpl
rosa login
```


必要な IAM Role を作成します。(インタラクティブに構成したい場合は `-y -m auto` を外します)

```tpl
rosa create account-roles --hosted-cp -y -m auto
```

OIDC Config を作成します。(インタラクティブに構成したい場合は `-y -m auto` を外します)

```tpl
 rosa create oidc-config  -y -m auto
```

Cluster の作成を開始します。いろいろ聞かれますが、全てデフォルトでエンターを叩いて大丈夫です。

```tpl
rosa create cluster --cluster-name=$CLUSTER_NAME --sts --hosted-cp  --region=$REGION --subnet-ids=$SUBNET_IDS
```
{{< expand "コマンド実行例" >}}
```tpl
$ rosa create cluster --cluster-name=$CLUSTER_NAME --sts --hosted-cp  --region=$REGION --subnet-ids=$SUBNET_IDS
I: Using '993114993799' as billing account
I: To use a different billing account, add --billing-account xxxxxxxxxx to previous command
I: Using arn:aws:iam::993114993799:role/ManagedOpenShift-HCP-ROSA-Installer-Role for the Installer role
I: Using arn:aws:iam::993114993799:role/ManagedOpenShift-HCP-ROSA-Worker-Role for the Worker role
I: Using arn:aws:iam::993114993799:role/ManagedOpenShift-HCP-ROSA-Support-Role for the Support role
? OIDC Configuration ID (default = '29vmlp9v462arcnt8fh1g9cfada8njd7 | https://oidc.op1.openshiftapps.com/29vmlp9v462arcnt8fh1g9cfada8njd7'): 29vmlp9v462arcnt8fh1g9cfada8njd7 | https://oidc.op1.openshiftapps.com/29vmlp9v462arcnt8fh1g9cfada8njd7
? Tags (optional): 
? AWS region (default = 'ap-northeast-1'): ap-northeast-1
? PrivateLink cluster: No
? Machine CIDR: 10.0.0.0/16
? Service CIDR: 172.30.0.0/16
? Pod CIDR: 10.128.0.0/14
? Enable Customer Managed key: No
? Compute nodes instance type (optional, choose 'Skip' to skip selection. The default value will be provided; default = 'm5.xlarge'): m5.xlarge
? Enable autoscaling: No
? Compute nodes: 2
? Host prefix: 23
? Encrypt etcd data: No
? Disable Workload monitoring: No
? Use cluster-wide proxy: No
? Additional trust bundle file path (optional): 
? Enable audit log forwarding to AWS CloudWatch: No
I: Creating cluster 'myhcpcluster'
I: To create this cluster again in the future, you can run:
   rosa create cluster --cluster-name myhcpcluster --sts --role-arn arn:aws:iam::923114993793:role/ManagedOpenShift-HCP-ROSA-Installer-Role --support-role-arn arn:aws:iam::923114993793:role/ManagedOpenShift-HCP-ROSA-Support-Role --worker-iam-role arn:aws:iam::923114993793:role/ManagedOpenShift-HCP-ROSA-Worker-Role --operator-roles-prefix myhcpcluster-a2m3 --oidc-config-id 29vmlp9v462arcnt8fh1g9cfada8njd7 --region ap-northeast-1 --version 4.14.15 --replicas 2 --compute-machine-type m5.xlarge --machine-cidr 10.0.0.0/16 --service-cidr 172.30.0.0/16 --pod-cidr 10.128.0.0/14 --host-prefix 23 --subnet-ids subnet-041a58ec4568049f4,subnet-02357affbc76a1061 --hosted-cp --billing-account 923114993793
I: To view a list of clusters and their status, run 'rosa list clusters'
I: Cluster 'myhcpcluster' has been created.
I: Once the cluster is installed you will need to add an Identity Provider before you can login into the cluster. See 'rosa create idp --help' for more information.

Name:                       myhcpcluster
Display Name:               myhcpcluster
ID:                         29vmnb67qqn61dpt060iojs11uuilsm1
External ID:                b4bae8e8-9ec7-457e-be80-4cef6b0fa9ac
Control Plane:              ROSA Service Hosted
OpenShift Version:          4.14.15
Channel Group:              stable
DNS:                        Not ready
AWS Account:                993114993799
AWS Billing Account:        993114993799
API URL:                    
Console URL:                
Region:                     ap-northeast-1
Availability:
 - Control Plane:           MultiAZ
 - Data Plane:              SingleAZ
Nodes:
 - Compute (desired):       2
 - Compute (current):       0
Network:
 - Type:                    OVNKubernetes
 - Service CIDR:            172.30.0.0/16
 - Machine CIDR:            10.0.0.0/16
 - Pod CIDR:                10.128.0.0/14
 - Host Prefix:             /23
 - Subnets:                 subnet-041a58ec4568049f4, subnet-02357affbc76a1061
EC2 Metadata Http Tokens:   optional
Role (STS) ARN:             arn:aws:iam::923114993793:role/ManagedOpenShift-HCP-ROSA-Installer-Role
Support Role ARN:           arn:aws:iam::923114993793:role/ManagedOpenShift-HCP-ROSA-Support-Role
Instance IAM Roles:
 - Worker:                  arn:aws:iam::923114993793:role/ManagedOpenShift-HCP-ROSA-Worker-Role
Operator IAM Roles:
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-kube-system-capa-controller-manager
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-openshift-cloud-network-config-controller-clou
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-openshift-image-registry-installer-cloud-crede
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-openshift-ingress-operator-cloud-credentials
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-openshift-cluster-csi-drivers-ebs-cloud-creden
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-kube-system-control-plane-operator
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-kube-system-kms-provider
 - arn:aws:iam::923114993793:role/myhcpcluster-a2m3-kube-system-kube-controller-manager
Managed Policies:           Yes
State:                      waiting (Waiting for user action)
Private:                    No
Created:                    Mar 13 2024 14:35:28 UTC
User Workload Monitoring:   Enabled
Details Page:               https://console.redhat.com/openshift/details/s/2ddahR6fghzwm5U8VmadJ80aHnT
OIDC Endpoint URL:          https://oidc.op1.openshiftapps.com/29vmlp9v462arcnt8fh1g9cfada8njd7 (Managed)
Audit Log Forwarding:       Disabled

I: Run the following commands to continue the cluster creation:

        rosa create operator-roles --cluster myhcpcluster

I: To determine when your cluster is Ready, run 'rosa describe cluster -c myhcpcluster'.
I: To watch your cluster installation logs, run 'rosa logs install -c myhcpcluster --watch'.
$ 
```
{{< /expand >}}

{{< expand "\"billing account is required\" エラー" >}}
以下のエラーが出た時は、HCPが有効化されてなかったり AWSアカウントとRed Hatアカウントが紐付いてない可能性があります。
"1.ROSA HCPの有効化" の手順を再実行して、`rosa logout` した後 `rosa login` してから再実行してみてください。

```
$ rosa create cluster --cluster-name=$CLUSTER_NAME --sts --hosted-cp  --region=$REGION --subnet-ids=$SUBNET_IDS
E: A billing account is required for Hosted Control Plane clusters. To see the list of billing account options, you can use interactive mode by passing '-i'.
I: Using arn:aws:iam::378713198531:role/ManagedOpenShift-HCP-ROSA-Installer-Role for the Installer role
I: Using arn:aws:iam::378713198531:role/ManagedOpenShift-HCP-ROSA-Worker-Role for the Worker role
< 省略 > 
```
{{< /expand >}}

Cluster の作成を開始した後に Operator Role を作成します。**これを行わないと Cluster の作成が進行しないのでご注意下さい。**
```tpl
rosa create operator-roles --cluster $CLUSTER_NAME -m auto --yes
```

Cluster の作成を開始した後に、OIDC Provider を作成します。**これを行わないと Cluster の作成が進行しないのでご注意下さい。**
```tpl
rosa create oidc-provider --cluster $CLUSTER_NAME -m auto --yes
```

ROSA のクラスターができるまで以下のコマンドでモニターします。大体 10分ほどかかるはずです。

```tpl
rosa logs install -c $CLUSTER_NAME --watch
```
{{< expand "コマンド実行例" >}}

```tpl
$ rosa logs install -c $CLUSTER_NAME --watch
I: Cluster 'myhcpcluster' is in validating state waiting for installation to begin. Logs will show up within 5 minutes
| 0001-01-01 00:00:00 +0000 UTC hostedclusters myhcpcluster Version 
2024-03-13 14:40:51 +0000 UTC hostedclusters myhcpcluster Condition not found in the CVO.
2024-03-13 14:40:51 +0000 UTC hostedclusters myhcpcluster The hosted control plane is not found
2024-03-13 14:40:51 +0000 UTC hostedclusters myhcpcluster Condition not found in the CVO.
2024-03-13 14:40:51 +0000 UTC hostedclusters myhcpcluster Condition not found in the CVO.
2024-03-13 14:40:51 +0000 UTC hostedclusters myhcpcluster Condition not found in the CVO.
<省略>
ployment has 1 unavailable replicas, control-plane-operator deployment has 1 unavailable replicas]
2024-03-13 14:42:51 +0000 UTC hostedclusters myhcpcluster Configuration passes validation
2024-03-13 14:42:51 +0000 UTC hostedclusters myhcpcluster AWS KMS is not configured
2024-03-13 14:42:51 +0000 UTC hostedclusters myhcpcluster EtcdAvailable StatefulSetNotFound
2024-03-13 14:42:51 +0000 UTC hostedclusters myhcpcluster Kube APIServer deployment not found
2024-03-13 14:42:59 +0000 UTC hostedclusters myhcpcluster All is well
| 2024-03-13 14:43:46 +0000 UTC hostedclusters myhcpcluster EtcdAvailable QuorumAvailable
- 2024-03-13 14:44:50 +0000 UTC hostedclusters myhcpcluster Kube APIServer deployment is available
2024-03-13 14:44:57 +0000 UTC hostedclusters myhcpcluster All is well
| 2024-03-13 14:45:53 +0000 UTC hostedclusters myhcpcluster [catalog-operator deployment has 1 unavailable replicas, certified-operators-catalog deployment has 2 unavailable replicas, cluster-autoscaler deployment has 1 unavailable replicas, cluster-image-registry-operator deployment has 1 unavailable replicas, cluster-network-operator deployment has 1 unavailable replicas, cluster-storage-operator deployment has 1 unavailable replicas, community-operators-catalog deployment has 2 unavailable replicas, csi-snapshot-controller-operator deployment has 1 unavailable replicas, dns-operator deployment has 1 unavailable replicas, hosted-cluster-config-operator deployment has 1 unavailable replicas, ignition-server deployment has 3 unavailable replicas, ingress-operator deployment has 1 unavailable replicas, machine-approver deployment has 1 unavailable replicas, oauth-openshift deployment has 2 unavailable replicas, olm-operator deployment has 1 unavailable replicas, packageserver deployment has 3 unavailable replicas, redhat-marketplace-catalog deployment has 2 unavailable replicas, redhat-operators-catalog deployment has 2 unavailable replicas, router deployment has 1 unavailable replicas]
2024-03-13 14:46:04 +0000 UTC hostedclusters myhcpcluster The hosted control plane is available
/ I: Cluster 'myhcpcluster' is now ready
$ 
{{< /expand >}}

## 2.ROSA HCP Cluster へのアクセス確認

インストールが完了したら管理者ユーザーを作成します。
ログインコマンド (`oc login`) パスワード付きで標準出力に表示されます。これはコマンドが終了してから、数分待つ必要があります。

```tpl
rosa create admin --cluster=$CLUSTER_NAME
```
`rosa create admin` 実行時に出力に以下のようなログイン用のコマンドが出てくるのでメモしておきます。

```tpl
oc login https://api.my-hpc-cluster.rc4b.p3.openshiftapps.com:443 --username cluster-admin --password abc123-XYZZH-1dNpZ-DBVjg
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


数分待ってから、`rosa create admin`の出力で現れた上記のコマンドを使ってログインコマンド(`oc login`) を実行します。
(準備ができるまで 401 Unauthorized が出ます) 

```tpl
oc login <API_SERVER> --username cluster-admin --password <PASSWORD>
```

{{< expand "コマンド実行例" >}}
```bash
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
```tpl
$ oc get nodes
NAME                                       STATUS   ROLES    AGE     VERSION
ip-10-0-0-72.us-east-2.compute.internal    Ready    worker   9m44s   v1.27.6+b49f9d1
ip-10-0-1-195.us-east-2.compute.internal   Ready    worker   70s     v1.27.6+b49f9d1
ip-10-0-2-242.us-east-2.compute.internal   Ready    worker   9m28s   v1.27.6+b49f9d1
$
```
{{< /expand >}}

## 3.構成を探って見る

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

