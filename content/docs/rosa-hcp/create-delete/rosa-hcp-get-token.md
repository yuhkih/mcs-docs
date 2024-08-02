---
title: 3. ROSA 作成用 token の取得
weight: 1
---

## 1. ROSA 作成用の token の取得

実験環境などでは昔の別の作業時の login 時のトークンが残っている可能性があるので、念のため一度 logout します。

```tpl
rosa logout
```

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
