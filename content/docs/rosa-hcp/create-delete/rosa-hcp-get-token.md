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

赤い部分をクリックします。

![image](https://github.com/user-attachments/assets/2375642b-41ed-4fd2-9277-49601fa3d772)

Token の表示させて、Token をコピーします。

![image](https://github.com/user-attachments/assets/13bba5b4-a889-4fad-9d67-d0c8cf3f1d08)

{{< hint info >}} 

ここの手順では、敢えて旧式の Token (Red Hat KeyCloack 用語で `オフライントークン`）を使用しています。30日に一度使用していれば、Token は有効で使い続けられ、特に作業に時間がかかる初回の実験環境用には使い勝手が良いためです。
新しく採用された Token 方式の Token は、Security がより強化され 10 hours 毎に更新が必要になります。

{{< /hint >}}

コピーしたToken をプロンプトに貼り付けて、以下のように login を完了させます。

```tpl
$ rosa login
To login to your Red Hat account, get an offline access token at https://console.redhat.com/openshift/token/rosa
? Copy the token and paste it here: ******************************************************************************************************************************************************************************************************

I: Logged in as 'yuhkih' on 'https://api.openshift.com'
$
```
