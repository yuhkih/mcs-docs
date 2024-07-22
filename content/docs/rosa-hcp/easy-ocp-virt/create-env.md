---
title: 4. 環境の作成
weight: 1
---

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




