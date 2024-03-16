---
title: 4. 内部 Image Registry を使用する
weight: 1
---
この手順は{{< button relref="/docs/rosa-hcp/applications/rosa-hcp-deploy-secure-app/" >}}標準の Ngix コンテナをセキュアにする{{< /button >}} の続きです。

## 1. 内部 Image Registry の公開


内部 Image Registry を Route を使ってクラスター外のネットワークに公開します。

```tpl
oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
```

## 2. ローカル Image Registry へのログイン

内部 Image Registry の ドメイン名を取得します。

```tpl
export IMAGE_SERVER=`oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}'`
```

変数に値がセットされているか確認します。

```tpl
echo $IMAGE_SERVER
```

内部 Image Registry に、[Bearerトークン](https://ja.wikipedia.org/wiki/Bearer%E3%83%88%E3%83%BC%E3%82%AF%E3%83%B3) を使ってログインします。

```tpl
podman login -u `oc whoami` -p `oc whoami --show-token` ${IMAGE_SERVER}
```

## 3. ローカル Image Registry への Image の Push


新しい project を作成します。内部 Imagre Registry は、プロジェクト名を持った名前空間で区切られます。

```tpl
export PROJECT_NAME=secure-nginx
```

```
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

```shell 
podman tag localhost/new-nginx:latest $IMAGE_SERVER/$PROJECT_NAME/mynginx:latest
```

イメージを push します

```tpl
podman push  $IMAGE_SERVER/$PROJECT_NAME/mynginx:latest
```

これで内部レジストリにビルドした image が push されました。

## 4. Push した Image を使用した Deployment の作成

内部 Image Registry に Push したイメージを使用してアプリケーションを Deploy します。

1. 現在いるプロジェクトを確認します。タグ付けに使った $PROJECT_NAME である事を確認します。

```tpl
oc project                 
```

2. コンテナをデプロイします。

`oc new-app` コマンドで一気に Service まで作成します。

```tpl
oc new-app --name new-nginx --image $IMAGE_SERVER/$PROJECT_NAME/mynginx:latest
```
{{< hint info >}}
**豆知識:**   
`oc new-app` コマンドが、image 名を引数に指定するだけで、Service まで作成できるのは、OpenShift がコンテナ image で EXPOSE されているポートを認識しているためです。
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

curl でアクセスしてみます。

```tpl
curl <Route の Host名>
```

{{< expand "実行例">}}
```tpl
$ curl new-nginx-new-nginx.apps.rosa.my-hpc-cluster.pd80.p3.openshiftapps.com
Hello OpenShift World!
$
```
{{< /expand >}}
