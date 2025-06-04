# Windowsの機能の有効化

・Windowsスタートボタンの右横にある検索バーで「Windowsの機能」と検索する
・検索にヒットした「Windowsの機能の有効化または無効化」をクリックして起動する

・「Linux用windowsサブシステム」
・「仮想マシンプラットフォーム」

## x64マシン用WSL2 Linuxカーネル更新プログラムパッケージのダウンロードとインストール
・https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

## Docker環境構築

・Dockerのダウンロード

 https://www.docker.com/products/docker-desktop

- WSL2（Windows Subsystem for Linux 2）のインストール

1. PowerShellを管理者権限で開く
2. WSLを有効化

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

3. 仮想マシンプラットフォームの有効化  
※WSL2を使用するためには、Windowsの「仮想マシンプラットフォーム」も有効にする必要があります。  

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

4. 仮想マシンプラットフォームを有効化した後、PCを再起動します。

- WSL2を既定のバージョンに設定

1. PowerShellを開く
2. wsl --set-default-version 2

- Linuxディストリビューションのインストール

1. Microsoft Storeを開く
2. Microsoft Storeの検索バーに Ubuntu と入力し、Ubuntuを選択します。
3. Ubuntuのインストール
4. 初回起動時には、ユーザー名とパスワードの設定が求められます。これはUbuntu内で使用するLinuxユーザーです。

- WSL2にUbuntuを設定する

1. PowerShellで実行し、UbuntuがWSL2で動作しているか確認します。

```
wsl -l -v
```

※Ubuntu のバージョンが 2 になっていればWSL2で動作しています。

- WSL2バックエンドを有効にする

1. Docker Desktopを起動し、設定メニューを開く
2. 設定画面で、左側のメニューから 「General」 を選択します。
3. 「Use the WSL 2 based engine」（WSL2をバックエンドとして使用）のオプションにチェックが入っていることを確認します。このオプションにチェックが入っていない場合、チェックを入れてください。

- WSLとの統合設定

～～WSL上で動作するLinuxディストリビューション（例えばUbuntu）をDockerと連携させる設定を行います。

1. WSL上で動作するLinuxディストリビューション（例えばUbuntu）をDockerと連携させる設定を行います。

2. ここでは、WSL2にインストールしたUbuntuなどのディストリビューションが表示されているはずです。UbuntuのスイッチをONにして、WSL2ディストリビューションでDockerが使用できるようにします。

3. 変更後は、再度 「Apply & Restart」 をクリックして、設定を反映させます。

- WSL2環境でのDocker動作確認
1. スタートメニューから 「Ubuntu」 を起動します。
2. WSL2上でDockerが動作しているかを確認します。
```
docker --version
```
3. Dockerが hello-world コンテナをダウンロードして実行します。正常に出力メッセージが表示されれば、WSL2上でDockerが問題なく動作していることが確認できます。

```
docker run hello-world
```

- Reactアプリケーションの作成

※まず、Node.jsがインストールされていることを確認してください。
Node.jsがインストールされていない場合は、Node.jsの公式サイトからインストールします。
https://nodejs.org/en/

1. コマンドプロンプトを開いてプロジェクトを作成したいディレクトリにcdで移動します。

2. my-react-app というディレクトリにReactアプリケーションが作成されます。このアプリケーションをDockerで実行します。

```
npx create-react-app my-react-app
cd my-react-app
```

- Dockerfileの作成

～～DockerでReactアプリを実行するための Dockerfile を作成します。Node.jsをDocker内で動かし、Reactの開発サーバーを実行します。～～

1. my-react-appの中にDockerfile を作成
```
touch Dockerfile
```

2. Dockerfile の内容を設定

```
# ベースイメージとしてNode.jsを使用
FROM node:20.17.0-alpine

# 作業ディレクトリを設定
WORKDIR /app

# パッケージ情報をコピーして依存関係をインストール
COPY package*.json ./
RUN npm install

# ソースコードをコピー
COPY . .

# 開発サーバーを起動
CMD ["npm", "start"]

# 開発サーバー用にポート3000を公開
EXPOSE 3000
```

3. Dockerイメージのビルド

※プロジェクトディレクトリ内で実行

```
docker build -t my-react-app .
```

→Reactアプリケーションが含まれたDockerイメージが作成されます。

4. Dockerコンテナの実行

※アプリから可能

```
docker run -it -p 3000:3000 -v ${PWD}:/app -v /app/node_modules my-react-app

・
```

docker-compose を使うと、ホットリロードのためのボリュームマウント設定やポートのマッピングを簡単に管理できます。

```
version: '3'
services:
  react-app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    stdin_open: true
    tty: true
```

2. ターミナル（WSL2またはPowerShell）で、以下のコマンドを実行してコンテナを起動します。

```
docker-compose up --build
```


- ブラウザで動作確認

http://localhost:3000/

- フロントエンド（React）でAPIリクエストを送る

1. APIリクエストのためのコード

※データを取得する処理を実装します。

ファイルApp.js

```javascript
import React, { useState, useEffect } from 'react';

function App() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // AWSのAPIエンドポイントにGETリクエストを送信
    fetch('https://971cm.com/users')
      .then((response) => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      })
      .then((data) => {
        setUsers(data);
        setLoading(false);
      })
      .catch((error) => {
        console.error('Error fetching data:', error);
        setError(error);
        setLoading(false);
      });
  }, []);

  if (loading) {
    return <p>Loading...</p>;
  }

  if (error) {
    return <p>Error: {error.message}</p>;
  }

  return (
    <div className="App">
      <h1>Users</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>
            {user.name} ({user.email})
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

2. 開発サーバーが起動していない場合は、次のコマンドでアプリケーションを実行します。

```
npm start
```

- CORS設定コード

※CORS設定コード は、FastAPI アプリケーションのサーバー側の設定です。これを FastAPIのメインアプリケーションが定義されているファイル（通常は main.py や app.py）に記述する必要があります。

main.py

以下、動作テスト用コード

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

# CORS設定
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],  # ReactアプリのURLを指定
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# 既存のルートやAPIエンドポイント
@app.get("/users")
async def get_users():
    return [
        {"id": 1, "name": "John Doe", "email": "john@example.com"},
        {"id": 2, "name": "Jane Doe", "email": "jane@example.com"}
    ]

```

- サーバーを再起動する

```
uvicorn main:app --reload
```
