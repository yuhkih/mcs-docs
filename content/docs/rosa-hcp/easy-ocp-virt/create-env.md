---
title: 4. 環境の作成
weight: 1
---

この手順では以下のような環境が作成されます。

![image](https://github.com/user-attachments/assets/899c41d5-5f5f-4614-bf2a-45545b4db53a)


## 1.各種スクリプトのダウンロード

この環境は、Linux での動作を想定しています。(Windows の WSL2 Ubuntu 上で作成しました)

このテスト環境の作成では、複数のスクリプトを実行しますが、環境変数を引きついでいるので、全て同じシェルウィンドウで実行してください。

以下の git repository をダウンロードします。スクリプトをダウンロードするディレクトリに移動して、以下のように `BASE_DIR` 変数をセットしてください。後の手順でのディレクトリの移動に使用します。

```tpl
export BASE_DIR=~
```

以下のコマンドを実行して、セットアップ用のスクリプトが入った、GitHub Repository をダウンロードします。

```tpl
git clone https://github.com/yuhkih/rosa-ocpv.git
```

ダウンロードが完了したら、以下のディレクトリに移動します。

```tpl
cd $BASE_DIR/rosa-ocpv/create-environment
```

## 2.ROSA HCP Cluster の 作成

以下のスクリプトで 仮想マシンの Worker Node を2本持った、ROSA HCP クラスターが作成されます。

20分くらいかかるはずです。

```tpl
./create-rosa-cluster.sh
```

スクリプトが完了したら、以下のコマンドを実行して、OpenShift にログインできる事を確認しましょう。
```tpl
./ocp-login.sh
```

OpenShift の GUI が見たい場合は、以下のスクリプトで Console の GUI の URL と UserID / Password が表示できます。

```tpl
./ocp-show-console-url.sh
```


## 3.OCP Virtualization Operator の Install

ROSA Cluster のインストールスクリプトが完成したら、以下のスクリプトを実行して `OpenShift Virtualization` の Operator と必要な  `Custom Resouce` を作成します。

```tpl
./install-ocpv-operator.sh
```

## 4.共有ストレージ (FSx for NetApp ONTAP) の 作成
上記のスクリプトが完了したら、RWX のストレージ用に `FSx for Net App ONTAP` を導入します。

以下のスクリプトを実行する事で、`FSx` と必要な `CSI Driver` がインストールされます。30分以上かかるはずです。


```tpl
./install-fsx-ontap.sh
```

## 5.Baremetal Node の 作成

上記のスクリプトが完了したら、`Baremetal` の `Worker Node` を2本追加します。

現状、`OCP Virtualization` の仮想マシンは、`Baremetal Node` 上にしか作成(Schedulging)されないため、`Baremetal Node` が必要になります。

`Baremetal` の `Worker Node` は、非常に高価であるので、この手順は最後にもってきています。

以下のスクリプトを実行する事で、新規の `machinepool` が作成されそこに、`Baremetal` の `Worker Node` が 2本作成されます。

30分以上かかるはずです。


```tpl
./create-baremetal-machinepool.sh
```

このスクリプトが完了すれば、テスト環境の作成は完了です。

{{< hint warning >}}
**コストに関する注意点**

この ROSA 環境は、机上計算ですが、Baremetal Node を追加する前の状態の仮想 Node (m5.xlarge: 4vCPU / 16GiB x 2) の最小構成で 4,300円/24 hours 程度のコストがかかります。

これが、Baremetal Node (m5zn.metal:  48vCPU / 192GiB x  2本) と、FSx for NetApp ONTAP を追加した後は、62,500円/24 hours 程度のコストに上昇します。
増加分の内訳は、EC2 Baremetal Node / ROSA Subscription / FSx for NetApp ONTAP = 38,000円 / 15,000円 / 9,500円 です。

On Demand 料金で割引が効かない状態での EC2 Baremetal Node は、高額になるのでご注意下さい。

※ 155円/ドル計算
{{< /hint >}}


## 6.Baremetal Node部分の削除と再追加

実験をしない時は、Cluster 自体は残しつつ、Baremetal Node だけを一時的に削除、再追加する事も可能です。

ただし、それぞれに 30分程度の時間を要する事に注意してください。

**Baremetal Node の削除**

コマンドのリターンはすぐに返ってきますが、削除はバックグラウンドで進行しています。

```tpl
cd $BASE_DIR/rosa-ocpv/delete-environment
./delete-baremetal-machinepool.sh
```

削除の進行状況は、以下のコマンドで確認できます。

```tpl
oc get nodes
```

**Baremetal Node の再追加**

以下のコマンドで、Baremetal Node 再作成する事ができます。

```tpl
cd $BASE_DIR/rosa-ocpv/create-environment
./create-baremetal-machinepool.sh
```
