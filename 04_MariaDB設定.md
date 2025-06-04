# MariaDB設定

## MariaDBのインストール

```bash
sudo dnf -y install mariadb105-server
```

## MariaDBの起動と初期設定

```bash
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo mysql_secure_installation
```

基本的に入力しないといけないのは、下記④⑤の「rootユーザーに設定するパスワード」だけて、  
他は「Enter」キーを押すだけで良いです。

```bash
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): ① [Enter]を入力
OK, successfully used password, moving on...

Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] ② [Enter]を入力
Enabled successfully!
Reloading privilege tables..
 ... Success!


You already have your root account protected, so you can safely answer 'n'.

Change the root password? [Y/n] ③ [Enter]を入力
New password: ④ rootユーザーに設定したいパスワードを入力
Re-enter new password: ⑤ ④と同じものを入力
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] ⑥ [Enter]を入力
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] ⑦ [Enter]を入力
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] ⑧ [Enter]を入力
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] ⑨ [Enter]を入力
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

## MariaDBにログイン

```bash
mysql -u root -p
```

## 「test」データベース作成

```bash
CREATE DATABASE test DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

## ユーザー作成

- ユーザー名 : camel
- パスワード : check

```bash
GRANT ALL ON test.* TO camel@"%" IDENTIFIED BY 'check';
FLUSH PRIVILEGES;
```

## MariaDBが外部接続を許可しているか確認

```bash
sudo nano /etc/my.cnf.d/mariadb-server.cnf
```

bind-address の設定を探して、次のようになっているか確認します。

※コメントアウトされていたらコメントアウトを外す。

```bash
bind-address = 0.0.0.0
```

## 変更を保存してMariaDBを再起動

```bash
sudo systemctl restart mariadb
```

## 設定の確認

権限を付与した後、再度リモートから接続を試みます。

```bash
mysql -u camel -p -h 971cm.com -P 3306
```

## タイムゾーン設定

現在の日時確認コマンド

```bash
# mariadbにログイン
mysql -u root -p

# 年月日確認
SELECT CURRENT_DATE;

# 時分秒
SELECT CURRENT_TIME;
```

`Asia/Tokyo`のタイムゾーンに次のコマンドで設定できます。

```bash
sudo timedatectl set-timezone Asia/Tokyo

# mariaDB再起動
sudo systemctl restart mariadb
```
