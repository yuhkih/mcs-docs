---
title: 1. oc コマンド 
weight: 1
---

# oc コマンド

oc コマンドは、kubectl コマンドを拡張するために作成された、OpenShift 独自の CLI です。

基本的に上位互換ですので、多くの場合、`kubetl get pods` のようなコマンドは `oc get pods` のように置き換える事ができます。

## 1. OpenShift console の URL の表示


```tpl
oc whoami --show-console
```

{{< expand "コマンド実行例" >}}

```tpl
$ oc whoami --show-console
https://console-openshift-console.apps.rosa.myhcpcluster.erth.p3.openshiftapps.com
$ 
```
{{< /expand >}}

