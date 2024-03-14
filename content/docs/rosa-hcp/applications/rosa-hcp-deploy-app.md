---
title: 1. Hello world コンテナのデプロイ
weight: 1
---

## 1. 簡単なサンプルコンテナをデプロイしてみる

`hello-openshift` という Web Server のコンテナをデプロイしてみます。アクセスすると、"Hello OpenShift" とメッセージを返すシンプルなコンテナです。

### 1.1. namespace の作成
1. `hello-openshift` という `namespace` を作成します。
```tpl
kubectl create namespace hello-openshift
```

2. namespace を `hello-openshift` に変更します。

```
 kubectl config set-context $(kubectl config current-context) --namespace=hello-openshift
```

### 1.2. deployment の作成

1. `hello-openshift` イメージを使った `deployment` を作成します。名前はコンテナ名と同じ　`hello-openshift` にします。
```tpl
kubectl create deployment hello-openshift --image=quay.io/openshift/origin-hello-openshift
```

2. `deployment` が作成されたか確認します。

```tpl
kubectl get deployment
```

{{< expand "出力例">}}
```tpl
$ kubectl get deployment
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
hello-openshift   1/1     1            1           152m
$ 
```
{{< /expand >}}

### 1.3. serivce の作成

1. `service` を作成します。 `hello-openshift` コンテナが使用している `8080` を公開します。`service` は `deployment` を `expose` する事で作成可能です。
(コンテナイメージがどのポートを使用しているかをコマンド等で突き止める事もできますが、基本的に事前に知っている必要があります。)

```tpl
kubectl expose deployment hello-openshift --port=8080
```

2. `Service` が作成されているはずです。以下のコマンドで確認します。

```tpl
kubectl get svc
```

{{< expand "出力例">}}
```tpl
$ kubectl get svc
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
hello-openshift   ClusterIP   172.30.169.213   <none>        8080/TCP   155m
$ 
```
{{< /expand >}}

### 1.4. アプリケーションが使用するドメインの確認

1. Application が、クラスターの外に公開される時に使用されるドメイン名を確認します。
`rosa` では、クラスターのアプリケーションが使用できるドメイン名が提供されています。`rosa list ingress` コマンドで取得できます。

```tpl
rosa list ingress -c $CLUSTER_NAME
```

{{< expand "出力例">}}
```tpl
$ rosa list ingress -c $CLUSTER_NAME
ID    APPLICATION ROUTER                                        PRIVATE  DEFAULT  ROUTE SELECTORS  LB-TYPE  EXCLUDED NAMESPACE  WILDCARD POLICY  NAMESPACE OWNERSHIP  HOSTNAME  TLS SECRET REF
v4u4  https://apps.rosa.myhcpcluster.erth.p3.openshiftapps.com  no       yes                       nlb                                                                          
$ 
```
{{< /expand >}}

上記の出力の`APPLICATION_ROUTER`が、アプリケーションが使用するベースのドメインです。

2. このドメイン名を APP_DOMAIN 変数にセットします。コマンドが複雑ですが、dns_name の値を抜いているだけです。

```tpl
APP_DOMAIN=$(rosa list ingress -c myhcpcluster -o yaml | grep dns_name | awk -F': ' '{print $2}' | sed "s/\"//g")
```

3. 変数に値がセットされているのか確かめます。
```tpl
echo $APP_DOMAIN
```

### 1.5. ingress の作成 (アプリの外部公開)

1. `ingress` を作成して Service をクラスターの外に公開します。
```tpl
kubectl create ingress hello-openshift --rule="hello-openshift.$APP_DOMAIN/*=hello-openshift:8080"
```

2. `ingress` が作成されたか確認します。
```tpl
kubectl get ingress
```

{{< expand "出力例">}}
```tpl
$ kubectl get ingress
NAME              CLASS    HOSTS                                                              ADDRESS                                                           PORTS   AGE
hello-openshift   <none>   hello-openshift.apps.rosa.myhcpcluster.erth.p3.openshiftapps.com   router-default.apps.rosa.myhcpcluster.erth.p3.openshiftapps.com   80      11m
$ 
```
{{< /expand >}}

3. ingress が使用している host 名を変数にセットします。
```tpl
HOST=$(kubectl get ingress/hello-openshift -o jsonpath='{.spec.rules[0].host}')
```

変数のセットを確認します。

```tpl
echo $HOST
```

### 1.6. curl でのアクセス確認

1. 作成された `ingress` にアクセスしてみます。
```tpl
$ curl $HOST
Hello OpenShift!
$
```

## 2. 作成したアプリのレプリカ数を増やしてみる

1. 可用性を保つために `Pod` の replica 数を3つに増やしてみます。
```tpl
kubectl scale deployment hello-openshift --replicas=3
```

2. Pod が ３つになっている事を以下のコマンドで確認します。
```tpl
kubectl get pods
```

{{< expand "出力例">}}
```tpl
$ oc get pods
NAME                               READY   STATUS              RESTARTS   AGE
hello-openshift-6f5d96958d-7j6wv   1/1     Running             0          3s
hello-openshift-6f5d96958d-fwv7j   0/1     ContainerCreating   0          3s
hello-openshift-6f5d96958d-r9w5m   1/1     Running             0          148m
$ 
```
{{< /expand >}}


3. ３つに増やしても引き続きアプリケーションにアクセスできることを curl コマンドで確認します。(アクセス先 URL は `kubectl get ingress` で表示される URL です)
```tpl
curl $HOST
```

## 3. デプロイしたアプリケーションを削除する

1. 作成した `deployment`、`service`、`ingress` は、`namespace` を削除することで全て消す事ができます。
以下のコマンドで実験で使用したアプリケーションを削除します。
```tpl
kubectl delete namespace hello-openshift
```





