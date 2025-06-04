# FastAPI チュートリアル - マニュアル

**参考ページ**
>[チュートリアル - ユーザーガイド](https://fastapi.tiangolo.com/ja/tutorial/)

## 目次

1. [最初のステップ](#1-最初のステップ)

    1. [FastAPIの最もシンプルなコード](#最もシンプルなfastapiファイルは以下のようになります)

    2. [ブラウザでのチェック](#チェック)

    3. [対話的APIドキュメント](#対話的apiドキュメント)

2. [基本コード解説](#2-ステップ毎の要約)

    1. [FastAPIのインポート](#1-fastapiをインポート)

    2. [FastAPIのインスタンス生成](#2-fastapiのインスタンスを生成)

    3. [path-operationを作成](#3-path-operationを作成)

    4. [パスオペレーションを定義](#4-パスオペレーションを定義)

    5. [コンテンツの返信](#5-コンテンツの返信)

3. [パスパラメータ](#3-パスパラメーター)

4. [クエリパラメータ](#4-クエリパラメーター)

5. [リクエストボディ](#5-リクエストボディ)

## 1. 最初のステップ

> **備考**：<br>
`uvicorn main:app`は以下を示します。
>>**main**: `main.py`ファイル (Python "module")。<br>
**app**: `main.py`内部で作られるobject（`app = FastAPI()`のように記述される）。<br>
**--reload**: コードの変更時にサーバーを再起動させる。開発用。

出力には次のような行があります:

~~~bash
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
~~~

この行はローカルマシンでアプリが提供されているURLを示しています。

### 最もシンプルなFastAPIファイルは以下のようになります：

~~~python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
~~~

### チェック：

ブラウザで [http://127.0.0.1:8000](http://127.0.0.1:8000) を開きます。<br>
※上記はローカル環境でのパス。<br>
※971cm.comのアクセス先は[https://971cm.com](https://971cm.com)

次のようなJSONレスポンスが表示されます:

~~~json
{"message": "Hello World"}
~~~

### 対話的APIドキュメント：

次に、 [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) にアクセスします。<br>
※971cm.comのアクセス先は[971cm.com/docs](https://971cm.com/docs)

自動生成された対話的APIドキュメントが表示されます。

### 他のAPIドキュメント：

- [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc) <br>
※971cm.comのアクセス先は[971cm.com/redoc](https://971cm.com/redoc)

## 2. ステップ毎の要約

### 1. FastAPIをインポート：

~~~python
from fastapi import FastAPI
~~~
`FastAPI`は、APIのすべての機能を提供するPythonクラスです。

### 2. `FastAPI`の「インスタンス」を生成：

~~~python
app = FastAPI()
~~~

ここで、`app`変数が`FastAPI`クラスの「インスタンス」になります。<br>
これが、すべてのAPIを作成するための主要なポイントになります。
この`app`はコマンドで`uvicorn`が参照するものと同じです

### 3. *path operation*を作成：

#### パス：

ここでの「パス」とは、最初の`/`から始まるURLの最後の部分を指します。<br>
したがって、次のようなURLでは: <br>
`https://example.com/items/foo`<br>
...パスは次のようになります。<br>
`/items/foo`

> 「パス」は一般に「エンドポイント」または「ルート」とも呼ばれます。

APIを構築する際、「パス」は「関心事」と「リソース」を分離するための主要な方法です。


#### Operation：

ここでの「オペレーション」とは、HTTPの「メソッド」の1つを指します。

以下のようなものの1つ：

|メジャー|マイナー|
|:--:|:--:|
|POST|OPTION|
|GET|HEAD|
|PUT|PATCH|
|DELETE|TRACE|

HTTPプロトコルでは、これらの「メソッド」の1つ（または複数）を使用して各パスにアクセスできます。

通常は次を使用します:

`POST`：データの作成<br>
`GET`：データの読み取り<br>
`PUT`：データの更新<br>
`DELETE`：データの削除

したがって、OpenAPIでは、各HTTPメソッドは「オペレーション」と呼ばれます。

#### パスオペレーションデコーダを定義：

~~~python
@app.get("/")
~~~

`@app.get("/")`は直下の関数が下記のリクエストの処理を担当することを**FastAPI**に伝えます:

- パス：`/`

- オペレーション：`get`

>@decorator について
>>Pythonにおける`@something`シンタックスはデコレータと呼ばれます。<br><br>
「デコレータ」は関数の上に置きます。かわいらしい装飾的な帽子のようです。<br><br>
「デコレータ」は直下の関数を受け取り、それを使って何かを行います。<br><br>
私たちの場合、このデコレーターは直下の関数が**オペレーション** `get`を使用した**パス**`/`に対応することを**FastAPI** に通知します。<br><br>
これが「パスオペレーションデコレータ」です。

### 4. パスオペレーションを定義

以下は、「**パスオペレーション関数**」です。

- **パス**：`/`
- **オペレーション**：`get`
- **関数**：デコレータ」の直下にある関数 （`@app.get("/")`の直下）
~~~python
async def root():
~~~

これは、Pythonの関数です。

この関数は、`GET`オペレーションを使ったURL「`/`」へのリクエストを受け取るたびに**FastAPI**によって呼び出されます。

この場合、この関数はasync関数です。

`async def`の代わりに通常の関数として定義することもできます:
~~~python
def root():
~~~

>データベース、API、ファイルシステムなどと通信し、await の使用を**サポートしていない**サードパーティライブラリ (**現在のほとんどのデータベースライブラリに当てはまります**) を使用している場合、次の様に、単に def を使用して通常通り path operation 関数 を宣言してください:

~~~python
def result():
~~~
>アプリケーションが (どういうわけか) 他の何とも通信せず、応答を待つ必要がない場合は、`async def` を使用して下さい。

>よく分からない場合は、通常の `def` を使用して下さい。

### 5. コンテンツの返信

~~~python
return {"message": "Hello World"}
~~~

`dict`、`list`、`str`、`int`などを返すことができます。

Pydanticモデルを返すこともできます（後で詳しく説明します）。

JSONに自動的に変換されるオブジェクトやモデルは他にもたくさんあります（ORMなど）。 お気に入りのものを使ってみてください。すでにサポートされている可能性が高いです。

### まとめ
- `FastAPI`をインポート

- `app`インスタンスを生成

- **パスオペレーションデコレータ**を記述 (@app.get("/"))

- **パスオペレーション関数**を定義 (上記のdef root(): ...のように)

- 開発サーバーを起動 (uvicorn main:app --reload)

---
---

## 3. パスパラメーター

Pythonのformat文字列と同様のシンタックスで「パスパラメータ」や「パス変数」を宣言できます:

~~~python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id):
    return {"item_id": item_id}
~~~

パスパラメータ `item_id` の値は、引数 `item_id` として関数に渡されます。

しがたって、この例を実行して `http://127.0.0.1:8000/items/foo` にアクセスすると、次のレスポンスが表示されます。

~~~python
{"item_id":"foo"}
~~~

### 1. パスパラメーターと型

標準のPythonの`型アノテーション`を使用して、関数内のパスパラメータの型を宣言できます:

~~~python
@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
~~~

ここでは、 `item_id` は `int` として宣言されています。

> 補足：<br>
`型アノテーション` は実行時にエラー処理をしないので表面的なものでしかない。

### 2. データ変換

この例を実行し、ブラウザで `http://127.0.0.1:8000/items/3` を開くと、次のレスポンスが表示されます:

~~~python
{"item_id":3}
~~~

>補足:<br>
関数が受け取った（および返した）値は、文字列の "3" ではなく、Pythonの int としての 3 であることに注意してください。<br>
したがって、型宣言を使用すると、**FastAPI**は自動リクエスト "解析" を行います

### 3. データバリエーション

しかしブラウザで `http://127.0.0.1:8000/items/foo` を開くと、次のHTTPエラーが表示されます

これは、パスパラメータ `item_id` が `int` ではない値 `"foo"` だからです。

`http://127.0.0.1:8000/items/4.2` で見られるように、intのかわりに `float` が与えられた場合にも同様なエラーが表示されます。

>したがって、Pythonの型宣言を使用することで、**FastAPI**はデータのバリデーションを行います。<br> 
これは、APIに関連するコードの開発およびデバッグに非常に役立ちます。

~~~python
#エラー内容：
{
    "detail": [
        {
            "loc": [
                "path",
                "item_id"
            ],
            "msg": "value is not a valid integer",
            "type": "type_error.integer"
        }
    ]
}
~~~

### 4. Pydantic

すべてのデータバリデーションは Pydantic によって内部で実行されるため、Pydanticの全てのメリットが得られます。そして、安心して利用することができます。

`str`、 `float` 、 `bool` および他の多くの複雑なデータ型を型宣言に使用できます。

これらのいくつかについては、チュートリアルの次の章で説明します。

### 5. 順序の問題

path operations を作成する際、固定パスをもつ状況があり得ます。

`/users/me` から、現在のユーザに関するデータを取得するとします。

さらに、ユーザIDによって特定のユーザに関する情報を取得するパス `/users/{user_id}` ももつことができます。

path operations は順に評価されるので、 `/users/me` が `/users/{user_id}` よりも先に宣言されているか確認する必要があります:

~~~python
@app.get("/users/me")
async def read_user_me():
    return {"user_id": "the current user"}


@app.get("/users/{user_id}")
async def read_user(user_id: str):
    return {"user_id": user_id}
~~~

`/users/me` と `/users/{user_id}` が逆の順で宣言されると `/users/me` も `@app.get("/users/me")` で処理が実行されてしまいます。

### 6. 定義済みの値

パスパラメータを受け取る path operation をもち、有効なパスパラメータの値を事前に定義したい場合は、標準のPython `Enum` を利用できます。

#### `Enum`クラスの作成

`Enum` をインポートし、 `str` と `Enum` を継承したサブクラスを作成します。

`str` を継承することで、APIドキュメントは値が `文字列` でなければいけないことを知り、正確にレンダリングできるようになります。

そして、固定値のクラス属性を作ります。すると、その値が使用可能な値となります:

~~~python
# Enumをインポート
from enum import Enum

from fastapi import FastAPI

# `str` と `Enum` を継承したサブクラス。
class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"


app = FastAPI()


@app.get("/models/{model_name}")
# 作成したenumクラスであるModelNameを使用した型アノテーションをもつ*パスパラメータ*を作成
async def get_model(model_name: ModelName):
    if model_name is ModelName.alexnet:
        return {"model_name": model_name, "message": "Deep Learning FTW!"}

    if model_name.value == "lenet":
        return {"model_name": model_name, "message": "LeCNN all the images"}

    return {"model_name": model_name, "message": "Have some residuals"}
~~~

### 7. パス変換

Starletteのオプションを直接使用することで、以下のURLの様な*パス*を含んだ、*パスパラメータ*の宣言ができます:

~~~python
/files/{file_path:path}
~~~

この場合、パラメータ名は file_path です。そして、最後の部分 :path はパラメータがいかなる*パス*にもマッチすることを示します。

つまり、以下のコードの場合 `/files//home/johndoe/myfile.txt` で `/home/johndoe/myfile.txt` が返される。

~~~python
@app.get("/files/{file_path:path}")
async def read_file(file_path: str):
    return {"file_path": file_path}
~~~

>補足：**Starlette**の機能<br>
**FastAPI**は、Starlette と完全に互換性があります（そしてベースになっています）。したがって、追加のStarletteコードがあれば、それも機能します。<br><br>
FastAPIは実際にはStarletteのサブクラスです。したがって、Starletteをすでに知っているか使用している場合は、ほとんどの機能が同じように機能します。

---
---


## 4. クエリパラメーター

パスパラメータではない関数パラメータを宣言すると、それらは自動的に "クエリ" パラメータとして解釈されます。

~~~python
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
~~~

クエリはURL内で `?` の後に続くキーとバリューの組で、 `&` で区切られています。

例えば、以下の様なURL内で:

`http://127.0.0.1:8000/items/?skip=0&limit=10`

クエリパラメーターは：

- `skip`：値は`0`

- `limit`：値は`10`

これらはURLの一部なので、「自然に」文字列になります。

しかしPythonの型を宣言すると (上記の例では int として)、その型に変換されバリデーションが行われます。

### 1. デフォルト：

クエリパラメータはパスの固定部分ではないので、オプショナルとしたり、デフォルト値をもつことができます。

上述の例では、skip=0 と limit=10 というデフォルト値を持っています。

したがって、以下のURLにアクセスすることは:

`http://127.0.0.1:8000/items/`

以下のURLにアクセスすることと同等になります:

`http://127.0.0.1:8000/items/?skip=0&limit=10`

しかし、例えば、以下にアクセスすると:

`http://127.0.0.1:8000/items/?skip=20`

関数内のパラメータの値は以下の様になります:

- `skip=20`: URL内にセットしたため

- `limit=10`: デフォルト値

### 2. オプショナルなパラメータ

同様に、デフォルト値を None とすることで、オプショナルなクエリパラメータを宣言できます:

~~~python
@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}
~~~

この場合、関数パラメータ `q` はオプショナルとなり、デフォルトでは `None` になります。

### 3. クエリパラメータの型変換

`bool` 型も宣言できます。これは以下の様に変換されます:

~~~python
@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None, short: bool = False):
    item = {"item_id": item_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
~~~

この場合、以下にアクセスすると:

`http://127.0.0.1:8000/items/foo?short=1`

もしくは、

`http://127.0.0.1:8000/items/foo?short=True`

もしくは、

`http://127.0.0.1:8000/items/foo?short=true`

もしくは、

`http://127.0.0.1:8000/items/foo?short=on`

もしくは、

`http://127.0.0.1:8000/items/foo?short=yes`

もしくは、他の大文字小文字のバリエーション (アッパーケース、最初の文字だけアッパーケース、など)で、関数は `short` パラメータを `True` な `bool` 値として扱います。それ以外は `False` になります。


### 4. 複数のパスパラメータとクエリパラメータ

複数のパスパラメータとクエリパラメータを同時に宣言できます。**FastAPI**は互いを区別できます。

そして特定の順序で宣言しなくてもよいです。

名前で判別されます:

~~~python
@app.get("/users/{user_id}/items/{item_id}")
async def read_user_item(
    user_id: int, item_id: str, q: Union[str, None] = None, short: bool = False
):
    item = {"item_id": item_id, "owner_id": user_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
~~~

### 5. 必須のクエリパラメータ

パスパラメータ以外のパラメータ (今のところ、クエリパラメータのみ説明しました) のデフォルト値を宣言した場合、そのパラメータは必須ではなくなります。

特定の値を与えずにただオプショナルにしたい場合はデフォルト値を None にして下さい。

しかしクエリパラメータを必須にしたい場合は、ただデフォルト値を宣言しなければよいです:

~~~python
@app.get("/items/{item_id}")
async def read_user_item(item_id: str, needy: str):
~~~

ここで、クエリパラメータ `needy` は `str` 型の必須のクエリパラメータです

以下のURLをブラウザで開くと:

`http://127.0.0.1:8000/items/foo-item`

...必須のパラメータ needy を加えなかったので、エラーが発生します。







### 番外編：パスパラメータ・クエリパラメータ違い

1. **見た目の違い**

    1. `https://zenn.dev/search`

    2. `https://zenn.dev/search?q=Laravel`

    ①と②の見た目違いとして「search」の後に「?〜」が」あるかどうか

    ①のパスパラメータはsearchの部分になる

    ②の場合、パスパラメータは①と同じくsearch、クエリパラメータは?q=Laravel

1. **中身の違い**

    - `パスパラメータ`は特定のもの（画面など）を表示したいときに必要になります。

    - `クエリパラメータ`は特定のもの（画面など）に条件を加える場合に必要になります。


---
---

## 5. リクエストボディ

クライアント (ブラウザなど) からAPIにデータを送信する必要があるとき、データを `リクエストボディ` (request body) として送ります。

`リクエスト` ボディはクライアントによってAPIへ送られます。`レスポンス` ボディはAPIがクライアントに送るデータです。

**APIはほとんどの場合** `レスポンス` ボディを送らなければなりません。しかし、**クライアントは必ずしも** `リクエスト` ボディを送らなければいけないわけではありません。

`リクエスト` ボディを宣言するために Pydantic モデルを使用します。そして、その全てのパワーとメリットを利用します。

### 1. `Pydantic` の `BaseModel` をインポート

まず初めに、`Pydantic` から `BaseModel` をインポートする必要があります。

~~~python
from pydantic import BaseModel
~~~

### 2. データモデルの作成

そして、`BaseModel` を継承したクラスとしてデータモデルを宣言します。

すべての属性にpython標準の型を使用します:

~~~python
class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None
~~~

クエリパラメータの宣言と同様に、モデル属性がデフォルト値をもつとき、必須な属性ではなくなります。それ以外は必須になります。オプショナルな属性にしたい場合は `None` を使用してください。

例えば、上記のモデルは以下の様なJSON「`オブジェクト`」(もしくはPythonの `dict` ) を宣言しています:

~~~python
{
    "name": "Foo",
    "description": "An optional description",
    "price": 45.2,
    "tax": 3.5
}

#...description と tax はオプショナル (デフォルト値は None) なので、以下のJSON「オブジェクト」も有効です:
{
    "name": "Foo",
    "price": 45.2
}
~~~

### 3. パラメータとして宣言

パスオペレーション に加えるために、パスパラメータやクエリパラメータと同じ様に宣言します:

~~~python
@app.post("/items/")
async def create_item(item: Item):
    return item
~~~

### 4. モデルの使用

関数内部で、モデルの全ての属性に直接アクセスできます:

~~~python

@app.post("/items/")
async def create_item(item: Item):
    item_dict = item.dict()
    if item.tax:
        price_with_tax = item.price + item.tax #ここ
        item_dict.update({"price_with_tax": price_with_tax})
    return item_dict
~~~

### 5. リクエストボディ + パスパラメータ (+ クエリパラメータ)

パスパラメータとリクエストボディを同時に宣言できます。

パスパラメータである関数パラメータは**パスから受け取り**、Pydanticモデルによって宣言された関数パラメータは**リクエストボディから受け取る**ということをFastAPIは認識します。

~~~python
@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):#ここ
    #第一引数はパスから、第二引数はリクエストボディから。
    return {"item_id": item_id, **item.dict()}

    ## * ** 可変長引数
~~~

また、**ボディ**と**パス**と**クエリ**のパラメータも同時に宣言できます。

~~~python
@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item, q: Union[str, None] = None):
    result = {"item_id": item_id, **item.dict()}
    if q:
        result.update({"q": q})
    return result
~~~

関数パラメータは以下の様に認識されます:

- パラメータが**パス**で宣言されている場合は、優先的にパスパラメータとして扱われます。(`item_id: int` はパスで `{item_id}` と宣言されている。)
- パラメータが**単数型** (int、float、str、bool など)の場合は**クエリ**パラメータとして解釈されます。
- パラメータが **Pydantic モデル**型で宣言された場合、リクエスト**ボディ**として解釈されます。(`item` は `Pydantic モデル`)
