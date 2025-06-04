# EC2一台クラウド作成2：Apacheのリバースプロキシ設定

## 設定ファイルを新規作成

Apacheの設定ファイルを編集

（971cm.com.conf）設定ファイル作成

```bash
$sudo nano /etc/httpd/conf.d/971cm.com.conf
```

記載内容

```bash
<VirtualHost *:443>
    ServerName 971cm.com

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/971cm.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/971cm.com/privkey.pem
    #971cm.comは自分のドメイン名

    # リバースプロキシの設定
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/
</VirtualHost>
```

## プロキシモジュールの有効化

```bash
# SSLモジュールのインストール
$sudo yum install mod_ssl -y

# Apacheを再起動して設定を反映させる
$sudo systemctl restart httpd
```

※最後にhttps://971cm.com/ にアクセスして、FastAPIのリクエストがApache経由で正しくプロキシされるか確認