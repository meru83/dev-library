# JavaScriptからFastAPIをコール

下記ソースコードの`async function get_web_t_auth_me(token)`関数が参考になるかもです。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
<script>

// =========================================
// web/t_auth/login に対してPOSTする処理
// =========================================
async function post_web_t_auth_login() {
    const url = 'https://971cm.com/web/t_auth/login';
    
    const data = {
        teacherId: "teacher",
        password: "Password"
    };

    try {
        const response = await fetch(url, {
            method: 'POST',
            headers: {
                'Accept': 'application/json',
                'Content-Type': 'application/json'
            },
            body: JSON.stringify(data)
        });

        if (!response.ok) {
            throw new Error('Network response was not ok');
        }

        const result = await response.json();

        // ローカルストレージにトークンを保存
        localStorage.setItem('accessToken', result.accessToken);

    } catch (error) {
        console.error('Error:', error);
    }
}

// =========================================
// web/t_auth/me に対してGETする処理
// =========================================
async function get_web_t_auth_me(token) {
    const url = "https://971cm.com/web/t_auth/me";
    try {
        // ★恐らくこの辺りのデータ送信方法が重要かもです。
        const response = await fetch(url, {
            method: "GET",
            headers: {
                "Content-Type": "application/json",
                "Authorization": `Bearer ${token}`
            }
        });
        
        if (!response.ok) {
            throw new Error("Network response was not OK");
        }
        const data = await response.json();
        console.log('=== get_web_t_auth_me() ===');
        console.log(data);
    } catch (error) {
        console.error("There was a problem with your fetch request:", error);
    }
}

// ログイン実行
post_web_t_auth_login();


// ローカルストレージからトークンを取得
const token = localStorage.getItem('accessToken');
get_web_t_auth_me(token);

</script>

</body>
</html>
```