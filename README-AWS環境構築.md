# AWSでの環境構築手順

ドメインは、`room202.online`を使う事を前提にしている。

別のドメインを使う場合は、適宜読み替えてください。

## Route53とお名前.comで独自ドメインの設定

独自ドメインを使用したい場合は、本設定を行う

※この項目を設定すると課金が発生するので注意

### 流れ

1. お名前.comで無料ドメインを取得
2. Route53でホストゾーンを作成
3. `2.`で作成されたネームサーバーを、お名前.comのネームサーバーに登録する
4. ElasticIPを使用していない場合は、Route53に`Aレコード`を追加する

### 1. お名前.comで無料ドメインを取得

１年間無料のドメインもある

- 注意点
    - 事前にクレジットカードの登録が必要になる
    - お名前.com側でドメインの自動更新を有効にしていると次年度課金される

### 2. Route53でホストゾーンを作成

1. AWSの`Route53`から`開始する`を選択する
2. `ドメインを登録`を選択して`開始する`を選択する
3. `Route53 ダッシュボード`へ飛び`ホストゾーンの作成`を選択する
4. `ドメイン名`に取得したドメイン名を入力する
5. `タイプ`が`パブリックホストゾーン`である事を確認して`ホストゾーンの作成`を選択する
6. 作成されたレコードのタイプが`NS`の値を後で使用する

### 3. `2.`で作成されたネームサーバーを、お名前.comのネームサーバーに登録する

#### AWS側

1. 作成されたレコードのタイプが`NS`の値を後を確認する

#### お名前.com

1. AWS側の`NS`の文字列をお名前.comに設定する
2. `ネームサーバー設定`の`その他のネームサーバーを使う`に`1.`の内容を入力する

### 4. ElasticIPを使用していない場合は、Route53に`Aレコード`を追加する

EC2のインスタンスを起動するたびにIPが変わるので、その都度この操作を実行する

- 初回
    - `レコードを作成`から`レコードタイプ`で`A`レコードを追加する
        - 値に現在のIPアドレスを入力する（他はデフォルトのまま）
    - `レコードを作成`を選択する
- ２回目以降
    - AレコードのIPアドレスを現在のIPアドレスに更新する

## EC2の設定

AWSのサービスから`EC2`を選択する

`インスタンスを起動`からEC2インスタンスを新規作成する

### 設定内容

- 名前とタグ
    - `名前`は、好きな名前を設定
- アプリケーションおよび OS イメージ (Amazon マシンイメージ) 
    - `Amazon Linux 2023 AMI`を選択
- インスタンスタイプ
    - `t2.micro`を選択
- キーペア(ログイン)
    - `新しいキーペアの作成`を選択
        - `キーペア名`は、好きな名前を設定
        - その他の設定はそのままで`キーペアを作成`を選択
        - ※キーファイルのダウンロードが始まるので、このキーファイルは大事に保管する
- ネットワーク設定
    - `編集`から`インバウンドセキュリティグループのルール`を設定する
    - タイプ
        - カスタムTCP
    - プロトコル
        - TCP
    - ポート範囲
        - 22 (ssh) ※デフォルトで設定済み
        - 80 (http)
        - 443 (https)
        - 3000 (Nodejs)
        - 3306 (MariaDB)
        - 8000 (uvicorn)
    - ソースタイプ
        - カスタム
    - ソース
        - 0.0.0.0/0
- ストレージを設定
    - 変更なし
- 高度な詳細
    - 変更なし

以上の設定ができたら`インスタンスを起動`を選択

## TeraTermからEC2インスタンスにSSH接続

ダウンロードしたキーファイルを使ってEC2インスタンスにSSHログイン

```
接続先 : EC2のパブリック IPv4 アドレス (もしくはElastic IP アドレス)
ユーザー名 : ec2-user
パスワード : ダウンロードしたキーファイル
```

## Apacheのインストールと設定

