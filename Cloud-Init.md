---
tags:
  - Proxmox
  - Ubuntu
  - Cloud-init
  - Terraform
target_os: Ubuntu(Noble 24.04)
---
# 1.テンプレートVMの作成
## 1.1 イメージの準備とVM作成
1. Ubuntuクラウドイメージダウンロード
``` bash
wget https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.img
```
2. VM作成
``` bash
qm create 9000 --memory 2048 --net0 virtio,bridge=vmbr0 --scsihw virtio-scsi-pci
```
3. UbuntuクラウドイメージをVM用としてインポート
``` bash
qm importdisk 9000 noble-server-cloudimg-amd64.img local-zfs
```
## 1.2 ハードウェア構成の設定
1. UbuntuクラウドイメージをVMに接続
``` bash
qm set 9000 --scsi0 local-zfs:vm-9000-disk-0
```
1. Cloud-init要仮想ドライブをVMに作成
``` bash
qm set 9000 --ide2 local-zfs:cloudinit
```
1. VMの起動順序をscsi0を優先に設定
``` bash
qm set 9000 --boot c --bootdisk scsi0
```
> [!WARNING] 起動順序の設定について
> 
>    - **`--boot c`**: 「ディスクドライブ（HDD/SSD）」から優先的に起動せよ、という指示です。
> 	   - `a`: フロッピー
> 	   - `c`: 第一ディスク（ハードディスク）
> 	   - `d`: CD-ROM
> 	   - `n`: ネットワーク（PXE）
>    - **`--bootdisk scsi0`**: 具体的にどのディスクを「第一ディスク」として扱うかを指定しています。

1. VMのディスプレイにシリアルポートを接続
``` bash
qm set 9000 --serial0 socket --vga serial0
```
> [!TIP] シリアルポート接続の設定について
> 
>- **`--serial0 socket`**: 仮想マシンに「シリアルポート（0番）」を追加し、その通信を「Unixソケット」という形式で行うように設定します。
>- **`--vga serial0`**: Proxmoxのコンソール（画面）に表示する内容を、通常のグラフィックカード出力ではなく、今作った **`serial0`** の内容にするよう指示します。

## 1.3 テンプレート化の実行
1. VMテンプレート化
``` bash
qm template 9000
```
# 2.カスタム設定(Cloud-initスニペット)
## 2.1 SSH鍵と設定ファイルの準備
1. Cloud-init設定ファイルを作成
``` bash
touch /var/lib/vz/snippets/user-config.yaml
touch /var/lib/vz/snippets/network-config.yaml
```
2. VMにSSH接続する端末でSSH鍵を作成、公開鍵の値を確認
``` bash
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub
```

>[!NOTE]- SSH公開鍵の実行結果
>AAAAC3NzaC1lZDI1NTE5AAAAIKD/2OKotkcWzQt4+QAK9EXU/3JDUUTQfCrUcS5u9zym akira@ubuntu

## 2.2 構成ファイルの記述(User/Network)
1. /var/lib/vz/snippets/user-config.yamlに設定追加
``` yaml
#cloud-config

# ユーザ設定
users:
  - name: nanten         # 作成するユーザー名
    groups: sudo             # sudoグループに追加
    shell: /bin/bash         # デフォルトシェルをbashに設定
    sudo: ALL=(ALL) NOPASSWD:ALL  # パスワードなしでsudoを実行可能にする
    ssh_authorized_keys:
      - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKD/2OKotkcWzQt4+QAK9EXU/3JDUUTQfCrUcS5u9zym nanten@ubuntu
    lock_passwd: false  # パスワードロックを解除

# パスワード設定
chpasswd:
  list: |
     nanten:xxx
  expire: false

# 基本設定
hostname: my-ubuntu-server
timezone: Asia/Tokyo

# 3. 起動時のパッケージ操作
package_update: true
package_upgrade: true
packages:
  - qemu-guest-agent
  - curl
  - vim
  - git

# 4. Proxmox向けの特別な設定
runcmd:
  - systemctl start qemu-guest-agent

```
2. /var/lib/vz/snippets/network-config.yamlに設定追加
``` yaml
network:
  version: 2
  ethernets:
    nics:
      match:
        name: "en*"  # enで始まるすべてのNICを対象にする（ens, enpなど）
      dhcp4: true
```
# 3. VMのデプロイと動作確認
## 3.1 クローンの作成
1. テンプレートからVMクローン
``` bash
qm clone 9000 123 --name ubuntu2
```
## 3.2 設定の適用と起動
1. VMにCloud-init設定ファイルをセット
``` bash
qm set 123 --cicustom "user=local:snippets/user-config.yaml,network=local:snippets/network-config.yaml"
```
2. VMの設定確認
``` bash
qm config 123
```
16. VM起動
``` bash
qm start 123
```

---
#  参考URL
- [Cloud-init documentation](https://cloudinit.readthedocs.io/en/latest/index.html)
- [Proxmox Cloud-init support](https://pve.proxmox.com/wiki/Cloud-Init_Support)
- [Ubuntu 24.04 LTSクラウドイメージ](https://cloud-images.ubuntu.com/noble/current/)
- [NotebookLM Cloud-init](https://notebooklm.google.com/notebook/72d94c17-9398-401f-8bd3-1080efca7b2b)