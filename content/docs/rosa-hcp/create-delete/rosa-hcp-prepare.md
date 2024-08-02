---
title: 2. CLIの準備
weight: 1
---

## 1. AWS CLI の準備


[こちらの AWS のページ](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/getting-started-install.html)を参考にして AWS CLI をインストールします。

AWS CLI がインストールされたかどうか、以下の version コマンドで確認できます。

```tpl
aws --version
```

{{< expand "出力例" >}}
```tpl
$ aws --version
aws-cli/2.15.29 Python/3.11.8 Linux/5.15.146.1-microsoft-standard-WSL2 exe/x86_64.ubuntu.22 prompt/off

```
{{< /expand >}}


次に `aws configure` を使用して、`AWS Access Key ID` や `AWS Secret Access Key` の値を構成します。

`AWS Access Key ID` や `AWS Secret Access Key` は、AWS Console から取得できます。[こちら](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_CreateAccessKey) を参考にしてください。

`aws configure` と入力すると、必要な値を聞いてくるので入力します。
また Cluster をインストールする AWS Region も聞いて来ますが、ここでは `ap-northeast-1` と入力します。


```tpl
aws configure
```

{{< expand "構成例" >}}
```tpl
$ aws configure
AWS Access Key ID [None]: ABCDEFG1234567890KKNUK
AWS Secret Access Key [None]: abcedefghijklmn012345678abcdefg
Default region name [None]: an-northeast-1
Default output format [None]: 
$ 
{{< /expand >}}


以下のコマンドを実行して正しく構成されているか確認します。

```tpl
aws sts get-caller-identity
```
{{< expand "出力例" >}}
```tpl
$ aws sts get-caller-identity
{
    "UserId": "ABCD1234BG6WLHYHMKFHK",
    "Account": "123407415212",
    "Arn": "arn:aws:iam::366607415212:user/myname@redhat.com-4w685"
}
```
{{< /expand >}}

## 2. Git CLI の準備

[こちらのページ](https://git-scm.com/book/ja/v2/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-Git%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)を参考に git コマンドをインストールします。

以下のコマンドを実行して正しく構成されているか確認します。
```tpl
git version
```

{{< expand "出力例" >}}
```tpl
$ git version
git version 2.17.1
$
```
{{< /expand >}}

## 3. terraform CLI の準備

[こちらのページ](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)を参考に terraform コマンドをインストールします。

以下のコマンドでバージョンを確認して、インストールを確認します。
```tpl
terraform -v
```

{{< expand "出力例" >}}
```tpl
$ terraform -v
Terraform v1.7.4-dev
on linux_amd64
+ provider registry.terraform.io/hashicorp/aws v4.67.0
+ provider registry.terraform.io/hashicorp/null v3.2.2

Your version of Terraform is out of date! The latest version
is 1.7.4. You can update by downloading from https://www.terraform.io/downloads.html
$ 
```
{{< /expand >}}

## 4. OpenShift / ROSA の CLI の準備

1.OpenShift の`oc`コマンドと ROSA 専用の追加コマンドである`rosa` コマンドをダウンロードして展開します。

```tpl
curl -LO https://mirror.openshift.com/pub/openshift-v4/clients/rosa/latest/rosa-linux.tar.gz
```
```tpl
tar -zxf rosa-linux.tar.gz 
```
```tpl
sudo mv ./rosa /usr/local/bin/
```
```tpl
rosa download oc
```
```tpl
tar -xzf openshift-client-linux.tar.gz 
```
```tpl
sudo mv ./oc /usr/local/bin
```
```tpl
sudo mv ./kubectl /usr/local/bin
```

2.インストールされたコマンドのバージョンを確認します。

`oc` コマンドのバージョンを確認します。`oc` コマンドは `kubectl` を拡張した OpenShift 独自のコマンドです。`kubectl` コマンドとほぼ同じ使い方ができます。


```tpl
oc version
```

{{< expand "出力例" >}}
```tpl
$ oc version
Client Version: 4.14.2
Kustomize Version: v5.0.1
Unable to connect to the server: dial tcp: lookup api.my-hpc-cluster.rc4b.p3.openshiftapps.com on 172.28.240.1:53: no such host
$
```
{{< /expand >}}


`rosa` コマンドのバージョンを確認します。`rosa` コマンドは、主に `oc` コマンドで取り扱う OpenShift のレイヤーより下のレイヤーを取り扱うコマンドです。AWSインフラにアクセスしてクラスターの作成/削除を行ったり、Kubernetes でカバーされていない AWS とのインフラ周りに関連する作業を行う時に使用します。


```tpl
rosa version
```

{{< expand "出力例" >}}
```tpl
$ rosa version
1.2.31
I: Your ROSA CLI is up to date.
```
{{< /expand >}}


## 5. jq コマンドの準備

手順を簡単にするために jq コマンドで JSON の操作をしている部分があるので、jq コマンドをインストールします。

[こちらのページ](https://jqlang.github.io/jq/download/)を参考に jq コマンドをインストールします。

