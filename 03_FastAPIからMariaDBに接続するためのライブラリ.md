# EC2一台クラウド作成3:FastAPIからMariaDBに接続するためのライブラリ

## ライブラリのインストール
$pip install sqlalchemy aiomysql databases

## SQLAlchemyとFastAPIの連携

ディレクトリ構成

```
/home/ec2-user/の中に
project/
│
├── main.py      # FastAPIのエントリーポイント
└── models.py    # データベースモデルの定義
└── crud.py      # CRUD操作の関数
└── database.py  # データベース接続の設定
````

database.py: データベース接続の設定。

models.py: データベースのテーブルモデルの定義。

crud.py: CRUD（作成、読み取り、更新、削除）操作の実装。

main.py: FastAPIのエンドポイントを定義し、データベース接続を使用。