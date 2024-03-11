---
title: 2. 内部 Image Registry を使用する
weight: 1
---
## 1. 内部 Image Registry の公開


Image Registry をインターネットに公開します。

```tpl
oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
```

## 2. ローカル Image Registry へのログイン

Image Registry の ドメイン名を取得します。

```tpl
export IMAGE_SERVER=`oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}'`
```

Image Registry にログインします。

```tpl
podman login -u `oc whoami` -p `oc whoami --show-token` ${IMAGE_SERVER}
```

## 3. ローカル Image Registry への Image の Push

現在、ある image を確認します。

```tpl
nginx $ podman images
REPOSITORY                   TAG         IMAGE ID      CREATED         SIZE
localhost/new-nginx  latest      d623ca329bc4  19 minutes ago  303 MB
nginx $ 
```

作成したローカルイメージにタグを付けます。

```tpl
podman tag localhost/new-nginx:latest $IMAGE_SERVER/new-nginx/mynginx:latest
```

イメージを push します

```tpl
podman push  $IMAGE_SERVER/new-nginx/mynginx:latest
```

## 4. Push した Image を使用した Deployment の作成

ローカル Image Registry に Push したイメージを使用して Deploy します。`new-app` で一気に Service まで作成します。

```tpl
oc new-app --name new-nginx --image $IMAGE_SERVER/new-nginx/mynginx:latest
```

`new-nginx` という名前で Deployment と Service が作成されている事を確認します。


```tpl
 $ oc get deployment,service
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


サービスを公開します。

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
