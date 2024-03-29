---
title: 3. htpasswd を使用したユーザーの追加
weight: 1
---

## 2. htpasswd を使用したユーザーの追加

本番環境では基本的には、GitHub や LDAP 等の外部の Identity Provider を使用する事が推奨されますが、実験で使用したい。という場合にそこまでの構成を準備するのは大変な事が多いでしょう。

ROSA (HCP/Classic両方) では、2023/4Q の機能追加で httpasswd を使ったユーザーの追加ができるようになっています。

1. [console.redhat.com](https://console.redhat.com/openshift/) にログインします。

2. 作業の対象の Cluster を選択します。
![clusters](https://github.com/yuhkih/mcs-docs/assets/8530492/1c9d9a83-bedf-467d-84df-f132d682b319)

3. `Access Control` タブに移動して、`htpasswd` を選択します。
![select-htpasswd](https://github.com/yuhkih/mcs-docs/assets/8530492/1d8446b2-6aa6-4c84-88e7-0f56a31b765f)

4. ユーザーを追加します。ここでは `yuhki` というユーザーを追加しています。
![add-users-to-htpasswd](https://github.com/yuhkih/mcs-docs/assets/8530492/efa71a4d-9971-4321-acbe-6f3fd2e54d4c)

5. ここでは、追加したユーザーを `cluster-admins` グループに追加してみます。
`Access Control` タブから、`Add User` を行います。
![cluster-role](https://github.com/yuhkih/mcs-docs/assets/8530492/5d88e8b2-e604-4936-8a5f-0a1dbb2c91cd)

6. 先ほど作成したユーザー名を入力し、`cluster admins` を選んで `OK` をクリックします。
![Add-cluster-user](https://github.com/yuhkih/mcs-docs/assets/8530492/33fc2eee-816f-42f6-a513-17378e4d7968)

7. 作成された `cluster-admins` グループのユーザーは以下のように表示されます。
![confirmation](https://github.com/yuhkih/mcs-docs/assets/8530492/a55c77e9-869e-41f7-b6b8-7e7b8f9e1c78)

8. CLI からも `cluster-admins` のユーザーに `yuhki` が追加されている事が確認できます。

```tpl
$ oc get groups -o wide
NAME               USERS
cluster-admins     cluster-admin, yuhki
dedicated-admins   
$ 
```
