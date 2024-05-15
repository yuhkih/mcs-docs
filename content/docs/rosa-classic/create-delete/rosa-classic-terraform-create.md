---
title: 4. Terraform を使用した ROSA Classic Cluster の 作成
weight: 1
draft: true
---

ここでは、最も簡単な方法で ROSA Classic Cluster (Single AZ) を作ってみます。
一番、少ない手順で作成したい場合は、terraform を使うのが手っ取り早いです。

一番、簡単な方法ですが、この方法を覚えておけば、terraform のファイルをカスタマイズして、好みのクラスタを作る事も可能です。

## 1.ROSA 作成用 Token の取得


```tpl
rosa login                                           
```

token を環境変数に設定します。この環境変数は terrafomr から参照されます。


## 2.ROSA Classic Cluster の作成


あらかじめ必要な Terraform の設定ファイルを同梱した Git Repository を作成してあるので、それをダウンロードして、作成されたディレクトリーに移動します。

```tpl
git clone https://github.com/yuhkih/rosa-classic-terraform.git && cd rosa-classic-terraform
```

初期化します。

```tpl
terraform init
```

apply します。50分程でクラスターが作成されされます。

```tpl
terraform apply
```

作成されたクラスターの名前を確認します。

```tpl
rosa list cluster                   
```

出力例
```tpl
$ rosa list clusters
ID                                NAME         STATE  TOPOLOGY
2aa9097v25puk2c01h9i69lqvqc2pj99  rosa-mbkgqo  ready  Classic (STS)
$ 
```




管理者ユーザーを作成します。これで cluster-admin という管理者ユーザーが作成されます。

```tpl
rosa create admin -c $CLUSTER_NAME
```

ログインしてみます。

```tpl
oc login -u cluster-admin -p xxxxx 
```
