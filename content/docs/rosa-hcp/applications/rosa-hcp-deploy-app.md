---
title: 1. Hello world コンテナのデプロイ
weight: 1
---

## 1. 簡単なサンプルコンテナをデプロイしてみる

`hello-openshift` という Web Server のコンテナをデプロイしてみます。アクセスすると、"Hello OpenShift" とメッセージを返すシンプルなコンテナです。

1. `hello-openshift` という `project` (OpenShift で使われる `namespace` の拡張概念) を作成します

```tpl
oc new-project hello-openshift
```

2. `hello-openshift` イメージを使った `deployment` を作成します。名前はコンテナ名と同じ　`hello-openshift` にします。


```tpl
oc create deployment hello-openshift --image=quay.io/openshift/origin-hello-openshift
```

`deployment` が作成されたか確認します。

```tpl
oc get deployment
```



3. `service` を作成します。 `hello-openshift` コンテナが使用している `8080` を公開します。`service` は `deployment` を `expose` する事で作成可能です。
(コンテナイメージがどのポートを使用しているかをコマンド等で突き止める事もできますが、基本的に事前に知っている必要があります。)

```tpl
oc expose deployment hello-openshift --port=8080
```

補則：`oc create service clusterip hello-openshift --tcp=8080:8080` でも上の操作と同じ事が可能です。

4. OpenShift での `ingress` と同等の概念である `route` を作成します。HTTP アプリケーションの場合、`route` は、`service` を `expose` する事で作成可能です。

```tpl
oc expose service hello-openshift
```

5. `route` が作成されたか確認します。

```tpl
oc get route
```

以下のような出力になるはずです。環境によって URL は違います。`route` の名前は、公開した `service` と同じ名前になっているはずです。これは、HTTP(Port 80) から `Service` の `8080` にマッピングされている事を意味しています。

```tpl
$ oc get route
NAME              HOST/PORT                                                                  PATH   SERVICES          PORT        TERMINATION   WILDCARD
hello-openshift   hello-openshift-test2.apps.rosa.my-hpc-cluster.rc4b.p3.openshiftapps.com          hello-openshift   8080                      None
$
```

6. 作成された `route` にアクセスしてみます。

```tpl
$ curl hello-openshift-test2.apps.rosa.my-hpc-cluster.rc4b.p3.openshiftapps.com
Hello OpenShift!
$
```

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
curl hello-openshift-test2.apps.rosa.my-hpc-cluster.rc4b.p3.openshiftapps.com
```

## 3. デプロイしたアプリケーションを削除する

1. 作成した `deployment`、`service`、`route` は、`project` を削除することで全て消す事ができます。
以下のコマンドで実験で使用したアプリケーションを削除します。

```tpl
oc delete project hello-openshift
```

## 4. oc new-app を使用して簡単なサンプルコンテナをデプロイしてみる

前回は kubernetes の `kubectl` コマンドと共通の `oc` コマンドを使用してコンテナをデプロイしましたが、今度は同じ事を OpenShift の独自コマンドを使用して行ってみます。

1. 新しい `project` を作成します。

```tpl
oc new-project hello-openshift2
```

2. 以下のコマンドで `hello-openshift` コンテナを使った `deployment` と `service` を一気に作成します。この `oc new-app` は OpenShift の独自コマンドです。
container のイメージが公開している port の情報を持っている場合は、`service` まで作成してくれます。

```tpl
oc new-app hello-openshift --image quay.io/openshift/origin-hello-openshift
```

3. `route` を作成します。

```tpl
oc expose service hello-oppenshift
```

作成された `route` を確認します。

```tpl
oc get route
```

4. `curl` コマンドでアクセスして確認します。

```tpl
$ curl hello-openshift-hello-openshift.apps.rosa.my-hpc-cluster.rc4b.p3.openshiftapps.com
Hello OpenShift!
$
```

ちょっとだけですが、OpenShift の独自コマンドを使うことで手数を減らす事ができるようになっています。