```bash
# Apacheのインストール
sudo dnf -y install httpd

# Apache起動
sudo systemctl start httpd.service

# Apacheの自動起動
sudo systemctl enable httpd.service
```

ブラウザでEC2のIPアドレスにhttpでアクセスすると「It works!」と表示される

```bash
# IPの部分は各自のEC2インスタンスのパブリックIPに変更すること
http://13.208.173.25/
```

## https化

参考URL

https://owani.net/lets-encrypt-ec2/

### Let's Encrypt 初回

「Let’s Encrypt」を使用するためにcertbotコマンドをインストールします。

certbotコマンドは、Let’s Encrypt CA（認証局）から無料のSSL/TLS証明書を取得し、インストールし、更新するためのツールです。

```bash
# 必要なパッケージをインストール
sudo dnf install -y python3 augeas-libs pip
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip

# certbotコマンドをインストール
sudo /opt/certbot/bin/pip install certbot

# certbotコマンドを直接実行できるようにシンボリックリンクの作成
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot

# certbotのバージョン確認
certbot --version

# Apache停止
sudo systemctl stop httpd.service

# 証明書の発行
sudo certbot certonly --standalone

# メールアドレスを入力する
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices)
(Enter 'c' to cancel): ここにメールアドレスを入力する

# Let’s Encryptの利用規約に同意しますか？
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.3-September-21-2022.pdf. You must
agree in order to register with the ACME server. Do you agree?
 
(Y)es/(N)o: Y

# Let’s EncryptプロジェクトとCertbotの開発を支援している非営利団体にメールアドレスを共有しますか？（今回私は不要なのでN）
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
 
(Y)es/(N)o: N

# ドメインを入力してください。
Account registered.
Please enter the domain name(s) you would like on your certificate (comma and/or
space separated) (Enter 'c' to cancel):room202.online
```

### Let's Encrypt 更新

```bash
# Apacheを止める
sudo service httpd stop

# 更新できるか一度確認する
# Congratulations, all simulated renewals succeeded: と表示されれば問題ない
sudo certbot renew --dry-run

# 更新
# Congratulations, all renewals succeeded: と表示されたら更新完了
sudo certbot renew

# Apacheを起動
sudo service httpd start
```

### SSLの設定とリバースプロキシの設定

```bash
# SSLモジュールのインストール
sudo yum install mod_ssl -y
```

Apacheの設定ファイルを新規作成します。

ファイル名は、`ドメイン名.conf`です。

例：`room202.online.conf`

```bash
sudo vi /etc/httpd/conf.d/room202.online.conf
```

記載内容

```bash
<VirtualHost *:443>
    ServerName room202.online

    DocumentRoot /var/www/html
    SSLEngine on
 
    <Files ~ "\.(cgi|shtml|phtml|php3?)$">
        SSLOptions +StdEnvVars
    </Files>
    <Directory "/var/www/cgi-bin">
        SSLOptions +StdEnvVars
    </Directory>
    <Directory "/var/www/html/">
        AllowOverride All
    </Directory>

    SSLCertificateFile /etc/letsencrypt/live/room202.online/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/room202.online/privkey.pem

    # リバースプロキシの設定
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/
</VirtualHost>
```

```bash
# Apacheを再起動して設定を反映させる
sudo systemctl restart httpd
```

## MariaDBのインストールと設定

```bash
# MariaDBのインストール
sudo dnf -y install mariadb105-server

# MariaDBの起動
sudo systemctl start mariadb

# MariaDBの自動起動
sudo systemctl enable mariadb

# MariaDBの初期設定
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

### MariaDBにrootでログイン

```bash
mysql -u root -p
```

### データベース作成

- データベース名 : SaVasity

```bash
CREATE DATABASE SaVasity DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

### ユーザー作成

`SaVasity`データベースに対して下記ユーザーを作成

- ユーザー名 : savasity
- パスワード : Savasity007

