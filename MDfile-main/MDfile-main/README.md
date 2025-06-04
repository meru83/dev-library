# README

## おねがい

あとから参戦する人も同じ環境が作れるように環境構築のやり方覚えとく。

特にインストールしたライブラリなどはテキストファイルなどにまとめとく。

プラットフォームなどダウンロードしたものはバージョンまでしっかり覚えておく。

難しい技術こそ技術書かいて自分も再現不可能ってならないようにする。  


## みんなへ

### 曜日のデータベース内での扱い方

曜日は数字として扱います。
プログラム内でも

- 月曜：１
- 火曜：２
- 水曜：３
- 木曜：４
- 金曜：５
- 土曜：６
- 日曜：7

### `uvicorn`を強制終了させる方法

`PID`を取得
```bash
# UNIX系のOS（LinuxやmacOS）
# amazonLinux(今回の場合TeraTerm)はこっち
ps aux | grep uvicorn

# Windows
tasklist | findstr uvicorn
```

以下を実際の`PID`に置き換えて実行

```bash
# UNIX系のOS（LinuxやmacOS）
kill <PID>

# windows
taskkill /F /PID <PID>
```

### aws上のファイルをPCのローカルフォルダに保存するコマンド

PC上のコマンドプロンプロなどを開き下記コマンドを実装
```bash
# お手本
scp -i /path/to/your-key.pem ec2-user@your-ec2-public-ip:/path/to/remote-file /local/path/to/save
```

`/path/to/your-key.pem` 秘密鍵の保存場所。（G-mailに鍵zipがあるからそれを展開して保存）
`your-ec2-public-ip` IPアドレスかもしくは今回の場合971cm.com
`/path/to/remote-file` 保存したいファイルの場所（/home/ec2-user/971cm.sql にSQLファイルが保存されている）
`/local/path/to/save` 保存したいPC上の場所

```bash
scp -i C:\Users\rion_\.ssh\971cm-public-key.pem ec2-user@971cm.com:/home/ec2-user/971cm.sql C:\Users\rion
_\OneDrive\デスクトップ\sql
```


### そもそもaws内のデータベースをエクスポートする方法

aws内にsqlファイルをインポートする方法
以下を実行するとFastAPIのファイルが存在する場所にsqlファイルが入ってくる

```bash
sh db_export.sh
```


これ以下はもう関係ない

```bash
mysqldump -u your_username -p your_database_name > /path/to/your_exported_file.sql
```

`your_username` DBのユーザー名(rootがおすすめ)
`your_database_name` DBの名前
`/path/to/your_exported_file.sql` 保存先とファイル名
