---
title: 2. Dev Spaces Operator
weight: 1
---
## Dev Spaces Operator のインストール

Dev Spaces Operator は、OpenShift 上にユーザー毎の開発環境を作成し、Webブラウザベースの VS Code ライクな開発環境を提供します。

1. `Operator Hub` に移動して、`Red Hat OpenShift Dev Spaces` をクリックします。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/f4c686f9-fe6d-4c73-bd88-1e40048a917b)

2. デフォルトのまま `Install` をクリックします。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/1cc670c8-ff33-4e6d-94c6-149aef591220)

3. デフォルトのまま `Install` をクリックします。
![image](https://github.com/yuhkih/rosa-hcp-workshop/assets/8530492/cdb4fbcd-ac54-42ef-bd9b-568e5b089863)

4. インストールが完了するまで暫く待ちます。(ここから新規キャプチャー)
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/7e3e77ba-f104-4b22-8e6a-14a9f796e0bb)

5. インストールが完了したら、「View Opearator」をクリックして Operator のページに移動します。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/d8418a8b-02e9-442a-b809-0c109c44a712)

6. `Red Hat OpenShift Dev Spaces instance Specification` というタブに移動して、 `Create CheClsuter` をクリックします。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/b7aaa774-8f00-4638-86a2-68120b8cd6e3)

7. 設定はデフォルトのままで`Create`をクリックします。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/72fbf066-e686-4f98-83de-060e571b7f8a)

これで `devspaces` という名前の `CheCluster` というオブジェクトが作成されました。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/07d847cf-dcb3-4fd6-8b0b-0a7f72407142)

`Workload` => `Pod` に移動すると Pod が作成されはじめているのがわかります。収まるまで暫く待ちます。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/79786118-0518-411d-b6a8-852f1c45af59)

8. Pod の作成が収まったら、`Networking` => `Route` に移動します。外部からアクセスするための `URL` が発行されているので、URL にアクセスします。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/68a15345-66b1-4baa-9989-e50c64fc4abd)

10. `Login with OpenShift` をクリックしてログインします。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/a73b57f4-0736-40f5-aee1-a3703b4d683e)

11. 連携している Identity Provider によって表示が違いますが、好きなユーザーでログインして大丈夫です。
    ここではデフォルトで必ず存在する `cluster-admin` を使ってログインします。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/8b87dbfc-17b3-471f-a03c-b6aa182804b9)

![image](https://github.com/yuhkih/mcs-docs/assets/8530492/b5488bec-1f07-4aa1-a32b-056d2cfc7deb)

12. 初回はアクセス許可を求められるので、許可します。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/e2a0249d-8b24-4ddf-b0fd-a9be76d50930)

13. ログインすると、以下のような画面が見えるはずです。
    ここでは `Empty Workspace` をクリックします。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/a66cb62d-f30b-437c-ba77-d59661c6fe83)

ワークスペースのデプロイが開始されるので、暫く待ちます。
![image](https://github.com/yuhkih/mcs-docs/assets/8530492/0a621c6f-1399-41ba-8585-eeee6946f304)

14. 三点リーダーのメニューから `Terminal` => `New Terminal` を選択します。

15. メニューから Terminal が作成できる事を確認します。
