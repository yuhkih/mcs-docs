---
title: 1. ROSA HCP の有効化
weight: 1
---
## 1. Red Hat Account の作成

Red Hat Account を作成します。メールアドレス等が必要になります。
この Red Hat Account を使用して、サポートの問い合わせなどを行います。

[こちら](https://console.redhat.com/connect/aws) にアクセスして作成します。Red Hat アカウントは無料で作成できます。


## 2. ROSA の有効化と前提条件の確認

### 2.1. ROSA HCP の有効化
以下のリンクをクリックして AWS の ROSA 設定画面に飛びます。
[https://console.aws.amazon.com/rosa/home#/get-started](https://console.aws.amazon.com/rosa/home#/get-started)

[Enable ROSA with HCP] のボタンをクリックします。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/0c4152d4-c51a-40c2-9440-bc89cfaaf03e)

有効化されるまで、暫く待ちます。数分かかるはずです。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/7eee2757-c526-4e28-8666-d5f4b4fce290)

有効化が完了すると以下のような表示になります。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/29a2de09-8041-46a1-9852-34b2bd52709c)

### 2.2. Service Quota の確認

もし Service Quota が足りない場合はチケットを上げて確認します。最終的に以下の状態になっていれば大丈夫です。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/41b134f4-8cbc-48bc-b023-104082d3550b)

### 2.3. ELB サービスにリンクされたロールの作成

過去に ELB をデプロイした事があれば `AWSServiceRoleForElasticLoadBalancing` というIAM Role が作成されており、以下のような表示になっているはずです。

![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/35975e14-6847-4b9a-b36e-b52295f0891d)

もし作成されていない場合は、以下のコマンドで作成します。

```tpl
aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
```
### 2.4. Red Hat Customer Portal の情報とリンクする

画面の一番下に移動して「Continue Red Hat」をクリックします。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/87d5a503-7a0a-4a51-9c08-0aa7ad2dc026)

Red Hat の ポータルサイトにログインします。(Red Hat Customer Portal アカウントが無い場合は、作成してから、再度[こちら](https://console.redhat.com/connect/aws) にアクセスします。Red Hat アカウントは無料で作成できます）
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/565e0b8d-eada-4d52-a1c3-16c58bae93fa)

日本語を選んで「Connect accounts] をクリックします。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/2148e419-4753-421b-a572-bcefc2660df3)

以上で前提条件の確認は完了です。


## 3. ROSA 作成用の token の取得

ROSA cluster を作成するためには、Red Hat が提供する token が必要です。以下のコマンドを実行します。

```tpl
rosa login
```

以下のように聞かれます。

```tpl
$ rosa login
To login to your Red Hat account, get an offline access token at https://console.redhat.com/openshift/token/rosa
? Copy the token and paste it here:
```

表示されたリンク [https://console.redhat.com/openshift/token/rosa](https://console.redhat.com/openshift/token/rosa) にログインして、token を取得します。Red Hat Portal の ID (無料) が必要になるので、作って無い場合は、作成してからこのリンクに再びアクセスします。

以下の画面が表示されるので、Token をコピーしてプロンプトに貼り付けます。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/a4f34b68-c230-4b55-b3b2-7e602d76a62c)

以下のように login を完了させます。

```tpl
$ rosa login
To login to your Red Hat account, get an offline access token at https://console.redhat.com/openshift/token/rosa
? Copy the token and paste it here: ******************************************************************************************************************************************************************************************************

I: Logged in as 'yuhkih' on 'https://api.openshift.com'
$
```