# React(Java Script) 技術メモ

## 目次

- [fetchリクエストでトークンを含めるリクエストを送る方法](#fetchリクエストでトークンを含めるリクエストを送る方法)
    - [トークンの保存と再利用方法](#トークンの保存と再利用方法)
    - [基本的なfetchでトークンを含むリクエストを送信する方法](#基本的なfetchでトークンを含むリクエストを送信する方法)

## `fetch`リクエストでトークンを含めるリクエストを送る方法

### トークンの保存と再利用方法

トークンは、通常、ユーザーがログインしたときに受け取り、その後のリクエストに再利用します。例えば、Reactで`localStorage`や`sessionStorage`にトークンを保存し、後のリクエストでそのトークンを取り出して使うことができます。

例：`localStorage`にトークンを保存し、リクエストに使用する。

~~~JS
// トークンを保存する
localStorage.setItem("authToken", "your-token-here");

// トークンを取得してリクエストに使用する
const fetchDataWithStoredToken = async () => {
    const token = localStorage.getItem("authToken");  // 保存したトークンを取得

    try {
        const response = await fetch("https://api.example.com/data", {
            method: "GET",
            headers: {
                "Content-Type": "application/json",
                "Authorization": `Bearer ${token}`
            }
        });

        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        console.log(data);
    } catch (error) {
        console.error("Error fetching data:", error);
    }
};

useEffect(() => {
    fetchDataWithStoredToken();
}, []);

~~~

ポイント解説：

- `localStorage`：`localStorage`を使って、ブラウザにトークンを保存しています。トークンは、後でリクエストに再利用できます。

- トークンの取り出し：`localStorage.getItem("authToken")`を使って、保存されたトークンを取り出し、リクエストに使用しています。

### 基本的なfetchでトークンを含むリクエストを送信する方法

トークンは、一般的にHTTPリクエストのAuthorizationヘッダーに含めて送信することが多いです。以下にその手順とコード例を詳しく解説します。

1. Authorizationヘッダーにトークンを含めて送信する

例：

~~~JS
const fetchData = async () => {
    const token = "your-token-here";  // トークンを設定

    try {
        const response = await fetch("https://api.example.com/data", {
            method: "GET",  // GETリクエスト
            headers: {
                "Content-Type": "application/json",  // 必要に応じてヘッダーを追加
                "Authorization": `Bearer ${token}`   // トークンをヘッダーに含める
            }
        });

        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();  // JSONデータの取得
        console.log(data);  // データを表示
    } catch (error) {
        console.error("Error fetching data:", error);
    }
};

// コンポーネントのレンダリング時にデータを取得する
useEffect(() => {
    fetchData();
}, []);
~~~

ポイント解説：

- `Authorization`ヘッダー: `Authorization`ヘッダーに`Bearer`トークンを含めてサーバに送信します。これは認証に使われる形式で、トークンは変数`token`に格納されています。

- `useEffect`：Reactの`useEffect`を使って、コンポーネントがマウントされたときにデータを取得します。このように非同期処理を行うことで、APIからのデータを取得します。

※ `POST` の場合も同じ

2. トークンをクエリパラメータとして送信する場合

クエリパラメータにトークンを含めて送信することも可能です。この場合、URLにトークンを直接含めます。

例：

~~~JS
const fetchWithQueryToken = async () => {
    const token = "your-token-here";  // トークンを設定
    const url = `https://api.example.com/data?token=${token}`;  // URLにトークンを含める

    try {
        const response = await fetch(url, {
            method: "GET",  // GETリクエスト
            headers: {
                "Content-Type": "application/json"
            }
        });

        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();  // JSONデータの取得
        console.log(data);  // データを表示
    } catch (error) {
        console.error("Error fetching data:", error);
    }
};

// コンポーネントのレンダリング時にクエリトークンを使ってデータを取得する
useEffect(() => {
    fetchWithQueryToken();
}, []);
~~~

ポイント解説：

- クエリパラメータにトークンを含める：URL自体にクエリパラメータとしてトークンを追加しています。(`?token=your-token-here`)

- トークンをヘッダーに含めない場合、クエリパラメータでトークンを渡すため、`Authorization`ヘッダーは必要ありません。

---
---

## `Navigate`

`Navigate`コンポーネントは、React Routerでページをリダイレクトするために使われるコンポーネントです。React Router v6で導入され、以前のバージョンでの`<Redirect>`コンポーネントに相当します。リダイレクトの条件や遷移先を動的に設定でき、特定の条件でユーザーを別のページに移動させる用途でよく使われます。

### 基本的な使い方

```jsx
import { Navigate } from 'react-router-dom';

function ExampleComponent() {
  const isAuthenticated = false; // 認証状態の例

  if (!isAuthenticated) {
    return <Navigate to="/login" />;
  }

  return <div>Welcome to the dashboard!</div>;
}
```

上記の例では、`isAuthenticated`が`false`の場合、ユーザーが`/login`にリダイレクトされます。`Navigate`コンポーネントを使うと、条件付きでルートを変更し、ユーザーを指定のパスに移動できます。

### `Navigate`コンポーネントの主なプロパティ

- **`to`**: リダイレクト先のURLを指定します。必須プロパティです。
- **`replace`**: `true`に設定すると、現在のURLを履歴から置き換え、戻るボタンで元のページに戻れないようにします。

### 例: ログイン時のリダイレクト

ログイン後にダッシュボードにリダイレクトする場合などにも便利です。

```jsx
<Route path="/login" element={isAuthenticated ? <Navigate to="/dashboard" replace /> : <LoginPage />} />
```

この例では、`isAuthenticated`が`true`のときに`/dashboard`にリダイレクトし、認証されていない場合はログインページを表示します。