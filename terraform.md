1. terraformインストール
``` bash
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

2. terraformバージョン確認
``` bash
terraform -version
```

3. terraform用ディレクトリ作成
``` bash
mkdir proxmox-terraform
```

4. main.tfとprovider.tfを作成
> [!NOTE]
> - main.tf : 仮想マシンの設定ファイル
> - provider.tf : プロバイダの設定ファイル

provider.tf
``` terraform
terraform {
  required_providers {
    proxmox = {
      source  = "bpg/proxmox"
      version = "0.98.0" # バージョンは適宜最新を確認してください
    }
  }
}

provider "proxmox" {
  endpoint = "https://proxmox:8006"
  insecure = true
  username = "root@pam"
  password = "xxx"
#  insecure = true

#  ssh {
#    agent = true
#    username = "root"  # required when using api_token
#  }
}
```
main.tf
``` terraform
resource "proxmox_virtual_environment_vm" "ubuntu_vm" {
  name        = "my-ubuntu-vm"
  node_name   = "proxmox-node" # Proxmoxのノード名を指定してください
  vm_id       = 200   # 一意のIDを指定

  # テンプレートからクローンする場合
  clone {
    vm_id = 9000      # Proxmox上にある元となるテンプレートのID
    full = true
  }

  # リソース設定
  cpu {
    cores = 2
  }
  memory {
    dedicated = 2048
  }

#  disk {
#    datastore_id = "local-zfs"
#    interface    = "virtio0"
#    iothread     = true
#    discard      = "on"
#    size         = 30
#  }

  # Cloud-init設定（初期セットアップ）
  initialization {
     datastore_id = "local-zfs"
     user_account {
       username = "ubuntu"
       password = "$6$cry1TPT2YV/2pfkX$kQPdTgWIYnjDbOQC..."
#      #keys     = [trimspace(file("~/.ssh/id_rsa.pub"))] # 公開鍵へのパス
    }
    ip_config {
      ipv4 {
        address = "dhcp"
      }
    }
  }
}
```


> [!NOTE]
> Cloud-init設定のpasswordにはopenssl passwd -6コマンドによりハッシュ化したパスワードを指定する

5.terraform初期化コマンド
``` terraform
terraform init
```

6. terraform実行確認
``` terraform
terraform plan
```
7. terraform実行
``` terraform
terraform apply
```

# 参考サイト
https://registry.terraform.io/providers/bpg/proxmox/latest/docs

https://github.com/AobaIwaki123/Proxmox-Terraform
