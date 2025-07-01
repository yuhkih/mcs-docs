---
title: 5. OCP-V を試して見る
weight: 1
---

## 1. virtctl コマンドのダウンロード

以下のディレクトリに移動します。

```tpl
cd $BASE_DIR/rosa-ocpv/test-virtual-machine
```

以下のコマンドを実行すると、OpenShift Console の URL とログイン方法が表示されるので、その情報を使って `OpenShift Console` にログインします。

```tpl
./ocp-show-console-url.sh
```


`OpenShift Console` の右上のメニューから、`コマンドラインツール`をえらびます。

![clidownload1](https://github.com/user-attachments/assets/f8314638-e3d2-4aa1-b7b1-ce733b930528)

以下のリンクからコマンドをダウンロードします。(この例は Linux です）

![clidownload2](https://github.com/user-attachments/assets/78356184-462f-4ae5-97e4-f5f61cf91e07)

**TIPS**: Linux (c86_64) の場合は、以下のコマンドで GUI にアクセスしなくても CLI でダウンロードできます。
```tpl
$ BASE_DOMAIN=$(rosa list clusters -o json | jq '.[0].dns.base_domain' | sed 's/"//g')
$ CLUSTER_NAME=$(rosa list clusters -o json | jq '.[0].name' | sed 's/"//g')
$ curl -LO https://hyperconverged-cluster-cli-download-openshift-cnv.apps.rosa.$CLUSTER_NAME.$BASE_DOMAIN/amd64/linux/virtctl.tar.gz
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


以下のスクリプトで仮想マシン `my-first-fedora-vm` が `my-vms` という `project` に作成されます。

仮想マシン用の秘密鍵を `~/.ssh/id_vm_rsa` として作成します。何か聞かれたとりあえず、Enter キーで大丈夫です。

10分程度で仮想マシンが作成されます。

```tpl
./create-virtual-machine.sh
```

## 3.Virtual Machine 作成の確認

以下のコマンドで作成状況が確認できます。

```tpl
watch oc get virtualmachine my-first-fedora-vm
```

{{< expand "出力例" >}}
```tpl
Every 2.0s: oc get virtualmachine my-first-fedora-vm                        

NAME                 AGE     STATUS         READY
my-first-fedora-vm   3m27s   Provisioning   False
```
{{< /expand >}}

`Status` が `Running` になるまで待ちます。

参考情報ですが、仮想マシンは、`my-vms` と言う `project` 内に作られます。作業の中で一番時間がかかるのは、RWX の Volume 作成で、その PVC (Persistent Volume Claim) の進行状況は、以下のコマンドで確認できます。


```tpl
oc get pvc -n my-vms
```

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
oc get pods -o wide | egrep "my-first-fedora-vm | Running"
```

{{< expand "出力例" >}}
```tpl
$ oc get pods -o wide | egrep "my-first-fedora-vm | Running"
virt-launcher-my-first-fedora-vm-rpgjq   1/1     Running     0          110s   10.131.0.85   ip-10-10-1-130.ap-northeast-1.compute.internal   <none>           1/1
$ 
```
{{< /expand >}}

仮想マシンを、別の `Node` に `Live Migration` (`VMWare` で言う所の `vMotion`) してみます。

```tpl
virtctl migrate my-first-fedora-vm
```

少し待ってから、もう一度、仮想マシンがデプロイされている `Node` を確認してみます

```tpl
oc get pods -o wide | egrep "my-first-fedora-vm | Running"
```

{{< expand "出力例" >}}

`Node`名が変わっているはずです。
```tpl
$ oc get pods -o wide | egrep "my-first-fedora-vm | Running"
virt-launcher-my-first-fedora-vm-p7nbp   1/1     Running     0          15s     10.128.2.93   ip-10-10-8-108.ap-northeast-1.compute.internal   <none>           1/1
$ 
```
{{< /expand >}}

違う `Node` 上に仮想マシンが移動しているがわかると思います。この環境では、10秒以内には `Live Migration` が完了しているはずです。
## 6.OCP Console を使った操作

仮想マシンの操作は、CLI だけでなく、GUI からも行う事ができます。

以下のコマンドで `OpenShift Console` のログイン URL が表示されるので、GUI での操作もいろいろ試して見てください。

```tpl
./ocp-show-console-url.sh
```

以下の GUI 上から、仮想マシンの起動、停止、Live Migration、スナップショットの取得など各種操作を行う事ができます。

![image](https://github.com/user-attachments/assets/6333fcb4-a377-48ad-b43f-bb56c3ab2160)

## 7.仮想マシンの削除

以下のコマンドで仮想マシンが削除できます。

```tpl
oc delete vm my-first-fedora-vm
oc delete project my-vms
```

