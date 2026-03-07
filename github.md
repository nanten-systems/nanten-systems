# Gitインストール

1. gitインストール
``` bash
sudo apt install git
```

2. gitにユーザ名、メールアドレス登録
``` bash
git config --global user.name "nanten-systems"
git config --global user.email "new_account@gmail.com"
```
3. SSH鍵作成
``` bash
ssh-keygen -t rsa -C "new_account@gmail.com"
Enter file in which to save the key (/home/$USER/.ssh/id_rsa): id_rsa_github_nanten-systems  
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
```
4. 公開鍵をクリップボードにコピーし、GitHubアカウントに追加
``` bash
cat ~/.ssh/id_rsa_github_nanten-systems.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCqvieTRR/x9PMv3YZOMRsqA2zXqWl1o6dB7zxYXXTJ404wyy4T01drXmcf5edveD1s2b2JTnfOohssMbPkEeSLAV7miJIUsqnNSHBIhSF+TeT4Nj0GrgW/3PyOZSjgALuAIPr+L0mEN9QWiwwneBwBBzcv+zhLiE1fghN51YY+eA03GYSioJ8nWa0A8GWvyt3nfWAHy8pMyDnWO3zrhFcUZCfdGfTdvl0B22UQC8YcuvAEk8Iiwk9cuZ3+ppMeWeKEqos0G+X/Z+FzrlN8dHBraPBSEvlPbvp4PzZzDcDwk9QE7MP74J8u9l1qHyE6LYz/wnfIjwTkDyCREaqTR/hjA+1m9tK/8edVQlHIxbBzTJOIrw4IMzabLcElnZYO5daQTHmFusD8OR6JQoisdxFDNGc/Ha8LM1KNZQVdzoyAPZrhWy++PYhHdgfUI4UB6U/85n5ES3u9MdMMOj7jqOvXZV250rImi3sCTaKAH7cVc83ZCxm7lJ71KWVQipPiEUk=
```
> [!NOTE] 
> 参考手順
> 
> [GitHub アカウントへの新しい SSH キーの追加](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
5. a
``` bash
ssh -T git@github.com
The authenticity of host 'github.com (20.27.177.113)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
Hi nanten-systems! You've successfully authenticated, but GitHub does not provide shell access.
```
> [!NOTE]
> 翻訳
> 
> 警告: 'github.com' (ED25519) を既知のホストリストに恒久的に追加しました。
こんにちは、nanten-systemsさん！認証は成功しましたが、GitHub はシェルを提供していません。

6. git clone
``` bash
git clone git@github.com:nanten-systems/nanten-systems.git
```

# Gitコマンド

更新対象のファイルを追加
``` bash
git add ファイル名
```

更新を確定
``` bash
git commit -m "作業内容"
```
> [!note]
> -mオプションを指定しないとエディタが起動して作業内容の記入を求められる

更新のアップロード
``` bash
git push
```

gitにアップロードしたファイルを削除する場合
``` bash
git rm ファイル名
git commit -m "ファイル削除等コメント"
git push
```

