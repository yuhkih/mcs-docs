---
title: 2. oc new-app を使った Hello world コンテナのデプロイ
weight: 1
---

## 1. OpenShift 独自のコマンドを使用して簡単なサンプルコンテナをデプロイしてみる

今度は OpenShift の独自コマンド `oc new-app`を使用して、一番はじめにデプロイした `hello-openshift` コンテナをデプロイしてみます。

### 1.1. Project の作成

1. 新しい `project` を作成します。

`project` は、 `namespace` を拡張した OpenShift 独自の概念です。

```tpl
oc new-project hello-openshift2
```

明示的に project を変更しなくても、新しい project にスイッチしています。

### 1.2. deployment / service の作成

1. 以下のコマンドで `hello-openshift` コンテナを使った `deployment` と `service` を一気に作成します。この `oc new-app` は OpenShift の独自コマンドです。
container のイメージが公開している port の情報を持っている場合は、`service` まで作成してくれます。
```tpl
oc new-app --name hello-openshift --image quay.io/openshift/origin-hello-openshift
```

### 1.3. route の作成 (アプリの外部公開)

1. `route` (Kubernetes の `ingress` に相当) を作成します。`Service` を `expose` する事で作成されます。
```tpl
oc expose service hello-openshift
```

2. 作成された `route` を確認します。

```tpl
oc get route
```
{{< expand "出力例">}}
```tpl
$ oc get route
NAME              HOST/PORT                                                                           PATH   SERVICES          PORT       TERMINATION   WILDCARD
hello-openshift   hello-openshift-hello-openshift2.apps.rosa.myhcpcluster.erth.p3.openshiftapps.com          hello-openshift   8080-tcp                 None
$ 
```
{{< /expand >}}

3. `route` で設定されたホスト名の部分を変数 HOST に取り出します。

```tpl
HOST=$(oc get route/hello-openshift -o jsonpath={.spec.host})
```

### 1.4. curl でのアクセス確認

1. `curl` コマンドでアクセスして確認します。
```tpl
$ curl $HOST
Hello OpenShift!
$
```

ちょっとだけですが、OpenShift の独自コマンドを使うことで手数を減らす事ができるようになっています。

## 2. 作成したアプリのレプリカ数を増やしてみる

1. 可用性を保つために `Pod` の replica 数を3つに増やしてみます。
```tpl
oc scale deployment hello-openshift --replicas=3
```

2. Pod が ３つになっている事を以下のコマンドで確認します。
```tpl
oc get pods
```

3. ３つに増やしても引き続きアプリケーションにアクセスできることを curl コマンドで確認します。(アクセス先 URL は `oc get route` で表示される URL です)
```tpl
curl $HOST
```

## 3. デプロイしたアプリケーションを削除する

1. 作成した `deployment`、`service`、`route` は、`project` を削除することで全て消す事ができます。
以下のコマンドで実験で使用したアプリケーションを削除します。
```tpl
oc delete project hello-openshift2


