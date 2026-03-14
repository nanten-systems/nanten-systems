## Hi there 👋

# 記録
- 2026/02/23(月) Githubアカウント作成、nanten-systemsリポジトリ作成
- 2026/03/07(土) UbuntuにGitインストール、クローンやコミット動作確認
- 2026/03/08(日) UbuntuにTerraformインストール。Proxmoxで仮想マシンをTerraformで作成
- 2026/03/14(土) やりたいことメモ追加

# やりたいことメモ

## Terraform
- ProxmoxにVM(Hugo実行基盤)を作成する
## Ansible
- Dockerインストール
- docker-compose.ymlファイル配置
- 証明書の更新
  - 定期的にAnsible実行が必要
    - 管理用マシンでcronを実行
    - またはGitHubActionを利用
  - Ansibleを使用せずwebサーバのcronでcertbotを定期実行しても証明書更新は可能

## GitHub
- コード管理
- GitHubにプッシュすると自動でHugoが更新される
  - GitHub Actionを使う
    - GitHubにPushされたらRunner(Self-hosted)がSSH経由でProxmox内のDockerコンテナ(Hugo)を起動
      - 生成されたHTMLをWebサーバ用コンテナへ移動

## Docker
- Hugoを動かす
- Nginxを動かす

## Hugo
- マークダウンをHTMLに変換

## Nginx
- Hugoが作成したHTMLを表示

## Cloudflare Tunnel
- ポートを開放せずにインターネットからの接続を可能とする
- Proxmox内にVMとして存在

## Let's Encrypt
- 証明書を発行


<!--
**nanten-systems/nanten-systems** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
