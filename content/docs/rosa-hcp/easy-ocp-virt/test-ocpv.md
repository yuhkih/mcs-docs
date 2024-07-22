---
title: 5. OCP-V を試して見る
weight: 1
---

## 1. virtctl コマンドのダウンロード

仮想マシン操作のための CLI ツールをダウンロードします。これは `OpenShift Console` から行います。

以下のディレクトリに移動します。

```tpl
cd $BASE_DIR/rosa-ocpv/test-virtual-machine
```

以下のコマンドを実行すると、OpenShift Console の URL とログイン方法が表示されます。

```tpl
./ocp-show-console-url.sh
```


```tpl
curl -OL https://hyperconverged-cluster-cli-download-openshift-cnv.apps.rosa.yuhki-hcp.mkkx.p3.openshiftapps.com/amd64/linux/virtctl.tar.gz
```

ダウンロードしたら解凍します。

```tpl
tar xzf virtctl.tar.gz
```

`/usr/local/bin` に移動させておきます。

```tpl
sudo mv virtctl /usr/local/bin
```

インストールを確認します。

```tpl
virtctl version
```


## 2.仮想マシンの作成

Repository ディレクトリ配下の、`rosa-ocpv/test-virt` に移動します。

```tpl
cd $YOUR_HOME_DIR/rosa-copv/test-virtual-machine
```

以下のスクリプトで仮想マシン `my-first-fedora-vm` が `my-vms` という `project` に作成されます。

仮想マシン用の秘密鍵を `~/.ssh/id_vm_rsa` として作成します。何か聞かれたとりあえず、Enter キーで大丈夫です。

10分程度で仮想マシンが作成されます。

```tpl
./create-virtual-machine.sh
```

仮想マシンは、`my-vms` と言う `project` 内に作られます。作業の中で一番時間がかかるのは、RWX の Volume 作成で、その PVC (Persistent Volume Claim) の進行状況は、以下のコマンドで確認できます。


```tpl
oc get pvc -n my-vms
```

## 3.Virtual Machine 作成の確認

以下のコマンドで作成状況が確認できます。

```tpl
watch oc get virtualmachine my-first-fedora-vm
```

`Status` が `Running` になるまで待ちます。

## 4.仮想マシンへのログイン

以下のスクリプトで 仮想マシンにログインできます。ログインして遊んでみましょう。

```tpl
virtctl ssh fedora@my-first-fedora-vm -i ~/.ssh/id_vm_rsa
```

{{< expand "出力例" >}}
```tpl
$ virtctl ssh fedora@my-first-fedora-vm -i ~/.ssh/id_vm_rsa
The authenticity of host 'vmi/my-first-fedora-vm.my-vms (<no hostip for proxy command>)' can't be established.
ED25519 key fingerprint is SHA256:0XVwG2F+s9r9Dfv4XOFP/JU5M4ZoCxfrRceoOuxNO7c.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'vmi/my-first-fedora-vm.my-vms' (ED25519) to the list of known hosts.
[fedora@my-first-fedora-vm ~]$ ls -ltr
total 0
[fedora@my-first-fedora-vm ~]$ 
```
{{< /expand >}}


## 5.仮想マシンのライブマイグレーション


この環境では、2本の `Baremetal Node` がインストールされています。そのどちらかの `Node` に 仮想マシンがデプロイされています。

以下のコマンドで現在、仮想マシンがデプロイされている `Node` がわかります。


```tpl
 oc get pod -l "kubevirt.io/domain=my-first-fedora-vm" -o jsonpath="{.items[0].metadata.labels.kubevirt\.io/nodeName}"
```

{{< expand "出力例" >}}
```tpl
ip-10-10-3-59.ap-northeast-1.compute.internal$ 
```
{{< /expand >}}

仮想マシンを、別の `Node` に `Live Migration` (`VMWare` で言う所の `vMotion`) してみます。

```tpl
virtctl migrate my-first-fedora-vm
```

少し待ってから、もう一度、仮想マシンがデプロイされている `Node` を確認してみます

```tpl
 oc get pod -l "kubevirt.io/domain=my-first-fedora-vm" -o jsonpath="{.items[0].metadata.labels.kubevirt\.io/nodeName}"
```

{{< expand "出力例" >}}

`Node`名が変わっているはずです。
```tpl
ip-10-10-0-229.ap-northeast-1.compute.internal$ 
```
{{< /expand >}}

違う `Node` 上に仮想マシンが移動しているがわかると思います。この環境では、10秒以内には `Live Migration` が完了しているはずです。

## 6.仮想マシンの削除

以下のコマンドで仮想マシンが削除できます。

```tpl
oc delete vm my-first-fedora-vm
oc delete project my-vms
```

