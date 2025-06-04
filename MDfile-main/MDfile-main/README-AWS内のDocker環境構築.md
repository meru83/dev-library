# AWS内のDocker環境構築

## Docker をインストールする

```bash
# インスタンスでインストールされているパッケージとパッケージキャッシュを更新します。
sudo yum update -y

# 最新の Docker Community Edition パッケージをインストールします。
sudo yum -y install docker
```

## Docker デーモンを起動する

```bash
# Docker デーモンを起動します。
sudo systemctl start docker

# Docker をブート時に自動起動するには、次のように実行します。
sudo systemctl enable docker
```

## 動作確認をする

```bash
# 動作確認のため Hello world を実行します。
sudo docker run hello-world
```

## Docker-Composeコマンドを追加する

※ ダウンロードするファイルは下記リンク先からURLを調べる事

※ 末尾が`docker-compose-linux-x86_64`であるファイルを使用すること

https://github.com/docker/compose/releases

```bash
# Docker-Composeコマンドをダウンロード
curl -OL https://github.com/docker/compose/releases/download/v2.30.3/docker-compose-linux-x86_64

# ファイル名を変更する
mv docker-compose-linux-x86_64 docker-compose

# 権限を変更する
chmod 755 docker-compose

# パスが通っている場所に移動する
sudo mv docker-compose /usr/bin/

# コマンドの確認
docker-compose -v

# Docker Composeでのコンテナのビルドと起動
sudo docker-compose up --build

# Docker Composeでの起動
sudo docker-compose up

# Docker Composeの停止
sudo docker-compose stop(control+cでも可)
```
