# AWS + EC2 + Amazon Linuxでnodejsをインストールする手順

1. SSHでEC2インスタンスにログインする

2. nvm（Node Version Manager）のインストール

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

3. nvmをロードするために、以下のコマンドを実行します。

```
source ~/.bashrc
```

4. Node.js 20.17.0のインストール

```
nvm install 20.17.0
```

5. Node.jsが正しくインストールされたか確認します。

```
node --version
```

6. npmの確認

```
npm --version
```

7. nodejsで実行する

```bash
# 必要なライブラリをインストール
npm install

# 3000番ポートでnodejsを起動する
PORT=3000 npm start
```