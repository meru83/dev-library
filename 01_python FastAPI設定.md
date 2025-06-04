# EC2一台クラウド作成：python FastAPI設定

## Python3とpipのインストール

```bash
# Python3のインストール
$ sudo yum install python3 -y

# pip（Pythonのパッケージ管理ツール）
$ sudo yum install python3-pip -y
```

## FastAPIとUvicornのインストール

```bash
$ pip3 install fastapi uvicorn
```

## MySQL関連のモジュールをインストール

```bash
$ pip3 install sqlalchemy pymysql
```

## FastAPIアプリの作成

```bash
$ mkdir /home/ec2-user/project
$ cd  /home/ec2-user/project
```

## 簡単なエンドポイントの作成
main.pyというファイルを作成,簡単なAPIエンドポイントを定義

```bash
# ファイルが開く(書き込み状態)
$nano main.py
```

```python
# 内容
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "FastAPI is running on Amazon Linux"}
```

ctl + o 保存

ctl + x 閉じる

## FastAPIの起動

```bash
# uvicornの起動対象プロジェクトは、main.pyがいるディレクトリを含めたパス
# 今回は「project」ディレクトリの中に「main.py」がある事を前提に実行している
$uvicorn project.main:app --host 0.0.0.0 --port 8000 --reload
```