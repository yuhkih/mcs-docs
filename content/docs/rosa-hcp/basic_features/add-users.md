## HTPasswd を使用したユーザーの追加

本番環境では基本的には、GitHub や LDAP 等の外部の Identity Provider を使用する事が推奨されますが、実験で使用したい。という場合にそこまでの構成を準備するのは大変な事が多いでしょう。

ROSA (HCP/Classic両方) では、2023/4Q の機能追加で httpasswd を使ったユーザーの追加ができるようになっています。

### cloud.redhat.com にログイン1

1. [console.redhat.com](https://console.redhat.com/openshift/) にログインします。

2. 作業の対象の Cluster を選択します。
![clusters](https://github.com/yuhkih/mcs-docs/assets/8530492/1c9d9a83-bedf-467d-84df-f132d682b319)

3. `Access Control` タブに移動して、`htpasswd` を選択します。
![select-htpasswd](https://github.com/yuhkih/mcs-docs/assets/8530492/1d8446b2-6aa6-4c84-88e7-0f56a31b765f)

4. ユーザーを追加します。ここでは `yuhki` というユーザーを追加しています。
![add-users-to-htpasswd](https://github.com/yuhkih/mcs-docs/assets/8530492/c3e3a7b2-c327-45d9-831e-ab6c49e0eff6)

5. ここでは、追加したユーザーを `cluster-admin` グループに追加してみます。
`Access Control` タブから、`Add User` を行います。
![cluster-role](https://github.com/yuhkih/mcs-docs/assets/8530492/5d88e8b2-e604-4936-8a5f-0a1dbb2c91cd)

6. 
