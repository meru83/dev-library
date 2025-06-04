```bash
# Nodejs
cd 971cmReact
PORT=3000 npm start

# FastAPI
# 通常
uvicorn project.main:app --host 0.0.0.0 --port 8000 --reload

※Let's Encryptで生成した鍵ファイルをカレントディレクトリの`.ssh`ディレクトリにコピーしておく

# SSLで実行
# root権限で実行するのでpipで必要なライブラリをインストール
sudo pip install fastapi uvicorn pip mysql-connector-python

# uvicorn実行
uvicorn project.main:app --host 0.0.0.0 --port 8000 --ssl-keyfile=/home/ec2-user/.ssh/privkey2.pem --ssl-certfile=/home/ec2-user/.ssh/fullchain2.pem --reload

# uvicorn実行のシェルスクリプト

```bash
sh start_uvicorn.sh
```
