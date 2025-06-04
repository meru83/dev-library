# AWS + EC2 + Amazon LinuxでGit + GitHubを使う

## Gitのインストール

```
sudo dnf install -y git
```

## SSHキーを作成

出てくる質問はすべてEnterで構いません。  

```
ssh-keygen -t rsa -b 4096
```

## SSHキーの内容をコピー

次に作成されたキーをコピーします。  
（ssh-rsa~から全て含みます）

```
cat ~/.ssh/id_rsa.pub
```

こんな感じの内容を`ssh-rsa`から全てコピー

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCeDbz
省略
ec2-user@ip-10-0-1-10.ap-northeast-3.compute.internal
```

## GitHubに登録しにいく

- GitHubにログインしてSettingsを開く
- `SSH and GPG keys`に行きます。
    - https://github.com/settings/keys
- そこに`New SSH key`ボタンがあるのでクリックします。
- `title`は各自自由に入力
- `Key type`は`Authentication Key`のまま
- `Key`のところに先ほどコピーしたキーを貼り付けます。
- `Add SSH key`をクリックする

## SSHから設定

SSH接続しているターミナルに戻り下記を実行する

```
ssh -T git@github.com
```

下記文章が表示されるので、

- `Are you sure you want to continue connecting (yes/no/[fingerprint])?`は`yes`

- `Enter passphrase for key '/home/ec2-user/.ssh/id_rsa':`にはSSHキー作成じに設定したパスワードを入力する

```
The authenticity of host 'github.com (20.27.177.113)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.

This key is not known by any other names

Are you sure you want to continue connecting (yes/no/[fingerprint])? yes 

Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.

Enter passphrase for key '/home/ec2-user/.ssh/id_rsa':

Hi room202! You've successfully authenticated, but GitHub does not provide shell access.
```

## Git Cloneを実行する

```bash
sudo git clone git@github.com:meru83/971cmReact.git
cd 971cmReact
```

## nodejsで実行する

```bash
# 必要なライブラリをインストール
npm install

# 3000番ポートでnodejsを起動する
PORT=3000 npm start
```