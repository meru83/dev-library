# VS Code + Remote SSHの具体的なセットアップ手順

VS Codeを使ってリモートサーバーに接続するために必要な、Remote Development 拡張機能をインストールします。

VS Codeのコマンドパレットを開く: Ctrl + Shift + P（またはCmd + Shift + P）を押してコマンドパレットを開きます。

「Remote-SSH: Connect to Host...」を選択します。

「SSHホストを構成する」を選択します。

~/.ssh/config ファイルに接続情報と一緒にSSHキーを設定します。

Host my-ec2-server
    HostName YOUR_EC2_PUBLIC_IP
    User ec2-user
    IdentityFile /path/to/your-key.pem
