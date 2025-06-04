# FastAPI(Python)技術・知識メモ

## 目次

- [その他参考リンク](#参考リンク)

- [可変長引数](#可変長引数)
    - [可変長引数とは](#可変長引数とは)
    - [サンプルプログラム](#サンプルプログラム)

- [union](#union)
    - [unionとは](#union-とは)
    - [Union[int, str] = None の意味](#unionint-str--none-の意味)

- [ボディパラメータ](#ボディパラメータ)
    - [path-operation-に期待されるボディの中身](#path-operation-に期待されるボディの中身)
    - [サブモデルの定義](#サブモデル-の定義)

- [Depends](#depends)
    - [基本的な使用法](#基本的な使用法)
    - [依存関係のネスト](#依存関係のネスト)
    - [依存関係における状態管理](#依存関係における状態管理)

- [JWTについて](#jwtについて)
    - [jwt用の秘密鍵と公開鍵を作成](#jwt用の秘密鍵と公開鍵を作成)
    - [fastapiアプリケーションで秘密鍵を使用してjwtを生成する](#fastapiアプリケーションで秘密鍵を使用してjwtを生成する)
    - [JWT まとめ](#まとめ)

- [tokenについて](#token-について生成取得)
    - [token-生成について](#token-生成について)
        - [jwtjson-web-token-のセットアップ](#1-jwtjson-web-token-のセットアップ)
        - [トークン生成のための設定](#2-トークン生成のための設定)
        -[fastapiでのログイン処理とトークンの返却](#3-fastapiでのログイン処理とトークンの返却)
    - [token-を取得する方法](#token-を取得する方法)
        - [httpリクエストのヘッダーからトークンを取得する方法](#1-httpリクエストのヘッダーからトークンを取得する方法)
        - [クエリパラメータからトークンを取得する方法](#2-クエリパラメータからトークンを取得する方法)
        - [ヘッダーとクエリパラメータを組み合わせて使う手法](#3-ヘッダーとクエリパラメータを組み合わせて使う手法)



## 参考リンク

このファイルに解説がないことについてのわかりやすい参考リンクを載せてます。

- [SQLAlchemyでDBの設定とORMの操作](https://zenn.dev/shimakaze_soft/articles/6e5e47851459f5)

## 可変長引数

- 参考

> https://aiacademy.jp/media/?p=1496#:~:text=Python%E3%81%AE%E9%96%A2%E6%95%B0%E3%81%AE%E5%BC%95%E6%95%B0,%E5%BC%95%E6%95%B0%E3%81%AB%E3%82%BB%E3%83%83%E3%83%88%E3%81%97%E3%81%BE%E3%81%99%E3%80%82

### 可変長引数とは

可変長引数とは、関数の引数の個数を指定せずに引数を渡す方法です。Pythonの関数の引数には、**と*が記述されている関数のプログラムが可変長引数です。関数定義の中で、仮引数（関数定義側）に*を使うと、可変個(固定ではなく任意の個数という意味)の位置引数をタプルにまとめてその仮引数にセットします。

### サンプルプログラム

~~~python
def sample(*args):
    print(args)

sample() # ()
sample(1,2,3,'python') # (1,2,3,'python')
~~~

*が一つの形式の場合、これより後ろの仮引数は全て、「キーワードオンリー」の引数となります。つまり、キーワード引数としてのみしか使えず、位置引数に出来ません。
つまり、値のみ

~~~python
def sample(**args):
    print(args)

sample(name='Tom', age='25', gender='male') # {'name': 'Tom', 'age': '25', 'gender': 'male'}
~~~

アスタリスクを2つ（**）使うと、キーワード引数を1個の辞書にまとめることが出来ます。引数の名前は辞書のキー、引数の値は辞書の値になります。

---
---

## Union

### Union とは

`nion` は、複数の型（データの種類）を受け入れることを意味します。Pythonでは変数や引数にいろいろな型のデータを渡すことができますが、明示的に「この変数には何の型のデータを渡しても良い」ということを指定したい場合に `Union` を使います。

例：
~~~python
from typing import Union
def example_function(value: Union[int, str]):
    print(value)
~~~

この関数では、`value` に整数（int）でも文字列（str）でも渡すことができるように `Union[int, str]` と指定しています。

### Union[int, str] = None の意味

`Union[float, None] = None` では、この式全体がデフォルト値として `None` を持つ変数や引数の `型アノテーション` です。

`= None` は、この変数のデフォルト値が `None` であることを示します。つまり、この引数や変数が指定されなかった場合、自動的に `None` が設定されます。

例：

~~~python
from typing import Union

def example_function(value: Union[float, None] = None):
    if value is None:
        print("値が設定されていません")
    else:
        print(f"値は {value} です")

example_function()  # 引数がないので None がデフォルトで使われる
example_function(10.5)  # 10.5 が引数として渡される

~~~


---
---

## ボディパラメータ

### **path operation** に期待されるボディの中身

1. **path operation** 内に一つのボディパラメータ（Pydantiocモデルである一つのパラメータ）

    ~~~python
    class Item(BaseModel):
        name: str
        price: int
        tax: Union[float, None] = None

    @app.put("/items/{item_id}")
    async def update_item(
        *,
        item_id: int = Path(title="title"), 
        q: Union[str, None] = None,
        item: Union[Item, None] = None,
    ):
        results = {"item_id": item_id}
        if q:
            results.update({"q": q})
        if item:
            results.update({"item": item})
        return results
    ~~~

    > 上記の `*` は以降のパラメータをキーワード引数として呼ばれるべきものであるとPythonが解釈する。

    上述の例では、**path operations** は `item` の属性を持つ以下のようなJSONボディを期待します。

    ~~~python
    {
        "name": "Foo",
        "description": "The pretender",
        "price": 42.0,
        "tax": 3.2
    }
    ~~~

2. **path operation** 内に二つ以上のボディパラメータ

    itemとuserのように複数のボディパラメータを宣言することができ、その場合は以下のようなJSONボディが期待されます。

    ~~~python
    {
        "item": {
            "name": "Foo",
            "description": "The pretender",
            "price": 42.0,
            "tax": 3.2
        },
        "user": {
            "username": "dave",
            "full_name": "Dave Grohl"
        }
    }
    ~~~

    > 備考：<br>
    以前と同じようにitemが宣言していても、itemはキーitemを持つボディの内部にあることが期待されていることに注意してください。

3. 単一のボディパラメータを埋め込む

    PydanticモデルItemのボディパラメータitemを1つだけ持っているとしましょう。

    デフォルトでは、**FastAPI**はそのボディを直接期待します。

    しかし、追加のボディパラメータを宣言したときのように、キー item を持つ JSON とその中のモデルの内容を期待したい場合は、特別な Body パラメータ embed を使うことができます:

    ~~~python
    item: Item = Body(..., embed=True)

    # 上記のようにしてあげることのよってパラメータにPydantiocモデル(bodyパラメーター)が一つでも、以下のようなJSONを期待するようになる。

    {
        "item": {
            "name": "Foo",
            "description": "The pretender",
            "price": 42.0,
            "tax": 3.2
        }
    }
    ~~~

### **サブモデル** の定義

~~~python
from typing import Set, Union

from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# サブモデルを定義
class Image(BaseModel):
    # urlフィールドがある場合以下のようにすることも可能
    # ※pydanticからHttpUrlのimportが必要
    #url: HttpUrl
    url: str
    name: str


class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None
    tags: Set[str] = set()
    # サブモデルをimage属性の型として使用
    image: Union[Image, None] = None


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
~~~

これにより、Fast以下のようなボディを期待する。

~~~python
{
    "name": "Foo",
    "description": "The pretender",
    "price": 42.0,
    "tax": 3.2,
    "tags": ["rock", "metal", "bar"],
    "image": {
        "url": "http://example.com/baz.jpg",
        "name": "The Foo live"
    }
}
~~~


---
---

## 辞書型 JSON 違い

### 結論

JSONは「データフォーマット」であり、辞書型は「Pythonにおけるデータ型の一種」です。

JSONというフォーマットに従って記述されたデータは、そのままでは構造化された情報へのアクセスが容易に行えません。
なぜなら、ただの文字列だからです。

d["key名"]のような形でデータにアクセスできるようにするには、それぞれのプログラミング言語に応じて適切なデータ型に変換してあげる必要があります。
それがPythonでは辞書型であり、JavaScirptではオブジェクト型に該当する訳です。

### JSONは「データフォーマット」

JSONは「こういうルールで書きましょうね」というただのフォーマットです。

JSONでは、「{}（波括弧）で囲みましょう」であったり「文字列は必ず""（ダブルクオーテーション）で囲みましょう」といったルールが定められており、そのルールに基づいて以下のようにデータを表現します。

つまり、JSONはただの「文字列」です。

~~~python
# json_text は JSONデータ（JSON形式のルールに基づいたデータ）が代入された変数である。
json_text = '{"name": "Taro", "age": 12, "country": "Japan"}'
print(type(json_text))
# <class 'str'>
~~~

JSONはstr型の文字列であるから `json_text["name"]` のようなことはできない。

### 辞書型はデータ型

一方で辞書型はPythonで扱えるデータ型の一種です。

辞書型はさまざまな便利機能をもっており、d["key名"]とすることで値を取得できたり、d.keys()とすることでkeyの一覧を取得できたりします。

Pythonでは、JSON形式で記述されたデータ（文字列）をこの辞書型に変換することで、辞書型のさまざまな便利機能を使ってデータをより柔軟に取り扱うことが出来るようになります。

変換に際しては、jsonというモジュールを使います。

str型である json_text を json.loads() という関数で変換することでdict型（辞書型）になり、d["name"]という形で値を取得できているのがわかります。

また当然ですが、辞書型からJSON形式の文字列に変換することも可能です。
その際は、json.dumps()に辞書型のデータを渡すことで実現できます。

以下はサンプルトードです。

~~~python
import json

json_text = '{"name": "Taro", "age": 12, "country": "Japan"}'
d = json.loads(json_text)

print(type(d))
# <class 'dict'>

print(d["name"])
# Taro
~~~

---
---

## Depends

FastAPIの `Depends` は、依存関係注入（Dependency Injection）というデザインパターンをサポートするためのツールです。これにより、再利用可能なコードを簡潔かつ効率的に作成でき、テストも容易になります。 `Depends` を使うことで、関数やクラスに特定の依存関係を宣言し、それらを他の関数に注入することができます。

### 基本的な使用法

`Depends` は、依存関係を受け取る側で使われ、別の関数をその依存関係として渡します。例えば、ユーザー認証、データベース接続、共通ロジックなど、再利用したい処理を別の関数にまとめ、それを `Depends` で呼び出すことで、他の関数に注入します。

例：認証機能の依存関係

以下にFastAPIで認証機能を `Depends` で処理する例を示します。

~~~python
from fastapi import Depends, FastAPI, HTTPException

app = FastAPI()

# 依存関係として利用する関数
def get_current_user(token: str):
    if token != "valid-token":
        raise HTTPException(status_code=401, detail="Invalid token")
    return {"user": "John"}

# 依存関係を利用するエンドポイント
@app.get("/users/me")
def read_users_me(current_user: dict = Depends(get_current_user)):
    return current_user
~~~

この例の説明

1. 依存関係関数(get_current_user)：
    - 引数として `token` を受け取ります。通常、このようなトークンはHTTPリクエストのヘッダーやクエリパラメータから取得しますが、この例ではシンプルな形で示しています。
    [`token` を取得する方法](#token-について生成取得)

    - トークンが無効であれば `HTTPException` を投げて401エラーを返します。トークンが有効であれば、ユーザー情報を返します。

2. 依存関係の注入(`Depends(get_current_user)`)：

    - `read_users_me` 関数では、`Depends(get_current_user)` を使って `get_current_user` を自動的に呼び出し、その結果を `current_user` パラメータに代入します。

    - これにより、エンドポイントにアクセスした際に、認証が行われ、認証結果が自動的に注入されます。

### 依存関係のネスト

`Depends` はネストすることもできます。依存関係の関数自体がさらに他の依存関係を持つことが可能です。

~~~python
from fastapi import Depends, FastAPI

app = FastAPI()

def dependency_a():
    return "dependency A"

def dependency_b(dep_a: str = Depends(dependency_a)):
    return f"{dep_a} and dependency B"

@app.get("/")
def read_root(dep_b: str = Depends(dependency_b)):
    return dep_b
~~~

説明：

1. `dependency_a` は単純に文字列を返します。

2. `dependency_b` は `dependency_a` を `Depends` で呼び出し、その結果を使って処理を行います。

3. エンドポイント`read_root`は `Depends(dependency_b)` を使い、`dependency_b` からの結果を返します。

このように、依存関係をネストすることで、複雑なロジックを段階的に組み合わせることができます。

### 依存関係における状態管理

例えば、データベース接続やリクエストごとに状態を保持するものについては、依存関係を状態として扱うこともあります。

~~~python
from fastapi import Depends, FastAPI
from sqlalchemy.orm import Session

# 仮のDBセッションを管理する関数
def get_db():
    db = Session()  # データベース接続を初期化
    try:
        yield db  # コントローラに渡す
    finally:
        db.close()  # 処理後に閉じる

@app.get("/items/")
def read_items(db: Session = Depends(get_db)):
    return {"message": "Database session injected"}
~~~

状態管理の説明：

- `get_db` 関数では、`yield` を使用してデータベースセッションを生成し、リクエストの完了後に自動的にクローズされるようにしています。FastAPIはyieldを使うことで、依存関係がリクエストのライフサイクルに基づいて管理されます。


---
---

## `JWT`について

### `JWT`用の秘密鍵と公開鍵を作成

※ 実行したカレントディレクトリに作成される。

1. 秘密鍵の作成

    以下のコマンドを使って、RSA形式の秘密鍵を作成します。

    ~~~bash
    openssl genpkey -algorithm RSA -out jwt_privkey.pem -pkeyopt rsa_keygen_bits:2048
    ~~~

    - `jwt_privkey.pem`: これがJWT署名用の秘密鍵です。

2. 公開鍵の作成

    公開鍵を以下のコマンドで生成します。

    ~~~bash
    openssl rsa -pubout -in jwt_privkey.pem -out jwt_pubkey.pem
    ~~~

    - `jwt_pubkey.pem`: これがJWTトークンを検証する際に使用する公開鍵です。

### FastAPIアプリケーションで秘密鍵を使用してJWTを生成する。

次に、生成した秘密鍵を使ってJWTを生成するFastAPIのコードを作成します。

1. 必要なパッケージのインストール

    FastAPIでJWTを生成・検証するために、python-joseパッケージをインストールします。

    ~~~bash
    pip install python-jose[cryptography] fastapi uvicorn
    ~~~

2. FastAPIアプリケーションの作成

    以下は、JWTを作成し、後で検証するためのFastAPIアプリケーションコードです。

    ~~~python
    from fastapi import FastAPI, Depends, HTTPException, status
    from jose import JWTError, jwt
    from datetime import datetime, timedelta
    from pydantic import BaseModel

    # 鍵ファイルのパス
    PRIVATE_KEY_PATH = "path_to_your_jwt_privkey.pem"
    PUBLIC_KEY_PATH = "path_to_your_jwt_pubkey.pem"

    # アプリケーションを作成
    app = FastAPI()

    # 秘密鍵を読み込む
    with open(PRIVATE_KEY_PATH, "r") as priv_file:
        private_key = priv_file.read()

    # 公開鍵を読み込む
    with open(PUBLIC_KEY_PATH, "r") as pub_file:
        public_key = pub_file.read()

    # JWT設定
    ALGORITHM = "RS256"
    ACCESS_TOKEN_EXPIRE_MINUTES = 30

    # Pydanticモデル（トークン用）
    class Token(BaseModel):
        access_token: str
        token_type: str

    # JWTトークン生成関数
    def create_access_token(data: dict, expires_delta: timedelta = None):
        to_encode = data.copy()
        if expires_delta:
            expire = datetime.utcnow() + expires_delta
        else:
            expire = datetime.utcnow() + timedelta(minutes=15)
        to_encode.update({"exp": expire})
        encoded_jwt = jwt.encode(to_encode, private_key, algorithm=ALGORITHM)
        return encoded_jwt

    # JWTトークンを検証する関数
    def verify_token(token: str):
        try:
            payload = jwt.decode(token, public_key, algorithms=[ALGORITHM])
            return payload
        except JWTError:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Could not validate credentials",
                headers={"WWW-Authenticate": "Bearer"},
            )

    # ログインしてJWTを発行するエンドポイント
    @app.post("/token", response_model=Token)
    def login_for_access_token():
        # ここでは例としてユーザーIDをデータに含める
        user_data = {"sub": "user_id_123"}
        
        # トークンの有効期限を設定
        access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
        
        # JWTトークンを作成
        access_token = create_access_token(
            data=user_data, expires_delta=access_token_expires
        )
        return {"access_token": access_token, "token_type": "bearer"}

    # トークンを確認するエンドポイント
    @app.get("/verify-token")
    def verify_token_endpoint(token: str):
        # トークンを検証
        payload = verify_token(token)
        return {"token_data": payload}
    ~~~ 
    
    コードの説明：
    - 秘密鍵と公開鍵の読み込み：
        - ファイルとして保存されたRST形式の秘密鍵（`jws_privkey.pem`）と公開鍵（`jws_pubkey.pem`）を読み込みます。
    
    - JWTトークンの生成（`create_access_token`）：
        - `create_access_token`関数で、JWTトークンを生成します。秘密鍵を使って、トークンに署名します。
        - トークンには、データ（例：`{"sub":"user_id_123"}`）と有効期限を含めています。
    
    - JWTトークンの検証（verify_token）：
        - クライアントから受け取ったトークンを、公開鍵を使って検証します。署名が一致しない場合や、有効期限切れの場合にはエラーを発生させます。
    
    - エンドポイント：
        - `/token`：JWTトークンを発行するエンドポイントです。例えば、ログイン時に呼び出され、トークンが発行されます。

        - `/verify-token`：クライアントから送信されたトークンを検証するエンドポイントです。

4. 動作確認：

    ※以下、コマンドプロンプトなどにより実効

    1. トークンを取得する

        `token`エンドポイントにリクエストを送信し、JWTトークンを取得します。

        ~~~bash
        curl -X POST "https://127.0.0.1:8000/token"
        ~~~

        レスポンス例：

        ~~~json
        {
        "access_token": "your_generated_jwt_token_here",
        "token_type": "bearer"
        }
        ~~~

    2. トークンを検証する
        取得したトークンを`/verify-token`エンドポイントに送信して検証します。

        ~~~bash
        curl -X GET "http://127.0.0.1:8000/verify-token?token=your_generated_jwt_token_here"
        ~~~

        レスポンス例：

        ~~~json
        {
            "token_data": {
                "sub": "user_id_123",
                "exp": 1634184634
            }
        }
        ~~~

### まとめ

1. **秘密鍵と公開鍵の生成**: OpenSSLを使って、JWT署名用のRSA秘密鍵と公開鍵を作成します。

2. **FastAPIでJWTを使用**: FastAPIアプリケーション内で、秘密鍵を使ってトークンを生成し、公開鍵でトークンを検証します。

3. **運用上の注意**: JWT専用の秘密鍵を使用し、安全に管理することが重要です。鍵は安全な場所に保存し、必要に応じてローテーションを行います。

このアプローチは、FastAPIでセキュアにJWT認証を行うためのベストプラクティスに従っています。


---
---

## `token` について（生成、取得）

FastAPIでは、HTTPリクエストのヘッダーやクエリパラメータからデータを簡単に受け取ることができます。それぞれの方法でトークンを取得する方法を、具体的なコード例とともに解説します。

### `token` 生成について

FastAPIでログイン認証後にトークンを生成してクライアントに返す方法は、主にJWT（JSON Web Token）を使用することが一般的です。以下に、具体的なステップでトークンを生成してレスポンスとして返す方法を説明します。

### 1. `JWT(JSON Web Token)` のセットアップ

まず、`JWT`トークンを生成するために必要なパッケージをインストールします。

~~~bash
pip install pyjwt[crypto] passlib[bcrypt]
~~~

- `pyjwt`: JWTトークンの生成と検証に使用します。

- `passlib[bcrypt]`: パスワードのハッシュ化と検証に使います。セキュアなパスワードの取り扱いのために推奨されます。

### 2. トークン生成のための設定

FastAPIでJWTを使ってトークンを発行するには、トークンの署名に使用する秘密鍵や、トークンの有効期限を設定します。

例：JWT設定

~~~python
from datetime import datetime, timedelta
from jose import JWTError, jwt
from passlib.context import CryptContext

# 秘密鍵とアルゴリズムを定義
SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

# パスワードのハッシュ化と検証のためのコンテキスト
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

# パスワードのハッシュ化
def hash_password(password: str):
    return pwd_context.hash(password)

# パスワードの検証
def verify_password(plain_password: str, hashed_password: str):
    return pwd_context.verify(plain_password, hashed_password)

# JWTトークンの生成
def create_access_token(data: dict, expires_delta: timedelta = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt
~~~

説明：

- `SECRET_KEY`: トークンの署名に使用する秘密鍵。これを守ることでトークンが改ざんされないようにします。実際のアプリケーションでは、より安全なランダムなキーを使用します。

- `ALGORITHM`: JWTトークンの署名に使用するアルゴリズム。一般的にはHS256が使われます。

- `ACCESS_TOKEN_EXPIRE_MINUTES`: トークンの有効期限（分）。この例では、30分間有効なトークンを発行します。

- `create_access_token`: トークンを生成する関数。ペイロード（`data`）に必要な情報を追加し、有効期限を設定した上でJWTトークンを生成します。

### 3. FastAPIでのログイン処理とトークンの返却

次に、FastAPIでログイン処理を実装し、認証が成功したら、トークンを返すエンドポイントを設定します。

~~~python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from pydantic import BaseModel

app = FastAPI()

# サンプルのユーザーデータ（実際にはデータベースを使う）
fake_users_db = {
    "johndoe": {
        "username": "johndoe",
        "full_name": "John Doe",
        "email": "johndoe@example.com",
        "hashed_password": hash_password("secret"),
    }
}

# ユーザーモデル
class Token(BaseModel):
    access_token: str
    token_type: str

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

# ユーザー認証関数
def authenticate_user(username: str, password: str):
    user = fake_users_db.get(username)
    if not user:
        return False
    if not verify_password(password, user["hashed_password"]):
        return False
    return user

# ログインエンドポイント
@app.post("/token", response_model=Token)
async def login_for_access_token(form_data: OAuth2PasswordRequestForm = Depends()):
    user = authenticate_user(form_data.username, form_data.password)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )
    
    # JWTトークンの生成
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user["username"]}, expires_delta=access_token_expires
    )
    
    return {"access_token": access_token, "token_type": "bearer"}

~~~

説明：

- `OAuth2PasswordBearer`: OAuth2のパスワードフローを使用した認証のための設定。クライアントから/tokenエンドポイントに対してログインリクエストを送信する際に使われます。

- `OAuth2PasswordRequestForm`: ユーザー名とパスワードを含むフォームデータを処理するためのモデル。ログインフォームから送信されるデータを受け取ります。

- `authenticate_user`: ユーザー名とパスワードを検証する関数。ユーザー名が正しいか、パスワードがハッシュと一致するかを確認します。

- `login_for_access_token`: ログイン処理を行うエンドポイント。認証に成功した場合、JWTトークンを生成し、クライアントにレスポンスとして返します。




### `token` 取得について

### 1. HTTPリクエストのヘッダーからトークンを取得する方法

FastAPIでは、`Header` を使ってHTTPヘッダーの値を取得できます。例えば、`Authorization`ヘッダーからトークンを受け取る場合、次のようにします。

~~~python
from fastapi import FastAPI, Header, HTTPException

app = FastAPI()

# ヘッダーからトークンを取得
@app.get("/header-token")
def get_token_from_header(authorization: str = Header(...)):
    if authorization != "Bearer valid-token":
        raise HTTPException(status_code=401, detail="Invalid token")
    return {"token": authorization}
~~~

例の説明：
- `Header(...)`：
    - `Header` を使って、HTTPリクエストの特定のヘッダーから値を取得します。上記の例では `Authorization` ヘッダーを使っています。

    - `Header(...)` の `...` は必須パラメータであることを示しています。これがないと400エラーが返されます。

- `authorization`：
    - この引数に、クライアントが送信した `authorization` ヘッダーの値が自動的に代入されます。Bearerトークン形式で送信されることが多いので、それをチェックしています。

リクエストの送信方法：

リクエストヘッダーにトークンを含める場合、以下のように送信します。

~~~python
GET /header-token HTTP/1.1
Host: localhost:8000
Authorization: Bearer valid-token
~~~

上記のように、ヘッダーに `Authorization` キーを設定し、値にトークンを送ります。

### 2. クエリパラメータからトークンを取得する方法

クエリパラメータからトークンを受け取るには、通常の関数パラメータとして定義します。FastAPIは、自動的にクエリパラメータからその値を取得します。

~~~python
from fastapi import FastAPI, HTTPException

app = FastAPI()

# クエリパラメータからトークンを取得
@app.get("/query-token)
def get_token_from_query(token: str):
    if token != "valid-token":
        raise HTTPException(status_code=401, detail="Invalid token")
    return {"token": token}
~~~

例の説明：

- `token: str`：
    - クエパラメータとして `token` を宣言します。FastAPIはこの関数に基づいて、URL内の `?token=XXXX` の部分を探し、値を取得して `token` に代入します。

リクエストの送信方法：

クエリパラメータとして、トークンを送信する場合、URLにトークンを含めます。

~~~python
GET /query-token?token=valid-token HTTP/1.1
Host: localhost:8000
~~~

この場合、`token` パラメータの値が `valid-token` であることが確認されます。

### 3. ヘッダーとクエリパラメータを組み合わせて使う手法

ヘッダーとクエリパラメータの両方を使ってトークンを受け取る場合、どちらか一方があれば良いという処理も簡単に行えます。

~~~python
from fastapi import FastAPI, Header, HTTPException, Query

app = FastAPI()

# ヘッダーかクエリパラメータのどちらかからトークンを取得
@app.get("/combined-token")
def get_token_from_header_or_query(
    authorization: str = Header(None),  # Noneを指定して必須ではなくする
    token: str = Query(None)            # Noneを指定して必須ではなくする
):
    if authorization:
        if authorization != "Bearer valid-token":
            raise HTTPException(status_code=401, detail="Invalid token in header")
        return {"token": authorization, "source": "header"}
    
    if token:
        if token != "valid-token":
            raise HTTPException(status_code=401, detail="Invalid token in query")
        return {"token": token, "source": "query"}
    
    raise HTTPException(status_code=400, detail="Token not provided")
~~~

処理の流れ：
- まず、ヘッダーからトークンが送信されてるか確認します。ヘッダーにトークンがあればそれを検証し、クエリパラメータから送信されていればクエリを検証します。

- どちらにもトークンが存在しない場合は、400エラーで「トークンが提供されてない」というメッセージを返しましす。

リクエストの送信方法 -> リクエストかクエリパラメータかどちらかで送信する

---
---

## SQLAlchemyモデル


### `relationship`


`relationship`はSQLAlchemyで**リレーション（テーブル間の関連）**を定義するために使用するメソッドです。これを定義することで、テーブルのレコード間にPythonオブジェクトとしての関連を持たせ、直感的にデータを取得・操作できるようになります。

### `relationship`の目的

`relationship`を使用すると、以下のような目的を達成できます：

1. **リレーションを使ったデータの参照**: 
   - 関連するレコードをPythonオブジェクトとして簡単に取得できるようになります。
   - 例えば、`SchoolSchedule`オブジェクトの`class_info`属性を使って、関連する`Class`オブジェクトにアクセスできます。

2. **SQLAlchemy ORMでのデータ操作を簡略化**:
   - 例えば、`SchoolSchedule`に新しい`Class`を関連付ける際、`class_info`に`Class`オブジェクトを直接代入するだけで、SQLAlchemyが内部的に`class_id`を設定してくれます。
   - データを挿入・削除・更新する際に、関連するテーブル同士のID管理が自動的に行われるため、コードが簡潔になります。

### `relationship`を定義するとできること

1. **親テーブル・子テーブルの双方向アクセス**
   - `SchoolSchedule`から`Class`、`Classroom`、`Teacher`、`Student`などの関連データにアクセスするだけでなく、逆に`Class`から関連する`SchoolSchedule`レコードにもアクセスできるように、双方向の関係が作成されます。
   - `back_populates`を使用すると、双方のモデルで関連が定義されるため、例えば`Class`のインスタンスから関連する`SchoolSchedule`レコードにアクセスできます。

2. **リレーションを使ったクエリの簡略化**
   - `relationship`を使用すると、関連するレコードを結合しやすくなります。例えば、`joinedload`を使って関連データを一度に取得することで、クエリのパフォーマンスを向上させられます。
   - 例：
     ```python
     from sqlalchemy.orm import joinedload
     
     schedules = session.query(SchoolSchedule).options(joinedload(SchoolSchedule.class_info)).all()
     for schedule in schedules:
         print(schedule.class_info.class_name)
     ```

3. **関連するデータの自動保存**
   - `SchoolSchedule`と`Class`オブジェクトを新しく作成する場合、`SchoolSchedule`のインスタンスに`Class`のインスタンスを割り当てるだけで、SQLAlchemyが両方のテーブルに関連付けたまま保存してくれます。

### 例
以下のように定義すると、関連データに直接アクセスすることが可能です。

```python
# SchoolScheduleのオブジェクトを取得
schedule = session.query(SchoolSchedule).first()

# 関連するClassの情報にアクセス
print(schedule.class_info.class_name)  # クラス名を取得

# 関連するTeacherの情報にアクセス
print(schedule.teacher.teacher_f_name)  # 教師のファーストネームを取得
```

### `relationship`の使い方と利点
- **使いやすさ**: `ForeignKey`でテーブル間の関係を定義するだけでなく、`relationship`を使うと関連データにPythonオブジェクトとしてアクセスでき、クエリや操作が簡単になります。
- **双方向アクセス**: `back_populates`を使うことで、関連するデータの双方向アクセスが可能になり、テーブル間の関連付けが明確になります。

---
---

## SQLAlchemyのクエリ

### `.all()`, `.first()`

### `.scalars()`の仕様

### `in_()`による複数条件

### `join()`による連結

### `.option()`
SQLAlchemyの`options`は、クエリの実行時に特定のオプションを設定するために使用されます。これにより、データの取得方法やパフォーマンスの最適化が可能になります。以下に、`options`でできる主な機能について詳しく解説します。

#### 1. **Eager Loading（事前読み込み）**

- **`joinedload`**: 関連するデータを同時に取得するために、SQLの`JOIN`を使用します。これにより、関連データを1つのクエリで取得でき、N+1クエリ問題を回避します。

  ```python
  from sqlalchemy.orm import joinedload

  query = session.query(Parent).options(joinedload(Parent.children)).all()
  ```

- **`subqueryload`**: サブクエリを使って関連データを取得します。これもN+1問題を解決しますが、`JOIN`を使わないため、データが多い場合に効果的です。

  ```python
  from sqlalchemy.orm import subqueryload

  query = session.query(Parent).options(subqueryload(Parent.children)).all()
  ```

`joinedload`と`subqueryload`は、SQLAlchemyにおけるEager Loadingの手法であり、関連するオブジェクトを効率的に取得するための方法です。両者の主な違いを以下に説明します。

#### 1. `joinedload`

- **動作**: `joinedload`は、SQLの`JOIN`を使用して、親オブジェクトと関連する子オブジェクトを1つのクエリで同時に取得します。
- **実行されるSQL**: 親テーブルと子テーブルの間にJOINを使ったSQLが生成され、すべての関連データを1回のクエリで取得します。
- **利点**: データを1回のクエリで取得できるため、N+1クエリ問題を回避できます。関連データが少ない場合、パフォーマンスが良いです。
- **欠点**: 取得するデータの量が多くなると、結果が大きな行になり、メモリやネットワークの負担が増える可能性があります。

#### 2. `subqueryload`

- **動作**: `subqueryload`は、サブクエリを使って関連する子オブジェクトを取得します。最初に親オブジェクトを取得し、その後で関連する子オブジェクトを別のクエリで取得します。
- **実行されるSQL**: 最初のクエリで親オブジェクトを取得し、その後、サブクエリが生成されて関連する子オブジェクトを取得します。このサブクエリは、親オブジェクトのIDを使って子オブジェクトを選択します。
- **利点**: 取得するデータのサイズが大きくなることを避けられるため、メモリの効率が良いです。親オブジェクトの数が多い場合、特に効果的です。
- **欠点**: 親オブジェクトを取得するためのクエリとは別に、追加のサブクエリが発行されるため、場合によってはパフォーマンスが低下することがあります。

#### 具体的な違いのまとめ

| 特徴              | `joinedload`                      | `subqueryload`                    |
|-------------------|-----------------------------------|------------------------------------|
| クエリ数          | 1回（JOINを使用）                | 2回（親用と子用のサブクエリ）      |
| データ取得方法    | JOINを使って1つの結果セットで取得 | サブクエリを使って別の結果セットで取得 |
| メモリ使用        | 結果が大きくなることがある       | メモリ効率が良い                   |
| パフォーマンスの傾向 | 関連データが少ない場合に効果的  | 親オブジェクトが多い場合に効果的  |

#### どちらを使うべきか

- **`joinedload`**: 関連するデータの量が少なく、1回のクエリで取得する方が効率的な場合に使用。
- **`subqueryload`**: 関連するデータの量が多く、メモリ使用を抑えたい場合に使用。

それぞれの用途に応じて、適切な方法を選択することが重要です。

### 2. **Lazy Loading（遅延読み込み）**

- **`lazyload`**: デフォルトでは、関連データは必要になるまで取得されません。これにより、必要ないデータの取得を避けることができます。

  ```python
  from sqlalchemy.orm import lazyload

  query = session.query(Parent).options(lazyload(Parent.children)).all()
  ```

### 3. **Selectin Loading**

- **`selectinload`**: `JOIN`を使わずに関連データを複数のSELECT文で取得します。通常、親の各エントリに対して関連する子を1回のクエリで取得します。

  ```python
  from sqlalchemy.orm import selectinload

  query = session.query(Parent).options(selectinload(Parent.children)).all()
  ```

### 4. **Columns and Attributes Loading**

- **`load_only`**: 特定の列だけをロードし、他の列は無視します。これにより、不要なデータを取得せず、パフォーマンスを向上させることができます。

  ```python
  from sqlalchemy.orm import load_only

  query = session.query(Parent).options(load_only(Parent.name)).all()
  ```

### 5. **Inherit Load Strategies**

- **`inheritance`**: 多重継承を使った場合の読み込み戦略を指定します。これにより、異なるタイプのオブジェクトを一つのクエリで適切に取得できます。

### 6. **その他のオプション**

- **`defer`**: 特定の列を遅延読み込みするために使用します。指定した列は、最初のクエリで取得されず、必要になったときに別のクエリで取得されます。

  ```python
  from sqlalchemy.orm import defer

  query = session.query(Parent).options(defer(Parent.large_data_field)).all()
  ```

- **`noload`**: 特定の関連をまったく読み込まないように設定します。

  ```python
  from sqlalchemy.orm import noload

  query = session.query(Parent).options(noload(Parent.children)).all()
  ```

### まとめ

`options`を使用することで、クエリのパフォーマンスを最適化したり、データの取得方法を細かく制御したりすることができます。これにより、効率的にデータを取得でき、アプリケーションのパフォーマンス向上に寄与します。使用するオプションは、アプリケーションの要件やデータの構造に応じて選択することが重要です。

---
---

## `Base`モデル内の設定など

### `to_dict`について

### `relationship`について

---
---

## `pydantic`モデル

### `from_attributes`

```python
class Config:
        from_attributes = True
```

```python
class_response_list = [ClassItem.from_orm(class_value) for class_value in class_response]
```
