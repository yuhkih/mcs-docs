---
title: 6.環境の削除
weight: 1
---

## 1. ディレクトリの移動

削除関連のスクリプトがあるディレクトリに移動します。

```tpl
cd $BASE_DIR/rosa-ocpv/delete-environment
```

## 1. 仮想マシンの削除

仮想マシンを削除していない場合は、削除します。


```tpl
oc delete vm my-first-fedora-vm
oc delete project my-vms
```

## 2. Baremetal Node の削除

Baremetal Node は高価なので、一番初めに処理しておきましょう。以下で削除されます。スクリプトは一瞬で終わり、裏で削除が進みますが、次のステップに進んで問題ありません。

```tpl
./delete-baremetal-machinepool.sh
```

## 3. FSx ONTAP の削除

FSx ONTAP Volume を削除し、その後、FSx 自体を削除します。以下のスクリプトが両方を行ってくれます。20分以上かかるはずです。

```tpl
./delete-fsx-ontap.sh 
```


## 4. ROSA Cluster の削除

以下のスクリプトでROSA HCP クラスターを削除します。

```tpl
./delete-rosa-cluster.sh
```

途中で以下のように確認を求められるので、`yes` と入力してください。

```tpl
...
Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes
...
```

目安として、30分程度で削除が完了するはずです。