```bash
# ユーザーの新規作成
GRANT ALL ON SaVasity.* TO savasity@"%" IDENTIFIED BY 'Savasity007';
FLUSH PRIVILEGES;
exit;

# 新規作成したユーザーでログインできたか確認
mysql -u savasity -p
```

### テーブル作成

- サンプルテーブル作成

- サンプルデータ追加

### データベースの設定（その他）

扱うデータのMaxサイズを変更

```sql
SET @@global.max_allowed_packet = 100 * 1048576; -- 100MB
```

### MariaDBが外部接続を許可しているか確認

```bash
sudo vi /etc/my.cnf.d/mariadb-server.cnf
```

bind-address の設定を探して、次のようになっているか確認します。

※コメントアウトされていたらコメントアウトを外す。

```bash
bind-address = 0.0.0.0
```

### 変更を保存してMariaDBを再起動

```bash
sudo systemctl restart mariadb
```

## ファイルの許可を設定する

```bash
# ユーザーをapacheグループに追加します
sudo usermod -a -G apache ec2-user

# 一旦ログアウト
exit

# 再度TeraTermで接続

# ec2-userの所属グループにapacheが入っている事を確認
groups

# /var/wwwとそのコンテンツのグループ所有権をapache グループに変更
sudo chown -R ec2-user:apache /var/www

# グループの書き込み許可を追加して、これからのサブディレクトにグループ ID を設定するには、/var/www とサブディレクトのディレクトリ許可を変更
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;

# グループ書き込み許可を追加するには、/var/www とサブディレクトリのファイル許可を再帰的に変更します
find /var/www -type f -exec sudo chmod 0664 {} \;

```


## PHPのインストール

```bash
# 必要なパッケージをインストール
# 今回はPHP Version8.2系をインストール
sudo dnf -y install php8.2
sudo dnf -y install php8.2-mbstring php-mysqli
sudo dnf -y install wget php-fpm php-json php-devel php-xml

# ディレクトリの所有権変更
sudo chown apache:apache /var/www/html -R

# Apache再起動
sudo systemctl restart httpd
```

## phpMyAdminのインストール

```bash
# Apacheを再起動
sudo systemctl restart httpd

# php-fpmを再起動
sudo systemctl restart php-fpm
```

下記サイトからインストールしたいphpMyAdminのURLを取得する

https://www.phpmyadmin.net/downloads/

```bash
# インストール先に移動
cd /var/www/html

# 最新版
wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz
# or
# 特定のバージョン
wget https://files.phpmyadmin.net/phpMyAdmin/5.2.1/phpMyAdmin-5.2.1-all-languages.tar.gz

# phpMyAdminフォルダを作成し、次のコマンドでパッケージを展開
mkdir phpMyAdmin && tar -xvzf phpMyAdmin-latest-all-languages.tar.gz -C phpMyAdmin --strip-components 1

# ダウンロードしたファイルを削除
rm phpMyAdmin-latest-all-languages.tar.gz
```

下記サイトへアクセスして、MariaDBのアカウントでログイン

http://room202.online/phpMyAdmin


### 警告がでる場合

`クッキーの暗号化のために、構成ファイルに有効なキーが必要です。一時的なキーが自動的に作成されました。`

- phpMyAdminディレクトリにある`config.sample.inc.php`を複製して`config.inc.php`を用意する
- 複製した`config.inc.php`を編集する

```bash
vi config.inc.php
```

変更前

```
 12 /**
 13  * This is needed for cookie based authentication to encrypt the cookie.
 14  * Needs to be a 32-bytes long string of random bytes. See FAQ 2.10.
 15  */
 16 $cfg['blowfish_secret'] = ''; /* YOU MUST FILL IN THIS FOR COOKIE AUTH! */
```

変更後

※32バイトのランダムな文字列を指定します。

```bash
# 32バイトのランダムな文字列を生成するコマンド
openssl rand -base64 24
```

