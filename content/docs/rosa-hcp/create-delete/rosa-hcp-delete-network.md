---
title: 6. Cluster 用 Network の削除
weight: 1
---

この手順は、Red Hat で提供している terraform を使用して、ROSA HCP Cluster を作成している事が前提になります。

## 1.Terraform で作成した AWS のネットワークを削除する

git clone したディレクトリに移動して、以下のコマンドで削除します。

```tpl
terraform destroy
```
