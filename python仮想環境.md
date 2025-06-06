# windowsアプリ用　Pythonの仮想環境　構築ガイド

## 1. Python のインストールを確認

1. **Python が既にインストールされているか確認**

    まず、Pythonが既にインストールされているか確認します。ターミナル（Mac/Linux）やコマンドプロンプト（Windows）を開き、次のコマンドを入力します。
    ~~~bash
    python --version

    #または

    python3 --version
    ~~~

1. **Python がインストールされていない場合**

    Pythonがインストールされていない場合、Pythonの公式サイトからインストールする必要があります。
    
    1. [Python](https://www.python.org/downloads/) 公式サイトにアクセスします。
    1. 最新の安定バージョンをダウンロードしてインストールします。

        >※ Windows：インストーラー実行時に「**PATHに追加**」のオプションにチェックを入れることを忘れずに。
1. **パッケージ管理ツール `pip` の確認**

    Pythonには`pip`というパッケージ管理ツールが同梱されていますが、これが動作するか確認します。次のコマンドを使って確認できます。
    ~~~bash
    pip --version

    #または

    pip3 --version
    ~~~

これで、Pythonとpipのインストールが完了していることが確認できました。もしpipがない場合は、公式ドキュメントを参考にインストールするか、Pythonの再インストールを試みてください。

## 2. 仮想環境を作成する
仮想環境を作成することで、システム全体に影響を与えず、プロジェクトごとに独立した環境を構築できます。これにより、異なるプロジェクト間で異なるバージョンのライブラリを使うことができるようになります。

1. **`venv` モジュールを使って仮想環境を作成**

    Python 3.3以降には、venvという仮想環境を作成するための標準モジュールが組み込まれています。このモジュールを使って仮想環境を作成します。

    1. **プロジェクトフォルダを作成**（任意）

        仮想環境は、通常プロジェクトごとに分けるため、まず新しいディレクトリを作成することをお勧めします。例えば、プロジェクトフォルダ「my_project」を作成します。

        ~~~bash
        mkdir my_project
        cd my_project
        ~~~
    1. **仮想環境を作成する**：
        次に、`venv`コマンドを使って仮想環境を作成します。

        **Windows**:
        ~~~bash
        python -m venv venv
        ~~~

        **Mac/Linux**：
        ~~~bash
        python3 -m venv venv
        ~~~

        `project`は仮想環境のディレクトリ名を指しており、`project`フォルダ内に仮想環境の必要なファイルが作成されます。別の名前を付けたい場合は、`project`の部分を変更することも可能です。
1. **仮想環境のディレクトリ構成**

    仮想環境が作成されると、ディレクトリ内には次のようなファイル構成が生成されます。

    ~~~makefile
    my_project/
    │
    └── venv/
        ├── bin/       # 仮想環境を有効化するためのスクリプト（Mac/Linux）
        ├── Scripts/   # 仮想環境を有効化するためのスクリプト（Windows）
        ├── include/   # C言語拡張モジュールのヘッダファイル（必要な場合）
        ├── lib/       # Pythonのライブラリを保存するディレクトリ
        └── pyvenv.cfg # 仮想環境の設定ファイル

    ~~~

    ここまでで、仮想環境の作成が完了しました。

## 3. 仮想環境を有効化する

1. **仮想環境の有効化**

    仮想環境を有効化するコマンドは、使用しているオペレーティングシステムによって異なります。

    **Windows の場合**

    1. コマンドプロンプト、PowerShell、またはWindows Terminalを開きます。
    1. 仮想環境を有効にするために、`Scripts\activate` スクリプトを実行します。
        ~~~bash
        venv\Scripts\activate
        ~~~
        成功すると、コマンドプロンプトの左側に `(venv)` という表示が追加されます。これは、仮想環境が有効化されていることを示しています。

        例：
        ~~~lua
        (venv) C:\path\to\my_project>
        ~~~
    
    **Mac/Linux の場合**

    1. ターミナルを開きます。
    1. `bin/activate` スクリプトを実行して仮想環境を有効化します。
        ~~~bash
        source venv/bin/activate
        ~~~
        これも同様に、ターミナルのプロンプトに `(venv)` という表示が追加されます。

1. **仮想環境の無効化**

    仮想環境を使い終わったら、次のコマンドで無効化できます。これにより、システムのデフォルトのPython環境に戻ります。

    ~~~bash
    deactivate
    ~~~

    無効化後、プロンプトから `(venv)` の表示が消えます。

## 4. 仮想環境内でのライブラリのインストール

仮想環境が有効化された状態で、Pythonのライブラリをインストールすると、それらは仮想環境内にインストールされ、他のプロジェクトやシステム全体には影響しません。ここでは、一般的なライブラリのインストール方法や依存関係管理のための`requirements.txt`ファイルの使い方について解説します。

1. **ライブラリのインストール**

    ライブラリのインストールには、Pythonのパッケージ管理ツール`pip`を使います。`pip`を使うことで、ライブラリを簡単にインストールできます。仮想環境が有効化されている状態で、次のコマンドを使ってライブラリをインストールできます。

    例えば、`requests`というHTTPリクエストライブラリをインストールする場合:
    ~~~bash
    pip install requests
    ~~~

    インストールが成功すると、ライブラリとその依存関係が仮想環境の`lib`ディレクトリに保存されます。

1. **インストールされているライブラリの確認**

    仮想環境にインストールされているすべてのライブラリを確認するには、次のコマンドを使用します。

    ~~~bash
    pip list
    ~~~

    これにより、仮想環境内にインストールされているパッケージの一覧と、それぞれのバージョンが表示されます。
1. **`requirements.txt` ファイルの作成と使用**

    プロジェクトで使われているライブラリを他の開発者と共有する場合や、同じ環境を再現する場合に、`requirements.txt`ファイルが役立ちます。このファイルには、インストールされたライブラリのリストが記載されます。

    <br>

    `requirements.txt`**の作成方法**:

    現在の仮想環境にインストールされているすべてのパッケージを`requirements.txt`ファイルに出力するには、次のコマンドを使います。
    ~~~bash
    pip freeze > requirements.txt
    ~~~

    これにより、`requirements.txt`ファイルが作成され、次のような内容が記載されます。

    ~~~makefile
    requests==2.26.0
    flask==2.0.1
    ~~~

    <br>

    `requirements.txt`**を使ってライブラリをインストールする方法**:

    他の開発者や自分自身が別の環境でこのプロジェクトを再現したい場合、`requirements.txt`を使って同じライブラリをインストールできます。

    ~~~bash 
    pip install -r requirements.txt
    ~~~

    このコマンドにより、`requirements.txt`に記載されたすべてのパッケージがインストールされます。

## 5. 仮想環境の削除とクリーンアップ
仮想環境の使用を終了したり、不要になった場合は、仮想環境を削除することでディスクスペースを節約し、クリーンな状態に戻すことができます。ここでは、仮想環境の削除やクリーンアップの方法について解説します。

1. **仮想環境の無効化（再確認）**

    仮想環境がまだ有効化されている場合は、まず無効化して通常のシステム環境に戻る必要があります。無効化するには、次のコマンドを使用します。

    ~~~bash
    deactivate
    ~~~
1. **仮想環境の削除**

    仮想環境を削除する場合は、単純に仮想環境のフォルダ（通常は `venv` と命名されている）を削除すればOKです。仮想環境は他の部分に依存しない独立したディレクトリなので、削除することで環境全体が削除されます。

    ~~~bash
    rm -rf venv
    ~~~

    `rm -rf`コマンドは、`venv`フォルダとその中のすべてのファイルを強制的に削除します。これで仮想環境は完全に削除されます。

1. **クリーンアップと再作成**

    仮想環境を一度削除しても、`requirements.txt`ファイルがあれば簡単に仮想環境を再作成できます。新しい仮想環境を作成し、`requirements.txt`に従って必要なライブラリをインストールすることで、以前と同じ環境を再現できます。

    再作成の手順は以下の通りです。

    1. 仮想環境の再作成：

        ~~~bash
        python -m venv venv
        ~~~ 
    1. 仮想環境の有効化：

        ~~~bash
        source venv/bin/activate   # Mac/Linux
        venv\Scripts\activate       # Windows
        ~~~
    1. `requirements.txt`からライブラリをインストール：
        ~~~bash
        pip install -r requirements.txt
        ~~~
    これで、クリーンな状態で仮想環境が再作成されます。


---
---
### 注意
- `pip install -r requirements.txt` を実行中に下記のようなメッセージが出たら2行目の`python.exe -m pip install --upgrade pip` の部分をコピー＆ペーストして実行し、`pip` をアップデートしてください。

    ~~~bash
    [notice] A new release of pip is available: 24.0 -> 24.2
    [notice] To update, run: python.exe -m pip install --upgrade pip
    ~~~

- **Pythonプログラムファイルの配置場所**

    仮想環境を作成したプロジェクトディレクトリに、Pythonのプログラムファイルや他の関連ファイルを置きます。通常、仮想環境を作成したプロジェクトディレクトリ自体がプログラムの管理場所となります。例えば、以下のような構造が考えられます。

    ~~~bash
    my_project/
    │
    ├── venv/               # 仮想環境フォルダ
    ├── main.py             # Pythonのメインプログラム
    ├── module1.py          # その他のモジュール
    ├── requirements.txt    # 使用するライブラリのリスト
    └── README.md           # プロジェクト説明ファイル
    ~~~
    
    - **具体例**：
    1. **仮想環境**(`venv`)はそのまま`my_project/`ディレクトリ内に存在しますが、ここに直接プログラムファイルは置きません。
    1. **Pythonプログラムファイル**（例えば`main.py`や`module1.py`など）は、`venv`フォルダと同じ階層に配置します。
    1. `requirements.txt` などのプロジェクトに関する設定ファイルも、仮想環境と同じプロジェクトルートに置くことが一般的です。

    - **pythonファイル実行方法**：
        ~~~bash
        #仮想環境を有効化
        venv\Scripts\activate

        #プログラムを実行
        python practice.py
        ~~~


---
---
### 出席管理　Windowsアプリ実行環境
#### **libzbarのインストール**：
1. [zbarの公式サイト](https://zbar.sourceforge.net/download.html) にアクセスし、**Windows用のzbar binaries**をダウンロードします。
    - `zbar`の64ビット版のダウンロードが推奨されます。
1. ダウンロードしたZIPファイルを解凍し、その中にある`libzbar-64.dll`ファイルを仮想環境内の`Lib\site-packages\pyzbar`ディレクトリにコピーします。

#### **必要ファイルのダウンロード**：

1. pythonファイルを実行するために以下のダウンロードが必要

    > サイトURL：<br>
    https://support.microsoft.com/ja-jp/topic/visual-c-2013-%E3%81%8A%E3%82%88%E3%81%B3-visual-c-%E5%86%8D%E9%A0%92%E5%B8%83%E5%8F%AF%E8%83%BD%E3%83%91%E3%83%83%E3%82%B1%E3%83%BC%E3%82%B8-%E3%83%91%E3%83%83%E3%82%B1%E3%83%BC%E3%82%B8%E3%81%AE%E6%9B%B4%E6%96%B0%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0-5b2ac5ab-4139-8acc-08e2-9578ec9b2cf1

    >ダウンロードURL：<br>
    https://download.microsoft.com/download/8/2/9/829AC8B2-E111-4F58-9B23-205A5E7D656A/vcredist_x64.exe



#### **環境変数設定**：

1. windowsボタンを押し、検索に「**環境変数**」と入れて、「**環境変数を編集**」を選択してください。
1. Pathを選択し、編集ボタンを押します。
1. 新規を選択し、下記のパスを入力します。<br>
    C:\Users\lightbox2\venv_python\win_app_project\venv\Lib\site-packages\pyzbar