```
 12 /**
 13  * This is needed for cookie based authentication to encrypt the cookie.
 14  * Needs to be a 32-bytes long string of random bytes. See FAQ 2.10.
 15  */
 16 $cfg['blowfish_secret'] = 'r3yPJj8l/8i2c/pEcuBgtYWKiiHa4bmS'; /* YOU MUST FILL IN THIS FOR COOKIE AUTH! */
```

`$cfg['TempDir'] (/var/www/html/phpMyAdmin/tmp/) にアクセスできません。phpMyAdmin はテンプレートをキャッシュすることができないため、低速になります。`

これはそもそもtmpディレクトリが無いかアクセス権限が無いからです。

そこで、ディレクトリを作成しアクセス権限を変更します。

```bash
mkdir /var/www/html/phpMyAdmin/tmp
chmod 774 /var/www/html/phpMyAdmin/tmp
```

`phpMyAdmin 環境保管領域が完全に設定されていないため、いくつかの拡張機能が無効になっています。理由についてはこちらをご覧ください。 代わりにデータベースの操作タブを使って設定することもできます。`

参考URL

https://qiita.com/Ryo-0131/items/c5ee34f1075dd3be7475

- 管理者権限（root）ユーザーでphpmyadminにログインします。
- `理由についてはこちらをご覧下さい。`の`こちら`をクリックします。
- `「phpmyadmin」データベースを作成し、phpMyAdmin 環境保管領域をセットアップ。`の`作成`をクリックします。
    - 左ペインをみると phpmyadminデータベースが作成されたことがわかります。
- config.inc.php編集します。

変更前

```bash
/* Storage database and tables */
// $cfg['Servers'][$i]['pmadb'] = 'phpmyadmin';
// $cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
// $cfg['Servers'][$i]['relation'] = 'pma__relation';
// $cfg['Servers'][$i]['table_info'] = 'pma__table_info';
// $cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
// $cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
// $cfg['Servers'][$i]['column_info'] = 'pma__column_info';
// $cfg['Servers'][$i]['history'] = 'pma__history';
// $cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
// $cfg['Servers'][$i]['tracking'] = 'pma__tracking';
// $cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
// $cfg['Servers'][$i]['recent'] = 'pma__recent';
// $cfg['Servers'][$i]['favorite'] = 'pma__favorite';
// $cfg['Servers'][$i]['users'] = 'pma__users';
// $cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
// $cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
// $cfg['Servers'][$i]['savedsearches'] = 'pma__savedsearches';
// $cfg['Servers'][$i]['central_columns'] = 'pma__central_columns';
// $cfg['Servers'][$i]['designer_settings'] = 'pma__designer_settings';
// $cfg['Servers'][$i]['export_templates'] = 'pma__export_templates';
```

変更後

`//`のコメントアウトを消して有効化する

```bash
/* Storage database and tables */
$cfg['Servers'][$i]['pmadb'] = 'phpmyadmin';
$cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
$cfg['Servers'][$i]['relation'] = 'pma__relation';
$cfg['Servers'][$i]['table_info'] = 'pma__table_info';
$cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
$cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
$cfg['Servers'][$i]['column_info'] = 'pma__column_info';
$cfg['Servers'][$i]['history'] = 'pma__history';
$cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
$cfg['Servers'][$i]['tracking'] = 'pma__tracking';
$cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
$cfg['Servers'][$i]['recent'] = 'pma__recent';
$cfg['Servers'][$i]['favorite'] = 'pma__favorite';
$cfg['Servers'][$i]['users'] = 'pma__users';
$cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
$cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
$cfg['Servers'][$i]['savedsearches'] = 'pma__savedsearches';
$cfg['Servers'][$i]['central_columns'] = 'pma__central_columns';
$cfg['Servers'][$i]['designer_settings'] = 'pma__designer_settings';
$cfg['Servers'][$i]['export_templates'] = 'pma__export_templates';
```




## FastAPIのインストールと設定

### https化

uvicornのhttps化

## nodejsのインストールと設定

### https化

nodejsのhttps化