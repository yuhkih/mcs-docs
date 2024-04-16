---
title: 4. 内部 OpenShift Registry を使用する
weight: 1
---
この手順は{{< button relref="/docs/rosa-hcp/applications/rosa-hcp-deploy-secure-app/" >}}標準の Ngix コンテナをセキュアにする{{< /button >}} の続きです。

ROSA では、コンテナアプリケーションの開発に必要な Docker Registry が既にセットアップされて使えるようになっています。

主に、この ROSA Cluster の内部のプロジェクトから使用される事を目的とした、シンプルな Docker Image Registry なので、`内部 OpenShift Registry` のように呼ばれています。

## 1. 内部 OpenShift Registry の公開


コンテナをビルドした端末から、直接、内部 OpenShift Registry にアクセスできるようにするために、Route を使って 内部 OpenShift Registry をクラスター外のネットワークに公開します。

```tpl
oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
```

## 2. 内部 OpenShift Registry へのログイン

内部 OpenShift Registry の ドメイン名を取得します。

```tpl
export IMAGE_SERVER=`oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}'`
```

変数に値がセットされているか確認します。

```tpl
echo $IMAGE_SERVER
```

内部 OpenShift Registry に、[Bearerトークン](https://ja.wikipedia.org/wiki/Bearer%E3%83%88%E3%83%BC%E3%82%AF%E3%83%B3) を使ってログインします。

```tpl
podman login -u `oc whoami` -p `oc whoami --show-token` ${IMAGE_SERVER}
```

## 3. 内部 OpenShift Registry への Image の Push


新しい project を作成します。

```tpl
export PROJECT_NAME=secure-nginx
```

```tpl
oc new-project $PROJECT_NAME
```


現在、作業端末上にある image を確認します。

```shell 
$ podman images
REPOSITORY                   TAG         IMAGE ID      CREATED         SIZE
localhost/new-nginx  latest      d623ca329bc4  19 minutes ago  303 MB
 $ 
```

作成したローカルイメージにタグを付けます。

内部 OpenShift Registry は、プロジェクト名を持った名前空間で区切られます。
tag 名に $PROJECT_NAME を入れる事で、特に追加の設定をしなくても、イメージが $PROJECT_NAME 内からアクセスできるようになります。

```shell 
podman tag localhost/new-nginx:latest $IMAGE_SERVER/$PROJECT_NAME/mynginx:latest
```

イメージを push します

```tpl
podman push  $IMAGE_SERVER/$PROJECT_NAME/mynginx:latest
```

これで内部 OpenShift Registryにビルドした image が push されました。

## 4. Push した Image の OpenShift 上へのデプロイ

内部 Image Registry に Push したイメージを使用してアプリケーションを Deploy します。

1. 現在いるプロジェクトを確認します。タグ付けに使った $PROJECT_NAME である事を確認します。

```tpl
oc project                 
```

2. コンテナをデプロイします。

`oc new-app` コマンドで一気に Depoloyment と  Service を作成します。

```tpl
oc new-app --name new-nginx --image $IMAGE_SERVER/$PROJECT_NAME/mynginx:latest
```

{{< hint info >}}
**豆知識:**   
`oc new-app` コマンドが、image 名を引数に指定するだけで、Service まで作成できるのは、OpenShift が、コンテナ image 内でEXPOSE されているポートを自動的に認識しているためです。コンテナ image 内で EXPOSE が行われてない場合は、Service は自動では作成されません。
{{< /hint >}}

`new-nginx` という名前で Deployment と Service が作成されている事を確認します。

```tpl
oc get deployment,service
```

{{< expand "実行例">}}

```tpl
$ oc get deployment,service
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/new-nginx   1/1     1            1           5m8s

NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
service/new-nginx      ClusterIP   172.30.252.226   <none>        8080/TCP            5m8s
service/nginx-sample   ClusterIP   172.30.121.16    <none>        8080/TCP,8443/TCP   3h46m
nginx $
```
{{< /expand >}}


今度は Pod が起動している事を確認します。

```tpl
oc get pods
```

{{< expand "実行例">}}

```tpl
 $ oc get pods
NAME                            READY   STATUS    RESTARTS   AGE
new-nginx-599f687494-vk78j      1/1     Running   0          7s
$ 
```
{{< /expand >}}


サービスを公開します。これをする事で `Route`オブジェクトが作成されます。

```tpl
oc expose svc new-nginx
```

route を確認します。

```tpl
oc get route
```

{{< expand "実行例">}}

```tpl
$ oc get route
NAME           HOST/PORT                                                                   PATH   SERVICES       PORT       TERMINATION   WILDCARD
new-nginx      new-nginx-new-nginx.apps.rosa.my-hpc-cluster.pd80.p3.openshiftapps.com             new-nginx      8080-tcp                 None
$
```
{{< /expand >}}

route の HOST 名を変数 `HOST` に取り出します。

```tpl
HOST=$(oc get route/hello-openshift -o jsonpath={.spec.host})
```

curl でアクセスできる事を確認します。

```tpl
curl $HOST 
```

{{< expand "実行例">}}
```tpl
$ curl new-nginx-new-nginx.apps.rosa.my-hpc-cluster.pd80.p3.openshiftapps.com
Hello OpenShift World!
$
```
{{< /expand >}}
