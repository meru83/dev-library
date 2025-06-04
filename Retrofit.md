# Javaを使って`Retrofit`と`FastAPI`を連携するための設定とコーディングについて

## 目次

- [awsとandroidstudio間のrestapi設計](#awsとandroid-studio間のrestapi設計)
    - [retrofitとgsonの依存関係を追加する](#1-retrofitとgsonの依存関係を追加する)
    - また目次いつか書く

- [初回ログイン以降ログインなしにする](#初回ログイン以降ログインなしにする)

## ~　AWSとAndroid Studio間のRestAPI設計　~

>型安全な Android 向けの HTTP クライアントライブラリです。<br>
>正確にはOkHttpのラッパーで、アノテーションなどを使ってより実装しやすくするためのライブラリです。

以下は、Retrofitの基本的な使い方の記事です。
>https://square.github.io/retrofit/ <br>
>https://kumaskun.hatenablog.com/entry/2021/02/13/113303

## 1. RetrofitとGsonの依存関係を追加する

> 最初にRetrofitとGsonのライブラリをプロジェクトに追加する必要があります。これにより、APIとの通信や、JSON形式のデータを扱うためのGsonが利用できるようになります。

- **`build.gradle`（アプリモジュールレベル）での作業**
    
- Android Studioで左側のプロジェクトツリーから `app` -> `Gradle Scripts` -> `build.gradle (Module: app)` を開きます。
- `dependencies` セクションにRetrofitとGsonの依存関係を追加します。以下のように、Javaプロジェクト向けの設定を行います。
    ```
    dependencies {
        // Retrofitの依存関係
        implementation 'com.squareup.retrofit2:retrofit:2.9.0'

        // Gson (JSONをパースするため)
        implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
    }
    ```
    Android Studioから提案（黄色い電球マーク）が来たら多分「Library Catalogを使う」というお誘いだと思うのでどちらで統一してもOK！
- **同期**：`Sync Now`をクリックして依存関係(ライブラリなど)を同期。これでRetrofitとGsonがプロジェクトに追加されます。
    
## 2. **RetrofitのAPIインターフェースを定義する**

> FastAPIで構築されたAPIにリクエストを送るための定義を行います。具体的には、APIのエンドポイントやリクエスト方法（GET, POSTなど）を定義します。    
- **ファイル名** : 
    `ApiService.java`
- **ディレクトリ** :
    `app/src/main/java/com/yourpackage/`<br>
    もしくは<br>
    `app/src/main/java/com.yourpackage.~`
- **内容**：
    以下のコードは、ユーザー情報を取得するAPIエンドポイントの例です。これはFastAPI側のエンドポイントに対応しています。
    ~~~java
    package com.yourpackage;

    import retrofit2.Call;
    import retrofit2.http.GET;
    import retrofit2.http.Path;

    public interface ApiService {
        // ユーザーIDに基づいてユーザー情報を取得するAPIエンドポイント
        @GET("users/{id}")
        Call<User> getUserById(@Path("id") String id);
    }
    ~~~
- **説明**:
    - `@GET("users/{id}")`は、HTTP GETリクエストを指定しています。users/{id} はFastAPI側のエンドポイントを指している。
    （IDに応じてユーザー情報を取得するという処理を想定している）
    - `Call<User>`は、サーバーからのレスポンスを`User`クラスとしてうけとることを表している。（の User クラスは後ほど定義します）
    - `@Path("id")`は、リクエストURL内の {id} を動的に置き換えるためのものです。

- **追加のエンドポイント**：
    他のエンドポイントがある場合、同じインターフェースに追加することができます。
    - POSTリクエスト
        ~~~java
        import retrofit2.http.Body;
        import retrofit2.http.POST;

        public interface ApiService {
            // 新しいユーザーを作成するエンドポイント
            @POST("users")
            Call<User> createUser(@Body User user);
        }
        ~~~
- **説明**：
    - `@POST("users")`はPOSTリクエストを示し、サーバーに新しいユーザーを作成します。
    - `@Body` はリクエストボディに `User` オブジェクトを含めることを意味します。
## 3. **`User`クラスの作成**
>まず、APIのレスポンスを受け取るためのデータクラスを定義します。FastAPIから送られてくるデータ形式に合わせてクラスを作成します。今回の例では、ユーザーのIDと名前がレスポンスとして返されると仮定してUserクラスを作成します。
- **ファイル名**：
    `User.java`
- **ディレクトリ**：
    `app/src/main/java/com/example/connect/model/`（`model`というパッケージを新規作成し、その中にフイルを置きます）
- **内容**：
    `User`クラスには、ユーザーのIDと名前に対応するフィールドを定義し、ゲッターとセッターを追加します。
    ~~~java
    package com.example.connect.model;

    public class User {
        private String id;
        private String name;

        // コンストラクタ（必要に応じて追加）
        public User(String id, String name) {
            this.id = id;
            this.name = name;
        }

        // ゲッターとセッター
        public String getId() {
            return id;
        }

        public void setId(String id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
    ~~~
- **説明**：
    - `id` と `name` はAPIレスポンスのフィールドに対応します。これをFastAPIが返すJSON形式に合わせます。
    - ゲッター (`getId`, `getName`) とセッター (`setId`, `setName`) を定義し、これを使ってユーザー情報にアクセスできるようにします。

    これで、APIレスポンスを正しく受け取るための User クラスが作成されました。
## 4. **Retrofitインスタンスの作成**
>次に、Retrofitを使ってAPIリクエストを行うためのRetrofitインスタンスを作成します。これをシングルトンパターンで作成し、プロジェクト全体で使い回すのが一般的です。
- **ファイル名**：
    `RetrofitClient.java`
- **ディレクトリ**：
    `app/src/main/java/com/example/connect/network/`(`network`パッケージを新規作成し、その中にファイルを置きます)
- **内容**：
    Retrofitの設定を行い、APIベースURLやGsonコンバータの設定を行います。
    ~~~java
    package com.example.connect.network;

    import retrofit2.Retrofit;
    import retrofit2.converter.gson.GsonConverterFactory;

    public class RetrofitClient {

        private static final String BASE_URL = "http://your-ec2-ip-address:port/"; // EC2のIPとポートを指定
        private static Retrofit retrofit = null;

        // Retrofitインスタンスを取得するメソッド
        public static Retrofit getClient() {
            if (retrofit == null) {
                retrofit = new Retrofit.Builder()
                        .baseUrl(BASE_URL)
                        .addConverterFactory(GsonConverterFactory.create())
                        .build();
            }
            return retrofit;
        }
    }
    ~~~
- **説明**：
    - `BASE_URL`: これはFastAPIがホストされているEC2インスタンスのURLです。IPアドレスとポート番号を設定してください（例: `http://54.123.45.67:8000/`）
    - `Retrofit`: `Retrofit.Builder()` を使ってRetrofitインスタンスを作成し、`GsonConverterFactory` を追加して、JSON形式のデータを自動でパースできるようにしています。
    - **シングルトンパターン**：`getClient()` メソッドでRetrofitインスタンスを1回だけ作成し、再利用できるようにしています。
## 5. **APIリクエストの実装（MainActivity）**
> MainActivityクラス内で実際にAPIリクエストを呼び出す処理を実装します。これにより、Retrofitを使用してFastAPIにリクエストを送り、結果を取得する部分を作成します。

今回は、以下の二つの処理を実装します。
1. **ユーザーIDを指定してユーザー情報を取得する**(getUserById メソッドの使用)
2. **新しいユーザーを作成する**(createUser メソッドの使用)
- **ファイル名**：
`MainActivity.java`
- **ディレクトリ**：
`app/src/main/java/com/example/connect/`既に存在している`MainActivity.java`ファイルを開きます）
- **コード**：以下のコードでは、`getUserById`と`createUser`を使ってFastAPIからデータを取得・送信するサンプルを示しています。
    ~~~java
    package com.example.connect;

    import androidx.appcompat.app.AppCompatActivity;
    import android.os.Bundle;
    import android.util.Log;
    import com.example.connect.api.ApiService;
    import com.example.connect.model.User;
    import com.example.connect.network.RetrofitClient;
    import retrofit2.Call;
    import retrofit2.Callback;
    import retrofit2.Response;

    public class MainActivity extends AppCompatActivity {

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            // Retrofitインスタンスを取得して、ApiServiceを作成
            ApiService apiService = RetrofitClient.getClient().create(ApiService.class);

            // ユーザーID "1" に基づいてユーザー情報を取得する処理
            Call<User> call = apiService.getUserById("1");
            call.enqueue(new Callback<User>() {
                @Override
                public void onResponse(Call<User> call, Response<User> response) {
                    if (response.isSuccessful()) {
                        User user = response.body();
                        if (user != null) {
                            Log.d("MainActivity", "ユーザー名: " + user.getName());
                        }
                    } else {
                        Log.e("MainActivity", "Error: " + response.code());
                    }
                }

                @Override
                public void onFailure(Call<User> call, Throwable t) {
                    Log.e("MainActivity", "APIリクエスト失敗: " + t.getMessage());
                }
            });

            // 新しいユーザーを作成する処理
            User newUser = new User("123", "新しいユーザー");
            Call<User> createCall = apiService.createUser(newUser);
            createCall.enqueue(new Callback<User>() {
                @Override
                public void onResponse(Call<User> call, Response<User> response) {
                    if (response.isSuccessful()) {
                        User createdUser = response.body();
                        if (createdUser != null) {
                            Log.d("MainActivity", "作成されたユーザー名: " + createdUser.getName());
                        }
                    } else {
                        Log.e("MainActivity", "Error: " + response.code());
                    }
                }

                @Override
                public void onFailure(Call<User> call, Throwable t) {
                    Log.e("MainActivity", "APIリクエスト失敗: " + t.getMessage());
                }
            });
        }
    }
    ~~~
- **説明**：
    - **APIの呼び出し**：
        -  `RetrofitClient.getClient()`を使ってRetrofitインスタンスを取得し、`ApiService`インターフェースを生成しています。
        - `getUserById("1")`で、ユーザーIDが`"1"`のユーザー情報を取得し、レスポンスをログに表示します。
        - `createUser(newUser)`で、新しいユーザー（ID: `123`, 名前: `新しいユーザー`）を作成します。
    - `enqueue()`：
        - 非同期リクエストを行うために、`enqueue()`メソッドを使ってAPIリクエストを送信しています。レスポンスが成功した場合は`onResponse()`が、失敗した場合は`onFailure()`が呼び出されます。
    - **エラーハンドリング**：
        - レスポンスが失敗した場合は、エラーコード（例: `404`, `500`）をログに出力し、通信に失敗した場合もエラーメッセージをログに出力します。
    
## 6. FastAPI側
>FastAPI環境はAWS EC2上に構築されているものとし、PythonでAPIを実装します。Pythonの仮想環境がセットアップされている場合、以下の手順に従って進めてください。

1. **必要なパッケージのインストール**
    
    FastAPIとデータ管理のために必要なパッケージをインストールします。
    ~~~bash
    pip install fastapi uvicorn
    pip install pydantic
    pip install sqlalchemy  # データベースを使う場合
    ~~~
    - **FastAPI**：
        APIフレームワーク
    - **Pydantic**：
        データモデルのバリデーションやシリアライゼーションを行うライブラリ
    - **SQLAlchemy**：
        データベース操作用（使用する場合）
1. **FastAPIアプリの基本設定**
    
    FastAPIのアプリケーションファイルを作成します。
    - **ファイル名**：
        `main.py`
    - **ディレクトリ**：
        EC2サーバー上のプロジェクトディレクトリに配置します。
    - **内容**：
        以下のコードを基に、ユーザー情報を取得し、新しいユーザーを作成するAPIを作成します。
        ~~~python
        from fastapi import FastAPI, HTTPException
        from pydantic import BaseModel
        from typing import List

        app = FastAPI()

        # ユーザーデータを保持するための一時的なリスト（データベースの代わり）
        fake_users_db = [
            {"id": "1", "name": "太郎"},
            {"id": "2", "name": "次郎"},
            {"id": "3", "name": "三郎"}
        ]

        # Pydanticモデル
        class User(BaseModel):
            id: str
            name: str

        @app.get("/users/{id}", response_model=User)
        async def get_user_by_id(id: str):
            # ユーザーIDで検索
            for user in fake_users_db:
                if user.id == id:
                    return user
            raise HTTPException(status_code=404, detail="User not found")

        @app.post("/users", response_model=User)
        async def create_user(user: User):
            # ユーザーIDの重複チェック
            for existing_user in fake_users_db:
                if existing_user.id == user.id:
                    raise HTTPException(status_code=400, detail="User already exists")
            # 新しいユーザーを追加
            fake_users_db.append(user)
            return user
        ~~~
    - **説明**：
        - `fake_users_db`：
            ユーザーデータを一時的に格納するリスト。データベースを使用する場合は、この部分をデータベース操作に置き換えます。
        - `User`**クラス**：
            `id`と`name`を持つPydanticのデータモデルです。`BaseModel`を継承してデータのバリデーションとシリアライゼーションを行います。
        - `get_user_by_id`**メソッド**：
            GETリクエストを受け取り、指定されたユーザーIDに基づいてユーザー情報を返します。見つからなければ404エラーを返します。
        - `create_user`**メソッド**：
            POSTリクエストで新しいユーザーを作成し、リストに追加します。同じIDを持つユーザーが既に存在する場合は、400エラーを返します。
## 7. Androidアプリにインターネットアクセスのパーミッションを設定
1. **`AndroidManifest.xml`にインターネットアクセスのパーミッションを追加する**
    - **ファイル**：`AndroidManifest.xml`を開く
    - **ディレクトリ**：`app/src/main/AndroidManifest.xml`
    - **インターネットパーミッションの追加**
        `<application>`タグの外側（上部）に、以下のパーミッションを追加します。
        ~~~xml
        <uses-permission android:name="android.permission.INTERNET" />
        ~~~
        AndroidManifest.xmlの構造は以下のようになります。
        ~~~xml
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
            package="com.example.connect">

            <!-- インターネットアクセスのパーミッションを追加 -->
            <uses-permission android:name="android.permission.INTERNET" />

            <application
                android:allowBackup="true"
                android:icon="@mipmap/ic_launcher"
                android:label="@string/app_name"
                android:roundIcon="@mipmap/ic_launcher_round"
                android:supportsRtl="true"
                android:theme="@style/Theme.AppCompat.DayNight">
                <activity android:name=".MainActivity">
                    <intent-filter>
                        <action android:name="android.intent.action.MAIN" />
                        <category android:name="android.intent.category.LAUNCHER" />
                    </intent-filter>
                </activity>
            </application>

        </manifest>
        ~~~
1. **アプリを再ビルド・再実行**

    インターネットパーミッションを追加したら、アプリを再ビルドして実行してください。これでインターネットアクセスが許可され、APIリクエストが正常に行えるようになるはずです。

    1. **メニューから再ビルドを実行**:

        Android Studioの上部メニューにある「**Build**」メニューをクリックし、次に「**Rebuild Project**」を選択します。

        - **Build** > **Rebuild Project** を選択することで、プロジェクトのすべてのコンパイル対象をクリーンにビルドし直します。
    1. **アプリの再実行（Runアプリ）**

        Android Studioの上部ツールバーにある緑色の「Run」ボタン（再生アイコンの形をしているボタン）をクリックします。

---
---
### 注意点

1. **Logcatの文字化け**

    一旦デバッグ用のコードに書き換える。
    ~~~java
    try {
        Log.d("MainActivity", "ユーザー名: " + new String(user.getName().getBytes("UTF-8"), "UTF-8"));
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }
    ~~~

---
---

## 初回ログイン以降ログインなしにする

Androidアプリで一度ログインを済ませた後、次回以降ログインの必要がないようにするためには、通常「`JWT（JSON Web Token）`」や「`リフレッシュトークン`」などの仕組みを使います。以下にその一般的な流れと実装方法を説明します。

### 1. JWTを使った認証の流れ

`JWT`は、ユーザーがログインした際にサーバー（FastAPI）が発行するトークンです。このトークンをAndroidアプリ内で保存しておくことで、次回以降のリクエストにこのトークンを付けることで、再度ログインをしなくてもユーザー認証が行われます。

基本的な流れは以下の通りです。
1. ログイン処理
    -  Androidアプリからユーザー名やパスワードを入力し、FastAPIのログインエンドポイントにリクエストを送ります。
    
    - FastAPIは認証情報が正しければ、JWTトークンを発行してアプリに返します。

1. トークンの保存
    - Androidアプリは、取得したJWTトークンを安全な場所に保存します（通常は`SharedPreferences`や暗号化ストレージに保存します）。

1. APIリクエストにトークンを付加
    - 次回以降、ユーザーがアプリを起動する際に、保存されたトークンを取得し、Retrofitを使用してサーバーにリクエストを送る際にヘッダーにトークンを付加します。

    - サーバー側は、このトークンを検証し、有効であれば認証済みのユーザーとして扱います。

1. トークンの有効期限管理
    - JWTトークンには有効期限があります。有効期限が切れた場合、リフレッシュトークンを使って新しいJWTトークンを発行することで、再ログインを防ぎます。

### 2. FastAPIサーバー側の設定

FastAPIでは、`fastapi-jwt-auth`や`pyjwt`などのライブラリを使ってJWTを扱います。簡単なFastAPIの実装例を以下に示します。

`dependencies.py`（JWTの生成と検証）

```python
from fastapi import Depends, HTTPException
from fastapi.security import OAuth2PasswordBearer
from jose import JWTError, jwt
from datetime import datetime, timedelta

SECRET_KEY = "your_secret_key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt

async def get_current_user(token: str = Depends(oauth2_scheme)):
    credentials_exception = HTTPException(
        status_code=HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
    except JWTError:
        raise credentials_exception
    return username

```

`main.py`（ログインと認証エンドポイント）
```python
from fastapi import FastAPI, Depends
from pydantic import BaseModel
from dependencies import create_access_token, get_current_user

app = FastAPI()

class LoginData(BaseModel):
    username: str
    password: str

@app.post("/token")
async def login(data: LoginData):
    # ここでユーザーの認証を行い、成功すればトークンを発行
    if data.username == "test" and data.password == "test":  # ダミー認証
        access_token = create_access_token(data={"sub": data.username})
        return {"access_token": access_token, "token_type": "bearer"}
    return {"error": "Invalid credentials"}

@app.get("/users/me")
async def read_users_me(current_user: str = Depends(get_current_user)):
    return {"username": current_user}

```

### 3. Android側の実装

**Retrofitの設定**

Retrofitを使ってFastAPIサーバーと通信する際に、JWTトークンをヘッダーに含める必要があります。以下はその設定方法です。

1. JWTトークンを保存・取得 `SharedPreferences`を使用してトークンを保存します。

    ```java
    SharedPreferences sharedPref = getSharedPreferences("myPrefs", Context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sharedPref.edit();
    editor.putString("JWT_TOKEN", token);
    editor.apply();
    ```

    保存したトークンを取得する際は以下のようにします。

    ```java
    String token = sharedPref.getString("JWT_TOKEN", null);
    ```

1. Retrofitの`Interceptor`でトークンを付加

Retrofitでリクエストにトークンを自動的に付けるために、`Interceptor`を使用します。

    ```java
    public class AuthInterceptor implements Interceptor {
        private String token;

        public AuthInterceptor(String token) {
            this.token = token;
        }

        @Override
        public Response intercept(Chain chain) throws IOException {
            Request newRequest = chain.request().newBuilder()
                .addHeader("Authorization", "Bearer " + token)
                .build();
            return chain.proceed(newRequest);
        }
    }

    ```

    Retrofitのインスタンス作成時にこのInterceptorを追加します。

    ```java
    OkHttpClient client = new OkHttpClient.Builder()
        .addInterceptor(new AuthInterceptor(token))
        .build();

    Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("https://your-api-url.com/")
        .client(client)
        .addConverterFactory(GsonConverterFactory.create())
        .build();

    ```

### 4. リフレッシュトークンの実装

JWTトークンが有効期限切れになった場合、自動的にリフレッシュトークンを使って新しいトークンを取得することができます。これには、リフレッシュトークン用のAPIエンドポイントをFastAPI側で実装し、Android側で期限切れを検知したらリフレッシュトークンを使って再ログインします。

---
---