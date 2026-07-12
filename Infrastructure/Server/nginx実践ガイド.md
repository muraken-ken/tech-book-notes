# nginx実践ガイド
## 第1章 nginxとは？
### 1-1. 注目を集めるnginx
- 歴史
  - 2004年：Igor Sysoev氏 最初のバージョンリリース
  - 2011年：NGINX,Inc. 設立
  - 2021年8月時点：シェア1位

### 1-2. nginxの機能と特徴
- 使用例
  - 静的なコンテンツのWebサーバー
  - 動的なコンテンツのWebサーバー
  - ロードバランサ、リバースプロキシ：設定が容易で高性能
- 特徴
  - 設定ファイルの構造がシンプル
  - 基本機能がしっかり作ってあり、安定して動作する
  - 性能が高い
  - 新機能への対応が早い

### 1-3. Webシステムのアーキテクチャ
```
                               => Webサーバー => アプリケーション
インターネット => ロードバランサ => Webサーバー => アプリケーション => DB、ファイル
                               => Webサーバー => アプリケーション
```  
上記のロードバランサ、Webサーバー部分にnginxは使用可能

### 1-4. nginxの内部構造
- プロセス
  - nginx:master process
  - nginx:worker process  
```
           => worker => I/O =>
master => worker => I/O => Webサーバー、クライアント
           => worker => I/O =>
```  
- master
  - rootユーザーで起動
  - 設定ファイルの読み込み
  - ソケットの待ち受け
  - worker起動／監視
  - 設定再読み込み、再起動
- worker
  - nginx,www（一般）ユーザーで起動
  - イベントループ
  - ネットワークI/O => 処理が多重化 => 高性能
  - ファイルI/O => 多重化も可能
- モジュール構造
  - 機能ごとにモジュール化
  - コンパイル時のフラグで設定

## 第2章 nginxのインストール
### 2-1. パッケージの種類
- バージョン系統
  - stable：安定版（毎年4月にメジャーバージョンアップ）
  - mainline：開発版  
  ※開発元はmainlineの使用を推奨

### 2-2. 本書で利用するその他のパッケージ
- 本体以外に必要なソフトウェア
  - openssl
  - firewalld
  - systemd
  - logrotate
  - CentOS7に入っているもの、入っていない場合は都度、インストール

### 2-3. nginxのインストール
- インストール方法  
[インストール方法](https://nginx.org/en/linux_packages.html)を参照
- Ubuntu
  - 必要なパッケージを事前にインストール  
  `sudo apt install curl gnupg2 ca-certificates lsb-release ubuntu-keyring`
  - aptがパッケージの信頼性を確認できるように、公式のnginxの署名鍵を導入します。鍵を取得します。  
  `curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null`
  - ダウンロードしたファイルに適切な鍵が含まれていることを確認する。  
  `gpg --dry-run --quiet --import --import-options import-show /usr/share/keyrings/nginx-archive-keyring.gpg`
  - 出力には、以下のように完全なフィンガープリント 573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62 が含まれているはずです。  
  ```
    pub rsa2048 2011-08-19 [SC] [expires: 2024-06-14] となります。
          573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62
    uid nginx signing key <signing-key@nginx.com>
  ```  
  フィンガープリントが異なる場合は、そのファイルを削除してください。  
  - stable nginxパッケージのためにaptリポジトリを設定するには、以下のコマンドを実行します。  
  ```
    echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
    http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
        | sudo tee /etc/apt/sources.list.d/nginx.list
  ```  
  - mainline nginxパッケージを使用したい場合は、代わりに以下のコマンドを実行してください。  
  ```
    echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
    http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" \
        | sudo tee /etc/apt/sources.list.d/nginx.list
  ```  
  - リポジトリのピン留めを設定して、ディストリビューションで提供されているパッケージよりも自分たちのパッケージを優先するようにします。  
  ```
    echo -e "Package: *nPin: origin nginx.org \nPin: release o=nginx \nPin-Priority: 900 \n" \
        | sudo tee /etc/apt/preferences.d/99nginx
  ```  
  - nginx をインストールするには、以下のコマンドを実行します。  
  ```
    sudo apt update
    sudo apt install nginx
  ```

## 第3章 基本操作
### 3-1. インストールされたファイルの全体構造
- ディレクトリ構造
|ディレクトリ／ファイル名|内容|
|---|---|
|/etc/logrotate.d/nginx|ログローテションの設定ファイル|
|/etc/nginx|nginxの設定ファイル|
|/usr/lib/systemd/system/nginx.service|systemdの設定ファイル、起動スクリプトに相当|
|/usr/lib/nginx/modules|追加モジュールを配置|
|/usr/shere/nginx/html|デフォルトのドキュメントルート|
|/var/cache/nginx|キャッシュファイルが置かれるディレクトリ|
|/var/log/nginx|ログファイルが書き出されるディレクトリ|
- 設定ファイル `/etc/nginx`
|ファイル名|内容|
|---|---|
|nginx.conf|最初に読み込まれる設定ファイル|
|mime.types|ファイルの拡張子とContent-Typeのマッピングテーブル|
|conf.d/default.conf|基本的なWebサーバーとしての設定が記述されている|
|fastcgi_params|FastCGIのパラメータとnginxの変数やテキストのマッピングテーブル|
|scgi_params|SCGIのパラメータとnginxの変数やテキストのマッピングテーブル|
|uwsgi_params|uWSGIのパラメータとnginxの変数やテキストのマッピングテーブル|

### 3-2. ファイアウォールの設定
- ファイアウォールの確認 `sudo ufw app list`
  ```
    Output
    Available applications:
      Nginx Full
      Nginx HTTP
      Nginx HTTPS
      OpenSSH
  ```  
  - Nginx Full: このプロファイルは、ポート80（通常の暗号化されていないWebトラフィック）とポート443（TLS/SSL暗号化トラフィック）の両方を開きます。
  - Nginx HTTP: このプロファイルは、ポート80（通常の暗号化されていないWebトラフィック）のみを開きます。
  - Nginx HTTPS: このプロファイルは、ポート443 （TLS/SSL暗号化トラフィック）のみを開きます。
- 有効化 `sudo ufw allow 'Nginx HTTP'`

### 3-3. 起動／終了／再起動／設定ファイル再読み込み
- 開始 `sudo service nginx start`
- 終了 `sudo service nginx stop`
- 再起動 `sudo service nginx restart`
- 設定の再読み込み `sudo service nginx reload`

### 3-4. nginxコマンドの使い方
|コマンド|内容|
|---|---|
|nginx -h|ヘルプ表示|
|nginx -v|バージョン情報の表示|
|nginx -t|設定ファイルのテスト、エラー表示|
|nginx -T|設定ファイルのテスト、ファイル内容の表示|
|nginx -V|バージョン情報、コンパイラのバージョンやコンパイラフラグの表示|
|nginx -s stop|実行中のnginxデーモンを強制終了|
|nginx -s quit|実行中のnginxデーモンをリクエスト終了後に終了(graceful shutdown)|
|nginx -s reopen|実行中のnginxデーモンにログファイルを開き直させる|
|nginx -s reload|実行中のnginxデーモンに設定ファイルを再読み込みさせる|

### 3-5. デーモンの制御
- nginxのプロセスへのシグナル送信
  - masterプロセス
  |シグナル|内容|
  |---|---|
  |TERM/INT|master/workerを強制終了する|
  |QUIT|master/workerをgraceful shutdownする|
  |HUP|設定ファイルの再読み込み|
  |USR1|ログファイルを開き直す|
  |USR2|新たな実行ファイルで再起動する|
  |WINCH|workerをgraceful shutdownする|
  - workerプロセス
  |シグナル|内容|
  |---|---|
  |TERM/INT|強制終了する|
  |QUIT|graceful shutdownする|
  |USR1|ログファイルを開き直す|
  |WINCH|異常終了する|

### 3-6. nginxのログファイル
- 出力先（デフォルト）
  - `/var/log/nginx/access.log`
  - `/var/log/nginx/error.log`
- ログファイルのローテート
  - logrotate
    - 設定ファイル `/etc/logrotate.d/nginx`
    - 毎晩ローテートでファイル切り替え
    - 古いファイルは、日付を末尾に付加し、gzipで圧縮され、52日分以上のファイルは自動的に削除

## 第4章 Webサーバーの構築
### 4-1. HTTPとは
省略「Webを支える技術」を参照

### 4-2. 設定ファイルの構造
- 設定ファイル `/etc/nginx/nginx.conf`
- ディレクティブとコンテキスト
  - ディレクティブ：設定項目
  - コンテキスト：設定内容
    ```
      # '#'から行末まではコメント

      nginx本体の設定（mainコンテキスト）

      events {
        イベント待ちのパラメーター # （eventsコンテキスト）
      }

      http {
        Webサーバー全体の設定
        server {
          バーチャルホスト1の設定
          location ... {
            URLのパス名ごとの設定
          }
        }
        server {
          バーチャルホスト2の設定
          location ... {
            URLのパス名ごとの設定
          }
        }
      }
    ```
  - [公式サイト](https://nginx.org/en/docs/)
  - includeディレクティブ => 別の設定ファイルを読み込む

### 4-3. 基本設定
- mainコンテキスト
  ```
    user  nginx;                                # ユーザー名
    worker_processes  auto;                     # workerプロセス数 auto：コア数

    error_log  /var/log/nginx/error.log notice; # エラーログ、場所、ログレベル
    pid        /var/run/nginx.pid;              # プロセスID
  ```  
  ※ 他にも書けるコンテキストはある
- eventsコンテキスト
  ```
    events {
      worker_connections  1024;   # 同時に受け付けられる接続数
    }
  ```
- httpコンテキスト
  ```
    http {
        include       /etc/nginx/mime.types;    # MIMEタイプの設定ファイル読み込み
        default_type  application/octet-stream; # 任意のバイナリファイル => HTTP Content-Type:ヘッダが正しく設定

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';           # ログ書式の設定

        access_log  /var/log/nginx/access.log  main;  # アクセスログの出力先

        sendfile        on;   # ファイルの送信方法
        #tcp_nopush     on;   

        keepalive_timeout  65;    # Keep-Aliveのサーバー側タイムアウト

        #gzip  on;

        include /etc/nginx/conf.d/*.conf;   # 他の設定ファイルの読み込み
    }
  ```
- 設定ファイルで使える変数
  ```
    GET /path/to/file?a=1&b=2 HTTP/1.1
    Host:www.example.com
    User-Agent:BrowserBame
    Cookie: c=3
  ```
  |nginx変数名|内容|
  |---|---|
  |$request_method|GET|
  |$args, $query_string|a=1&b=2|
  |$arg_a|1|
  |$arg_b|2|
  |$cookie_c|3|
  |$host, $http_host|www.example.com|
  |$url, $document_url|/path/to/file|
  |$request_url|/path/to/file?a=1&b=2|
  |$http_user_agent|BrowserName|
  |$http_cookie|c=3|
  |$is_arg|?|
  |$request|GET /path/to/file?a=1&b=2 HTTP/1.1|
  |$scheme|http|
  |$server_protocol|HTTP/1.1|
- ログ引数
  - Apache：%ではじまる書式が定義
  - nginx：変数がそのまま使える
- ディレクティブの引数への変数の利用
  - 使える：access_logなど
  - 使えない：error_logなど
- ユーザー定義の変数
  - `set $<変数名> 値;`
    - 使える：server、location、ifコンテキスト
    - 使えない：main、httpコンテキスト

### 4-4. 静的なWebサイトの構築
- serverコンテキスト（例）
  ```
    server {
      listen 80;
      server_name static.example.com;
      access_log /var/log/nginx/static-access.log;
      error_log /var/log/nginx/static-error.log;

      location / {
        root /www/dir;
        index index.html index.htm;
      }
    }
  ```
  - listenディレクティブ  
  待ち受けるポート番号を示す  
    ```
      listen 192.0.2.1:80;              # IPアドレス指定
      listen [2001:db8::1]:80;          # IPv6アドレス指定
      listen unix:/var/run/nginx.sock;  # Unixドメインソケット使用
    ```
  - server_nameディレクティブ  
  バーチャルホストのサーバーのホスト名を指定する
    ```
      *.example.com             # wildcard
      www.example.*             # wildcard
      ~^www\d+\.example\.com$   # 正規表現
      .example.com              # *.example.com or example.com
    ```
    - 優先順位  
      - 完全一致
      - *で始まるワイルドカードで最長のもの
      - *で終わるワイルドカードで最長のもの
      - 設定ファイルの中で最初にマッチする正規表現
  - locationディレクティブ
    ```
      location <path名> {...}       # 前方一致 /：全てのパス /<path名>/：path名で始まる全て
      location = <path名> {...}     # 完全一致
      location ~ <パターン> {...}   # 正規表現
      location ~* <パターン> {...}  # 正規表現 大文字／小文字区別なし
      location ^~ <パターン> {...}  # 前方一致 正規表現
      location @名前 {...}          # コンテキストに名前をつける
    ```
    - 優先順位
      - 完全一致
      - 前方一致の最長のもので「^~」修飾子がついたもの
      - 正規表現で最初に一致したもの
      - 前方一致の最長のもの
    - ネストしたlocationディレクティブ <= 優先順位の制御に便利
    ```
      location /path/ {
        # /path/ で始まる場合
        location /subpath/ {
          # どこにもマッチしない
        }
      }

      location /path/ {
        # /path/ で始まる場合
        location /path/subpath/ {
          # /path/subpath/ で始まる場合
        }
      }
    ```
  - rootディレクティブ、indexディレクティブ
    - rootディレクティブ：ドキュメントルート（特定条件に一致）
    - indexディレクティブ：レスポンスに使用されるファイル名
  - aliasディレクティブ
    ```
      location /files/ {
        root /data/;      # /data/files/a.html
      }

      location /files/ {
        alias /data/;     # /data/a.html => location を書き換える
      }
    ```
- ブログサイトの構築例：Ruby gem 「jekyll」を参照

### 4-5. アクセス制限
- IPアドレスによるアクセス制限
  ```
    location /private {
      deny  192.168.1.1;    # 192.168.1.1拒否
      allow 192.168.1.0/24; # 192.168.1.1～255許可
      deny all;             # 全部拒否 => 192.168.1.2～255のみ許可（ファイルの記述順）
    }
  ```
- パスワード認証によるアクセス制限（Basic認証）
  ```
    location /private {
      auth_basic           "enter password"
      auth_basic_user_file /etc/nginx/htpasswd; # Apacheと同じファイルが使える
    }
  ```
- 両方書いた場合
  - and条件となる
  - or条件 `satisfy any;`を記述

## 第5章 Webアプリケーションの構築
### 5-1. WebアプリケーションとWebサーバーの接続方法
- CGI(Common Gateway Interface)  
アクセスごとにWebアプリのプロセスを毎回起動、パラメータを環境変数・標準入出力でやりとり。起動コスト高い => 最近は使われない
- FastCGI（人気）  
CGIの処理遅いを改善。Webアプリのプロセスがデーモンとして常駐、パラメータはソケットを通じてバイナリプロトコルでやりとり。
- SCGI(Simple CGI)  
FastCGIと同様だが、単純で実装しやすい
- Webサーバーの拡張モジュール  
Webサーバー内部でWebアプリの言語をの処理系を動作させる接続方法。Apacheはモジュール多い、nginxは少ない
- WSGI(Web Server Gateway Interface)  
PythonのWebアプリ用プログラミングインターフェイス  
  - Ruby：Rack
  - Perl：PSGI
- TCPソケットのHTTPサーバー（最近、人気）  
WebサーバーのバックエンドでWebアプリを含んだWebサーバーを動作、前段のWebサーバー側でリバースプロキシを設定する
- UnixドメインソケットのHTTPサーバー  
WebサーバーのバックエンドとしてTCPソケットの代わりにUnixドメインソケットを使用する。他のホストとの通信は不可。

### 5-2. Webアプリケーションの設定によく使われる機能
- メソッドごとのアクセス制限
  ```
    location / {
      limit_except GET POST {
        deny all;
      }
    }
  ```

### 5-3. URLの書き換え
  - rewriteモジュール
    - 条件判定
      - URLのホスト部やポート番号での判定
        ```
          server_name ~^(www\.)?(.+)$;            # $1:www or なし $2:その他
          server_name ~^(www\.)?(?<domain>.+)$;   # $1:www or なし $2 or $domain:その他
        ```  
        ※ nginxの正規表現：PCRE(Perl形式)のライブラリを使用
      - パス名で判定
        ```
          location ~ ^/path1(.+\.(?<ext>gif|jpe?g|png))$ {} # /path1/test.gif => $1:test.gif $2/$ext:gif
        ```
      - ファイルやディレクトリの有無での判定
        ```
         location ~ ^/path1(?<subpath>/.+)$ {
           try_files $subpath $subpath/ /newpath$subpath;
           # /path1/dir/file => /dir/file → /dir/file/ → /newpath/dir/fileの順で探す
         }
        ```
      - 条件式による判定
        ```
          if (条件) {         # 引数の条件は1つ、AND/ORは不可、locationにしか使えない、複数のifは非推奨
            # 条件に合う場合  
          }
        ```
        - 変数名：空文字、0以外は真
        - 変数や文字列の比較：「`=`」一致、「`!=`」一致しない
        - 変数や文字列と正規表現のマッチ：「`~`」大／小文字区別、「`~*`」大／小文字区別なし、「`!~`」「`!~*`」否定
        - ファイルの有無：「`-f パス名`」ファイルあり、「`!-f パス名`」ファイルなし
        - ディレクトリの有無：「`-d パス名`」ディレクトリあり、「`!-d パス名`」ディレクトリなし
        - ファイル／ディレクトリ／シンボリックリンクの有無：「`-e パス名`」あり、「`!-e パス名`」なし
        - 実行ファイルの有無：「`-x パス名`」あり、「`!-x パス名`」なし
      - geoディレクティブ：アクセス元のIPアドレス判定
        ```
          geo $from {
            default         external;
            192.168.0.0/16  internal;
            10.0.0.0/8      internal;
          }
          server {
            /var/www/$from; # 内部用=> /var/www/internal 外部用=> /var/www/external
          }
        ```
      - mapディレクティブ：文字列マッチングの判定
        ```
          map $http_user_agent $mobile {
            default           0;
            ~(Android|iPhone) 1;
          }
          server {
            if ($mobile) {
              # Android/iPhone の場合
            }
          }
        ```
    - URLの書き換え  
    ※ 判定と書き換えは10回まで実行、収束しない場合はInternal Seerver Error(500)となる
      - returnディレクティブ：リダイレクト
        ```
          return http://www.example.com;      # 302(Moved Temporarily)
          return 301 http://www.example.com;  # 303,307も同様
          return 404 "not found.";            # 第二引数省略 => デフォルトメッセージ
          return 444;                         # nginx固有：接続断                        
        ```
      - rewriteディレクティブ：リダイレクト(302)
        ```
          rewrite 正規表現 置換文字列 [フラグ];
        ```
        - フラグなし：noflag => a_noflag => b_noflag 
          ```
            location /noflag {
              rewrite ~ /noflag /a_noflag;    # 実行
              rewrite ~ /a_noflag /b_noflag;  # 実行
            }
            location /a_noflag {
              rewrite ~ /a_noflag /c_noflag;    # 実行されない
            }
          ```
        - lastフラグ：last => a_last => C_last 
          ```
            location /last {
              rewrite ~ /last /a_last last;   # 実行
              rewrite ~ /a_last /b_last last; # 実行されない
            }
            location /a_last {
              rewrite ~ /a_last /c_last last; # 実行
            }
          ```
        - breakフラグ：break => a_break 
          ```
            location /break {
              rewrite ~ /break /a_break break;   # 実行
              rewrite ~ /a_break /b_break break; # 実行されない
            }
            location /a_break {
              rewrite ~ /a_break /c_break break; # 実行されない
            }
          ```
    - locationディレクティブの@プレフィックス  
    ※ error_page,post_action,try_filesの転送にしか使えない
      ```
        location / {
          try_files $uri $uri/ @fallback; # ファイルが見つからなければ、Webアプリへ
        }
          location @fallback {
            proxy_pass http://backend;    # Webアプリへ転送
          }
      ```
    - internalディレクティブ  
    locationコンテキスト内に「`internal;`」を記述  
    locationのマッチが普通のlocationと同様でrewriteの書き換え先のパス名にマッチさせられる
    - rewrite_logディレクティブ：状況を把握したいときにログで確認できる
      ```
      server {
        rewrite_log on;
        error_log /var/log/nginx/rewrite.log notice;
      }
      ```

### 5-4. PHP(PHP-FPM)でWordPressを動かす  
### 5-5. Python(uWSGI)でtracを動かす
### 5-6. Ruby(Phusion Passenger)でRedmineを動かす
### 5-7. Unixドメインソケットによる接続
- nginxでリバースプロキシ設定、アプリは内蔵のWebサーバー利用
  ```
    server {
      location / {
        proxy_pass http://unix:/var/run/unix.sock:/; #「unix:path名」で設定
      }
    }
  ```

### 5-8. TCPソケットによる接続
- nginxでリバースプロキシ設定、アプリは内蔵のWebサーバー利用  
※ Unixドメインソケットに対応していない場合
  ```
    server {
      location / {
        proxy_pass http://127.0.0.1:3000/; #TCPソケットで設定
      }
    }
  ```

### 5-9. CGIへの対応  
※ nginxはCGIには対応していない
- FastCGIでCGIを実行するfcgiwrapを使用する
  - fcgiwrapのインストール
    ```
      # apt install fcgiwrap
    ```  
※ 設定はネット参照

## 第6章 HTTPSへの対応
### 6-1. SSL/TLSについて知ろう
### 6-2. サーバー証明書の取得
### 6-3. nginxにサーバー証明書を設定しよう
- nginxに設定
  - 証明書
  - 秘密鍵
    ```
      $ cat server.crt chain.crt crossroot.crt > your-server.crt
      $ sudo cp your-server.crt /etc/pki/tls/certs/
      $ sudo chown root:root /etc/pki/tls/certs/your-server.crt
      $ sudo chmod 400 /etc/pki/tls/certs/your-server.crt
    ```  
    ```
      server {
        listen 443 ssl;
        ssl_certificate /etc/pki/tls/certs/your-server.crt;
        ssl_certificate_key /etc/pki/tls/certs/your-server.key;
        root /usr/share/nginx/html;
      }
    ```
  - [Let's Encrypt](https://letsencrypt.org/ja/)で証明書を発行

### 6-4. HTTPSのセキュリティ設定
- 常にセキュリティ情報を入手し、必要に応じ作業を行う
- Mozillaの設定ガイドライン：有用
  - [設定ガイドライン](https://wiki.mozilla.org/Security/Server_Side_TLS)
  - [設定ジェネレータ](https://ssl-config.mozilla.org/)
  - [チェッカー](https://github.com/mozilla/tls-observatory)
    - Modern：新しい設定（2013～14年以降の環境）
    - Intermediate：IEをVerUpしていないXPを考えない設定（2004～09年頃の環境）
    - Old：XPに対応 => 現在、使用すべきではない
      ```
        server {
            listen 80 default_server;
            listen [::]:80 default_server;

            location / {
                return 301 https://$host$request_uri;
            }
        }

        server {
            listen 443 ssl http2;
            listen [::]:443 ssl http2;

            ssl_certificate /path/to/signed_cert_plus_intermediates;
            ssl_certificate_key /path/to/private_key;
            ssl_session_timeout 1d;
            ssl_session_cache shared:MozSSL:10m;  # about 40000 sessions
            ssl_session_tickets off;

            # curl https://ssl-config.mozilla.org/ffdhe2048.txt > /path/to/dhparam
            ssl_dhparam /path/to/dhparam;

            # intermediate configuration
            ssl_protocols TLSv1.2 TLSv1.3;  # TLSv1は削られた
            ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
            ssl_prefer_server_ciphers off;  # off：クライアント側優先 on：サーバー側優先

            # HSTS (ngx_http_headers_module is required) (63072000 seconds)
            add_header Strict-Transport-Security "max-age=63072000" always;

            # OCSP stapling
            ssl_stapling on;
            ssl_stapling_verify on;

            # verify chain of trust of OCSP response using Root CA and Intermediate certs
            ssl_trusted_certificate /path/to/root_CA_cert_plus_intermediates;

            # replace with the IP address of your resolver
            resolver 127.0.0.1;
        }
      ```
      - 暗号化アルゴリズム：ssl_ciphers 
        - 前方秘匿性(Perfect Forword Secrecy)  
        暗号化された通信が盗聴され保管され、後から秘密鍵が盗まれても暗号が解けない => ディフィー・ヘルマン(DH)鍵交換  
        「DH」「DHE」が名前に含まれるもの
      - OCSPステープリング(OCSP Stapling)
        ```
          $ cat chain.crt crossroot.crt > chainroot.crt
          $ cp chainroot.crt /etc/pki/tls/certs/
        ```
        ```
          # OCSP stapling
          ssl_stapling on;
          ssl_stapling_verify on;
          ssl_trusted_certificate /etc/pki/tls/certs/chainroot.crt;
        ```
      - HSTS(HTTP Strict Transport Security)：HTTPでのアクセス => HTTPSへリダイレクト
        ```
        server {
            listen 80 default_server;
            listen [::]:80 default_server;

            location / {
                return 301 https://$host$request_uri;
            }
        }
        server {
            listen 443 ssl http2;
            listen [::]:443 ssl http2;

            # HSTS (ngx_http_headers_module is required) (63072000 seconds)
            add_header Strict-Transport-Security "max-age=63072000" always;
        }
        ```
      - HPKP(Public Key Pinning Extension for HTTP)  
      公開鍵のフィンガープリント（ハッシュ値）をHTTPヘッダに書き込んでおき、不正に鍵が変更されたことを検出する規格
        - TOFU(Trust On First Use)  
        最初のアクセスを信頼して、次のアクセス時に変更を検出する手法

### 6-5. SNI(Server Name Indication)  
HTTPSでバーチャルホストを使えるようなSSL/TLSの拡張  
Client Helloを受信したサーバーはサーバー名を見て、サーバー証明書を選択
  ```
    server {
      listen 443 ssl;
      server_name aaa.example.com;
      ssl_certificate     serverA.crt;
      ssl_certificate_key serverA.key;
    }
    server {
      listen 443 ssl;
      server_name bbb.example.com;
      ssl_certificate     serverB.crt;
      ssl_certificate_key serverB.key;
    }
  ```

### 6-6. HTTPSの性能を上げる設定  
SSL/TLSのハンドシェイクの処理をどう省略するかがポイント  
- セッションを切断しない(HTTP Keep-Alive)
  - keepalive_request：1つの接続で処理するリクエストの最大数(default:100)
  - keepalive_timeout：サーバー側75秒
- 切断したセッションを再開する  
SSL/TLSで最も負荷のかかる処理は共通鍵を生成する部分  
鍵をキャッシュしておき、2回目以降の接続で再利用 => 大幅に軽量化
  - セッションID  
  サーバー側セッションIDを発行し、双方で記憶 => サーバーを複数台にクラスタ化した場合に問題
    ```
      server {
        ssl_session_timeout 1d;           # default 5min
        ssl_session_cache shared:SSL:50m; # default none
      }
    ```
  - セッションチケット  
  サーバー側で共有鍵を暗号化したものをチケット情報としてクライアントに送り、再接続時は復号してセッションを再開
    ```
      server {
        ssl_session_ticket on;
        ssl_session_ticket_key current.key;
        ssl_session_ticket_key prev1.key;
        ssl_session_ticket_key prev2.key;
      }
    ```
  - HTTP/2  
  Webの性能を向上させるプロトコルでヘッダの圧縮、リクエストの多重化、優先度制御 など
    ```
      server {
        listen 443 ssl http2;
      }
    ```

### 6-7. 自己署名の証明書を使う場合  
Apacheの資料を参照

## 第7章 リバースプロキシ
### 7-1. リバースプロキシとは  
- フォワードプロキシとリバースプロキシ
  - フォワードプロキシ  
  単にプロキシサーバー、組織内部のLANからインターネットにアクセスするために用意する
  - リバースプロキシ  
  外側のネットワークから内側のネットワークにアクセスを中継するサーバー（Webサーバーの入口）
    - 負荷分散
    - キャッシュ
    - 暗号化(HTTPS)対応
- ロードバランサ  
リバースプロキシとして動作し、複数のアプリケーションサーバーから1つを選択し、アクセスを転送する  
  - サーバーの増強
    - スケールアップ：サーバーの処理能力向上 => データベースサーバー
    - スケールアウト：複数のサーバーに負荷分散（容易、コスト小で規模を拡大） => Webアプリサーバー
- リバースプロキシの便利な使い方
  - 可用性の向上  
  複数台のアプリケーションサーバーを用意し、1台がダウンしても他のサーバーで補う
  - メンテナンスの補助  
  アップグレード、メンテナンス、好位性変更時に有用
  - キャッシュによる性能向上  
  コンテンツをキャッシュし、Webサイトの性能を向上
  - HTTPSへの対応  
  リバースプロキシにSSLの処理をさせ、バックエンドはHTTPを使うことで、容易にHTTPS化
- リバースプロキシの基本設定
  - upstreamコンテキスト：アプリケーションサーバー
  - serverコンテキスト：外部からのアクセス方法
    ```
      http {
        upstream app1 {
          server 192.168.1.10:8080;
          server 192.168.1.11:80;
          server 192.168.1.12:8080;
        }
        server {
          listen 80;
          location / {
            proxy_pass http://app1;
          }
        }
      }
    ```

### 7-2. アクセス元IPアドレスとヘッダの設定
- ヘッダ情報
|ヘッダ|意味|
|---|---|
|X-Forworded-For|クライアントIPアドレスを設定する|
|X-Real-IP|クライアントIPアドレスを設定する|
|X-Forworded-Host|受信したHostヘッダを設定する|
|X-Forworded-Server|リバースプロキシのホスト名を設定する|
|Via|プロトコルバージョン、プロキシサーバーのホスト名、コメントを設定する|
  ```
    server {
      proxy_set_header Host               $host;
      proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Host   $host;
      proxy_set_header X-Forwarded-Server $hostname;
      proxy_set_header X-Real-IP          $remote_addr;
    }
  ```
  - リクエストヘッダ（削除や書き換えられる）
  |ヘッダ|意味|
  |---|---|
  |Host|サーバーのホスト名を示す、proxy_passで指定したupstream名に変換|
  |Connection|リクエスト完了後にcloseに変換|
  |Content-Length|正しい値に変換|
  |Transfer-Encoding|正しい値に変換|
  |TE|上記と同じ意味、削除される|
  |Keep-Akive|削除される|
  |Expect|削除される|
  |Upgrade|削除される|
  - レスポンスヘッダ（削除や書き換えられる）
  |ヘッダ|意味|
  |---|---|
  |Date|nginxが処理した時間変換|
  |Server|nginxに変換|
  |X-Aeeel-*|削除される|  
- バックエンドで使った場合のクライアントIPアドレス  
リバースプロキシが`X-Real-IP`、`X-Forwarded-For`を書き換えてクライアントIPを伝える  
nginxをバックエンドで使用すると、そのIPを使ってクライアントがそのIPであるように振る舞う機能がある
  - クライアントIPアドレスの書き換え
    ```
      server {
        set_real_ip_from 192.168.1.10;  # リバースプロキシのIPアドレス
        real_ip_header X-Forwarded-For;
      }
    ```
  - リバースプロキシとKeep-Alive
    - クライアントの接続にKeep-Aliveを使う
      ```
        server {
          keepalive_timeout 60;    
        }
      ```
    - バックエンドとの接続にkeep-Aliveを使う
      ```
        http {
          upstream app1 {
            server ・・・
            keepaliv 32;     # キープアライブ設定
          }
          server {
            listen 80;
            location / {
              proxy_http_version 1.1; # HTTP/1.1を指定
              proxy_set_header Connection "";
              proxy_pass http://backend;
            }
          }
        }
      ```
- バックエンドからの応答の書き換え
  - proxy_redirectディレクティブ
    - `off`：書き換えを行わない
    - `default`：ディレクティブ省略 => 自動で書き換え（通常は問題ない）
    - リバースプロキシでURLの書き換えを設定した場合
      ```
        upstream {
          server 192.168.1.10;
          server 192.168.1.11;
        }
        server {
          server_name example.com;
          location /one/ {
            proxy_pass     http://backend/two/;
            proxy_redirect http://backend/two/ http://example.com/one/; # defaultと同じ
          }
          location /three/ {
            rewrite ^/three/1 /three/2;           # URLの書き換え
            proxy_pass     http://backend/four/;
            proxy_redirect http://backend/four/2 http://example.com/three/1; 
          }
        }
      ```
  - proxy_cookie_pathとproxy_cookie_domainディレクティブ
    - defaultは`off`
    - 書き換える場合
      ```
        server {
          server_name example.com;
          location /one/ {
            proxy_pass          http://backend/two/;
            proxy_cookie_path   /two/  /one/;
            proxy_cookie_domain backend $server_name;
          }
        }
      ```

### 7-3. 分散方式
- upstreamコンテキストで定義されるバックエンドが複数ある場合：ラウンドロビン（default）
- 分散の手法
  - 重み付け：バックエンドの性能に差がある場合
    ```
      upstream backend {
        server 192.168.1.10 weight=2;
        server 192.168.1.11 weight=1;
        server 192.168.1.12;  # defaultはweight=1と同じ
      }
    ```
  - 稼働系と待機系：冗長性を持たせる場合
    ```
      upstream backend {
        server 192.168.1.10;
        server 192.168.1.11 backup; # エラー時のみ稼働
      }
    ```
  - 接続数による負荷分散
    ```
      upstream backend {
        least_conn;   # 接続数が最も少ないサーバーを選択
        server 192.168.1.10;
        server 192.168.1.11;
      }
    ```
  - IPアドレスによるハッシュ  
  セッション管理の都合などで、同じクライアントからのアクセスを同じバックエンドサーバーに割り振りたい場合  
  IPアドレスの第3オクテットまで
    ```
      upstream backend {
        ip_hash;
        server 192.168.1.10;
        server 192.168.1.11;
      }
    ```
  - ハッシュキーの指定
    ```
      upstream backend1 {
        hash $http_user_agent;
        server 192.168.1.10;
        server 192.168.1.11;
      }
      upstream backend2 {
        hash $uri consistent;
        server 192.168.1.12;
        server 192.168.1.13;
        server 192.168.1.14;
      }
    ```

### 7-4. タイムアウトの設定  
ロードバランサで割り振られたサーバーがダウンしている場合
- ダウン状態の条件判定
  ```
    server {
      location /one/ {
        proxy_pass            http://backend/two/;
        proxy_connect_timeout 60s;
        proxy_read_timeout    60s;
        proxy_send_timeout    60s
      }
    }
  ```
  - connect_timeout：接続時のタイムアウト
  - read_timeout：バックエンドのサーバーから受信するデータが滞ってから
  - send_timeout：バックエンドのサーバーへもデータ送信が滞ってから
- タイムアウト処理
  - max_fails/fail_timeout
    ```
      upstream backend {
        server 192.168.1.10 max_fails=1 fail_timeout=10s;
        server 192.168.1.11 backup;
      }
    ```

### 7-5. キャッシュ処理とバッファ
- キャッシュ用のディレクティブ
  ```
    http {
      proxy_cache_path /var/cache/nginx/rproxy
                       levels=1:2 keys_zone=proxy1:10m
                       inactive=1d;
      
      upstream backend {
        server 192.168.1.10;
      }

      server {
        ...
        location / {
          proxy_cache proxy1;
          proxy_path http://backend;
        }
      }
    }
  ```
  - proxy_cache_pathディレクティブ
    - key_zone：ゾーン => workerの共有メモリ、サイズも指定
    - levels：キャッシュディレクトリの構造
    - inactive：キャッシュが捨てられるまでの時間
    - max_size：キャッシュ全体のサイズ
  - proxy_cacheディレクティブ  
  ゾーン名を指定し、対象コンテンツをキャッシュ
    ```
      server {
        ...
        location / {
          proxy_cache proxy1;
          proxy_cache_bypass $http_authorization $http_cookie;
          proxy_no_cache $http_authorization $http_cookie;
          proxy_pass http://backend
        }
      }
    ```
    - proxy_cache_bypassディレクティブ
    - proxy_no_cacheディレクティブ  
    空文字もしくは「0」でない場合、キャッシュからレスポンスを返さない
    - proxy_cache_keyディレクティブ
      ```
        server {
          ...
          location / {
            ...
            proxy_cache_key "$host$request_uri $cookie_user"
          }
        }
      ```
- バッファ処理  
バックエンドからのレスポンスは、バッファを使って転送  
バックエンド：高速 => バッファに溜めて接続を切り、次の処理ができる
  - proxy_nuffering：default => `on`、`off` => 無効
  - proxy_buffer_size：レスポンスの先頭部分に使われるバッファ容量、default=4KB
  - proxy_buffers：proxy_buffer_sizeの次に使われるバッファの数とサイズ、default=4KB x 8
  - proxy_max_temp_file_size：予備、default=1GB
  - proxy_temp_file_write_size：一時ファイルのバッファに一度に書き出されるデータサイズ
  - proxy_busy_buffers_size：レスポンス送信中の状態にできるバッファのサイズ
    ```
      server {
        location / {
          proxy_buffering on;
          proxy_buffer_size 4k;
          proxy_buffers 8 4k;
          proxy_max_temp_file_size 1024m;
          proxy_temp_file_write_size 8k;
          proxy_busy_buggers_size 8k;
        }
      }
    ```

### 7-6. リバースプロキシとHTTPS
- 構成
  - リバースプロキシでSSL/TLSを終端：証明書、秘密鍵をリバースプロキシに設定
  - TCPの接続を中継：バックエンドまで暗号化、nginxはロードバランサ
- HTTPSのリバーズプロキシ
  - バックエンド：http
    ```
      upstream backend {
        server 192.168.1.10;
        server 192.168.1.11;
      }

      server {
        listen 443 ssl;
        ssl_certificate /etc/pki/tls/certs/your-server.crt;
        ssl_certificate_key /etc/pki/tls/certs/your-server.key;

        location {
          proxy_pass http://backend;
        }
      }
    ```
  - バックエンド：https
    ```
      upstream backend_https {
        server 192.168.1.10:443;
        server 192.168.1.11:443;
      }

      server {
        ...
        location {
          proxy_pass https://backend_https; # デフォルトでは、bakkuendとの証明書の検証はしない
        }
      }
    ```
    - proxy_ssl_verify(default=off)：証明書の検証のon/off
    - proxy_ssl_verify_depth(default=1)：証明書の階層
    - proxy_ssl_trusted_certificate：信頼するCAの証明書ファイルを指定
    - proxy_ssl_server_name(default=off)：SNI使用のon/off
    - proxy_ssl_name(default=$proxy_host)：SNIで使うサーバー名を指定
    - proxy_ssl_session_reuse(default=on)：セッション再利用のon/off
    - proxy_ssl_crl：証明書の失効リストのファイル指定
    - proxy_ssl_protocols(default=TLSv1.1 TLSv1.2)：TLS/SSLのプロトコルを指定
    - proxy_ssl_ciphers(default=DEFAULT)：暗号アルゴリズムのリスト
    - proxy_ssl_certificate：クライアント認証に使うクライアント証明書のファイルを指定
    - proxy_ssl_certificate_key：クライアント認証に使う秘密鍵のファイルを指定
    - proxy_ssl_password_file：秘密鍵にパスフレーズが設定されている場合に試すパスフレーズのリストファイルを指定
- TCPストリームのロードバランサ
  - WebサーバーのHTTPS＋バックエンド接続のHTTPS：暗号=>復号=>暗号 負荷大、セキュリティ危険
  - TCPの通信の中身を見ずにバックエンドに中継
    - メリット：負荷小、セキュリティ安心
    - デメリット：キャッシュ、バッファ、リバースプロキシのアクセスログができない
    ```
      http {
        ...
      }
      stream {
        error_log /var/log/nginx/stream.kog info;
        include /etc/nginx/stream.d/*.conf;
      }
    ```
    - エラーログ  
    アクセスログは記録できないが、エラーログにクライアント、IPアドレス、転送したデータのバイト数のみ記録
    ```
      upstream backend {
        server 192.168.1.10:443;
        server 192.168.1.11:443;
      }
      server {
        listen 443;
        proxy_pass backend
      }
    ```
    - 重み付け
      ```
        upstream backend {
          server 192.168.1.10:443 weight=2;
          server 192.168.1.11:443 weight=1;
          server 192.168.1.12:443;  # defaultは、weight=1と同じ
        }
      ```
    - 接続元IPアドレスによるハッシュ
      ```
        upstream backend {
          hash $remote_addr;
          server 192.168.1.10:443;
          server 192.168.1.11:443;
        }
      ```
    - 最小コネクション数による負荷分散
      ```
        upstream backend {
          least_conn;
          server 192.168.1.10:443;
          server 192.168.1.11:443;
        }
      ```

### 7-7. WebSocketとリバースプロキシ
- WebSocketはHTTPを拡張し双方向通信を可能にする規格  
リクエスト／レスポンスの枠組みをなくし、必要な時に双方向通信ができる  
「ws://」、「wss://」で始まるURLを使用
- WebSocketへの対応
  ```
    http {
      map $http_upgrade $connection_upgrade {
        default upgrade;
        ''      close;
      }

      server {
        ...
        location /chat/ {
          proxy_pass http://backend;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection $connection_upgrade;
        }
      }
    }
  ```  
  - タイムアウト値の設定
    ```
      server {
        location /chat/ {
          ...
          proxy_read_timeout 1h;
        }
      }
    ```

### 7-8. アップロード処理とバッファリング
- アップロード処理のパラメータ  
1MB以上のファイルのアップロード不可 => 413(Request Entity Too Large)
  - アップロードサイズの変更
    ```
      server {
        ...
        client_max_body_size 100m;  # 0に設定すると無制限
      }
    ```
  - バックエンドのバッファサイズの変更  
    ```
      server {
        ...
        client_max_body_size 100m;                      # 100MBまで
        client_body_buffer_size 1m;                     # 1MBまではメモリ
        client_body_temp_path /var/tmp/nginx_temp 1 2;  # それ以上はファイルにバッファ 
      }
    ```
- バッファサイズと処理時間  
バッファ動作はサイズが大きくなると問題 データ=>バッファ=>バックエンド転送=>応答=>リバースプロキシが応答
  ```
    server {
      ...
      proxy_request\buffering off;  # アップロードのバッファリングをやめる
    }
  ```

### 7-9. S3互換のオブジェクトストレージの構築例
- 仕様
  - オブジェクトストレージ：minio （AWS S3互換）
  - S3プロトコル：HTTP
  - minioはGO言語 => HTTPサーバーもGo言語のライブラリ
- minioのインストール
- nginxの設定
  ```
    upstream minio {
      server http://127.0.0.1:9000;
    }
    server {
      server_name s3.example.com;
      location / {
        proxy_pass http://minio;
        proxy_set\header Host $host;
        proxy_buffering off;
        proxy_request_buffering off;
        proxy_http_version 1.1;
        client_max_body_size 0;
      }
    }
  ```

## 第8章 性能向上
### 8-1. 性能向上の基礎知識
- スループットとレスポンス時間と多重度の関係
  - スループット：時間当たりの処理量を表す
    - 帯域
    - bytes/sec、request/sec
    - 大きいほど性能が高い
  - レスポンス時間：1つのアクセス応答にかかった時間
    - レイテンシ、応答時間
    - 1つのアクセス：プロトコル処理、I/O処理など全てが含まれる
  - 多重度：同時に行うアクセスの数を表す
    - 同時接続数、並列度
  - 関係式  
  スループット = 多重度 / レスポンス時間
- ベンチマークとボトルネック
  - ベンチマーク：システムの性能を測定するソフトウェア
    - システムベンチマーク：システム全体
    - マイクロベンチマーク：個別の要素
      - ネットワーク：iperf、netperf
      - ストレージ：filebench、fio、iozone、dbench
      - Webサーバー：ab、siege、wrk、httperf、jmeter
  - ボトルネック：速度の決定要因となっている部分  
  ボトルネックを推測し、対策するのが効率的
- ホットスタートとコールドスタート
  - ホットスタート：アクセスが続き性能が安定した状態で計測
  - コールドスタート：起動直後の状態で計測
- 性能は環境によって変わる  
  - 測定環境：実環境とは異なるケースが多い => 精度が落ちる  
  => 実環境に近づけて測定する工夫が必要
    - Linux：tc（帯域制限／遅延）機能など
- 測定した性能をグラフ化する
  - グラフ化は必須
  - 複数の項目を同一グラフにすることも効果的
- 性能情報のモニタリング
  - 実運用のWebサイトの性能把握
    - 性能情報やログを保管
    - ユーザーに影響ない範囲でアクセスしレスポンス時間を測定
  - モニタリングツール
    - vmstat、iostat：一定時間おきにI/O、CPU、メモリなどリソース状況を出力
    - dsta：一定時間おきにリソースの使用状況を出力、機能が豊富
    - sar：sysstatパッケージをインストールすると10分おきにデータを取得し保存、sarコマンドで参照
    - munin：複数サーバーのデータを集約し、グラフ化できる
    - cacti、gri：snmpでデータを保存し、グラフ化する
    - influxdb：時系列データベース、柔軟にデータの種類が増やせる
  - stub_statusの設定と出力
    - stub_status：接続中のクライアント数などの情報をHTTPで取得できるnginxのモジュール
    - 設定例
      ```
        server {
          location /status {
            stub_status;
            access_log off;   # アクセスログは残さない
            allow 127.0.0.1;  # localhostのみ許可
            deny all;
          }
        }
      ```
    - 出力
      - Active connections：現在の接続数
      - server accepts handled request：nginxを起動してからの合計値
        - これまでのクライアント接続数
        - 処理できた接続数（上記と同じになるのが理想、異なれば多重度の見直し）
        - 処理できたレクエスト数（keep-Alive等でことなる）
      - Reading：ヘッダ受信待ちの接続数
      - Writing：レスポンス送信中の接続数
      - Waiting：Keep-Aliveなどで接続中でリクエスト待ちの接続数
  - レスポンス時間のログ出力：$request_time
    ```
      http {
        log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for" $request_time';

        access_log /var/log/nginx/access.log main;
      }
    ```
- Webブラウザで使える性能解析
  - 開発者ツール
    - リクエスト、レスポンスのタイミング情報
    - Webページの構造、スクリプトの解析
  - Yslow：ブラウザ拡張機能 => HTMLの構造まで性能解析ができる

### 8-2. 性能に影響する設定項目
- 同時接続数の設定
  - worker_connections：workerごとの最大接続数（クライアント＋バックエンド）
  - worker_processes：workerのプロセス数（auto => CPUのコア数）
  - worker_rlimit_nofile：同時に利用できるファイルディスクリプタ数のOS制限値変更
  - nginxの同時接続最大値 = worker_processes × worker_connections
- 同時接続数に関するOSの制限値
  - システム全体の設定値：`sysctil fs.file-max`
  - プロセスごとの設定値：`LimitNOFILE=`
- workerの動作方法に関する設定
  - worker_priority：OSのプロセススケジューリングの優先度nice値(-20～20)
  - worker_cpu_affinity：workerが使用するCPUコアを制限
  - /proc/プロセスID/oom_scope_ad：OOM(Out of Memory)Killerから守る設定(-1000～1000)
- ファイルやソケットのアクセス方法の設定
  - straceの使い方：システムコールの呼ばれ方を観測（ログ出力）する
    ```
      $ sudo apt install strace
      $ ps ax | grep nginx
      $ sudo strace -p プロセスID
    ```
  - wiresharkの使い方：パケットキャプチャ
    ```
      $ sudo apt install wireshark
      $ sudo tshark -i eth0 port 80
    ```
  - nginxのリクエスト処理の基本
    - イベント待ち
    - recv（ヘッダ受信）
    - stat（ファイル確認）
    - open（ファイル）
    - fstat（ファイルサイズ確認）
    - pread（データ読み込み）
    - writev（ヘッダとデータ送信）
    - write（アクセスログ書き出し）
    - close（ファイル）
    - close（ソケット）
  - sendfile：ファイルの中身をネットワークに転送するシステムコール
    ```
      server {
        sendfile on;
      }
    ```
    - writev => sendfile
  - sendfileとtcp_nopush
    - tcp_nopush：ヘッダのみが送信され、性能劣化することを防ぐ
      ```
        server {
          sedfile    on;
          tcp_nopush on;
        }
      ```
- ファイルキャッシュ
  - open_file_cache：特定のファイルへのアクセスが集中する場合に有効
    - stat（ファイル確認）        => 省略
    - open（ファイル）            => 省略
    - fstat（ファイルサイズ確認） => 省略
      ```
        server {
          open_file_cache         max=1000 inactive=60s;
          open_file_cache_valid   60s;
          open_file_cache_errors  on;
        }
      ```
- 非同期I/O
  - ネットワークI/O：default on
  - ファイルI/O：default off
    ```
      server {
        aio on;
      }
    ```
- ダイレクトI/O  
キャシュを使わずにストレージにアクセスする方法 => サイズの大きいファイルで有効
  ```
    server {
      aio
      directio           1m;  # 最小ファイルサイズ
      directio_alignment 4k;  # 揃える
      sendfile           on;
    }
  ```
- 帯域の制限  
  - limit_rate  
  特定のクライアントによる帯域の占有を防ぐ
    ```
      server {
        limit_rate       50k;   # 50KB/sに制限
        limit_rate_after 500k;  # 500KBまでは制限なし
      }
    ```

### 8-3. キャッシュによる性能向上
- リクエストヘッダによるキャッシュ制御
  - If-Modified-Since：その時刻以降に変更なければ「304 Not Modified」
  - If-None-Match：ETagヘッダの情報を基に変更を判断
- レスポンスヘッダによるキャッシュ制御
  - キャッシュの制御：`Cache-Controlヘッダ`
  - キャッシュの有効期限：`Expiresヘッダ`
  - コンテンツの更新：`ETagヘッダ`、`Last-Modified`
  - ヘッダの付与：expiresディレクティブ
  - 時刻情報の制御：`max`=>10年以上先、`@`をつけると1日の時刻
  - 設定例
    ```
      server {
        location / {
          etag    off;
          expires off;
        }
        location /images/ {
          etag    off;
          expires 10d;
        }
      }
    ```
    ```
      map $sent_thhp_content_type $expires {
        default         off;  # content-typeで期限を変える
        application/pdf 10d;
        ~image/         max;
      }
      server {
        expires $expires;
      }
    ```

### 8-4. 転送量の削減による性能向上
- 方法
  - 画像サイズや画質を調整
  - HTMLやスクリプトを最小化
  - HTTPのデータ圧縮 <= nginxでできる、gzip対応
- データ圧縮とデータ送信のトレードオフ
  - gzipによる圧縮 => sendfileは使えない
  - gzip_static => あらかじめ圧縮したファイル、sendfileは使える、管理煩雑
- データ圧縮のログ出力
  - $gzip_ratio = 圧縮前のサイズ / 圧縮後のサイズ
    ```
      server {
        gzip            on;
        gzip_min_length 20;
        gzip_types      text/* application/xml application/json;
        gzip_static     on;

        log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                        '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for" "gzip=$gzip_ratio"';

        access_log /var/log/nginx/access.log main;        
      }
    ```
  
### 8-5. 負荷分散やCDNの活用
  - 負荷分散
    - ロードバランサ
    - CDN（コンテンツデリバリネットワーク）
  - 回線速度 => ロードバランサ
  - CDNサービスの利用
  - 固定データ => Webサイトホスティング機能 AWS S3、Google Cloud Storage

## 第9章 セキュリティ
### 9-1. OSのセキュリティ設定
- ファイアウォールの設定  
サービスに必要な接続以外受け付けない設定
  - CentOS：firewalld
  - Ubuntu、Debian：ufw
- listenアドレスの設定  
アクセスを受け付けるIPアドレスを指定
  nginx：listenディレクティブで設定
- ログインの制限
- セキュアOSの使用  
  - SELinux、AppArmorなど
- 仮想マシン(VM)やコンテナによる機能の分離  
単機能のサーバーを組み合わせる設計にすると影響を最小限にできる  
サーバーのリソース分配の設定も容易になる
- ログや負荷の監視  
ログや負荷を記録し、異常検知 => 管理者に通知
- セキュリティアップデートの適用  
セキュリティアップデートの公開 = 脆弱性の公開  
アップデートは適宜適用する
- IPSやIDSの利用
  - IPS(Intrusion Prevention System)
  - IDS(Intrusion Detection System)  
  システムへの侵入やファイルの改ざんを検出できる

### 9-2. Webシステムのセキュリティ設計
- 接続ネットワーク
  - インターネット
  - 組織内ネットワーク
  - Webシステム自体が属する内部ネットワーク => DMZ（Demilitarized Zone：非武装地帯）
  - ファイアウォールの設定
    - 外部 => DMZ ： 特定のポートのみ許可
    - DMZ => 外部 ： 許可しない（被害の拡大を防ぐ）
    - 組織内 => 踏み台サーバー => DMZの構成は更に良い

### 9-3. Web Application Firewall(WAF)
- WAFの方式
  - ホワイトリスト方式
  - ブラックリスト方式
- nginxで使えるOSSのWAFモジュール
  - ModSecurity：Apache用をnginx、IISへ展開、Common Rule Set(CRS)が配布されている  
    - 標準モジュールなし => インストールにはビルドが必要
    - CRSを利用してルール設定
    - ModSecurityの有効化
      ```
        server {
          location / {
            ModSecurityEnabled on;
            ModSecurityConfig modsecurity.conf;
          }
        }
      ```
    - /etc/nginx/modsecurity.confの末尾に追加
      ```
        Include modsec.d/modsecurity_crs_10_setup.conf
        Include modsec.d/activated_rules/*.conf
      ```
  - Naxsi：nginx専用、自動学習などの機能に優れる

### 9-4. DoS対策
- DoS(Denial of Service)
  - DDoS(Distributed DoS)：攻撃元分散 => 大量のアクセス
- アクセス元IPアドレスごとの同時接続数を制限する
  ```
    http {
      limit_conn_zone $binary_remote_addr zone=addr:10m;
      server {
        limit_conn addr 10;
      }
    }
  ```
- アクセス元のIPアドレスごとのリクエスト数のスループットを制限する
  ```
    http {
      limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
      server {
        limit_req zone=one burst=5;
      }
    }
  ```
- リクエストごとの帯域を制限する
  ```
    server {
      limit_rate        50k;
      limit_rate_after  500k;
    }
  ```

## 第10章 モジュールの追加
### 10-1. nginxのモジュール
- モジュールのインストールフラグ：`nginx -V`  
  - 有効化：`--with-<モジュール名>`
  - 無効化：`--without-<モジュール名>`
- 有効化されているモジュール
  - コンパイルフラグに含まれない有効なモジュール
  |モジュール名|概要|
  |---|---|
  |http_access|アクセス元IPsドレスによるアクセス制限|
  |http_auth_basic|Basic認証|
  |http_autoindex|自動でインデックスページを生成|
  |http_browser|User-Agentヘッダによる動作の変更|
  |http_charset|Content-Typeレスポンスヘッダの制御|
  |http_empty_gif|1x1ピクセルの透過GIFファイルを提供|
  |http_fastcgi|FastCGIへの対応|
  |http_geo|アクセス元のIPアドレスを使った分類|
  |http_gzip|gzip圧縮|
  |http_limit_conn|接続数によるアクセス制限|
  |http_limit_req|スループット制限|
  |http_map|任意の文字列キーに使った分類|
  |http_memcached|memcachedへの問い合わせ結果をレスポンスに使う|
  |http_proxy|リバースプロキシ機能|
  |http_referer|Refererヘッダによる動作の変更|
  |http_rewrite|リクエストの書き換え|
  |http_scgi|SCGIへの対応|
  |http_split_clients|任意のキーをハッシュにかけ、指定された割合で振り分ける、A/Bテスト|
  |http_ssi|SSI(Server Side Include)への対応|
  |http_upstream_hash|ロードバランスの分散に使うハッシュ|
  |http_upstream_ip_hash|ロードバランスの分散に使うIPハッシュ|
  |http_upstream_keepalive|ロードバランスのアクセスでkeep-aliveを使用|
  |http_upstream_least_conn|ロードバランスの分散に最小コネクション数を使用|
  |http_upstream_zone|ロードバランサのバックエンドのゾーンの定義|
  |http_userid|Cookieを使ったクライアント識別|
  |http_uwsgi|uwsgiへの対応|
  |mail_imap/mail_pop/mail_smtp|メール関連のモジュール|
  |poll/select|イベント待ち関連のモジュール|
  |stream_access|TCPストリームのロードバランサで、アクセス元のIPアドレスによるアクセス制限|
  |stream_geo|TCPストリームのロードバランサで、アクセス元のIPアドレスによる動作の変更|
  |stream_limit_conn|TCPストリームのロードバランサで、キーごとの接続数の制限|
  |stream_map|TCPストリームのロードバランサで、任意のキーによる動作の変更|
  |stream_return|TCPストリームのロードバランサで、特定の文字列を返して切断する機能|
  |stream_split_clients|TCPストリームのA/Bテスト用モジュール|
  |stream_upstream_hash|TCPストリームのロードバランサで、バックエンド選択に使うハッシュ|
  |stream_upstream_least_conn|TCPストリームのロードバランサで、分散に最小コネクション数を使用|
  |stream_upstream_zone|TCPストリームのロードバランサで、バックエンドのゾーン定義|
  - コンパイルフラグに含まれる有効なモジュール
  |モジュール名|概要|
  |---|---|
  |http_addition|レスポンス前後へのコンテンツの挿入|
  |http_auth_request|バックエンドでの認証|
  |http_dav|WebDAVへの対応|
  |http_flv|FLV(Flash Video)ファイルの擬似的なストリーミング|
  |http_gunzip|gzip圧縮されたファイルを伸長してクライアントに返却|
  |http_gzip_static|gzip圧縮済みのファイルを圧縮転送に利用|
  |http_mp4|MPEGファイルの擬似的なストリーミング|
  |http_realip|リバースプロキシでアクセス元IPアドレス情報のバックエンドへの転送|
  |http_secure_link|時間制限付きのURLを生成|
  |http_slice|Rangeヘッダを使ったバックエンドへのリクエストの分割|
  |http_ssl|SSL/TLSへの対応|
  |http_stub_status|簡易な統計情報の提供|
  |http_sub|文字列変換によるコンテンツの書き換え|
  |http_v2|HTTP/2への対応|
  |mail|メールのリバースプロキシ対応|
  |mail_ssl|メール機能のSSL/TLSへの対応|
  |stream|TCPストリームのロードバランサ機能|
  |stream_ssl|TCPストリームのロードバランサのSSL/TLSへの対応|
  |stream_ssl_preread|TCPストリームのSSL/TLSロードバランサのSNIへの対応|
  - 追加でインストールできるダイナミックモジュール
  |モジュール名|パッケージ名|概要|
  |---|---|---|
  |http_geoip|nginx-module-geoip|IPアドレスから位置情報を求めるGeoIPデータベースの利用|
  |stream_geoip|nginx-module-geoip|TCPストリームのロードバランサでのGeoIPデータベースの利用|
  |http_image_filter|nginx-module-image-filter|画像の変換機能の提供|
  |http_js|nginx-module-njs|nginxの設定にJavaScriptを使用|
  |http_perl|nginx-module-perl|nignxの設定やSSIにPerlを使用|
  |http_xslt_filter|nginx-module-xlst|XSLTを使ってXMLを変換|
    - ダイナミックモジュールのインストール
      ```
        $ sudo apt install <パッケージ名>
      ```
    - ダイナミックモジュールの利用  
    nginxの設定ファイルの先頭付近で`load_moduleディレクティブ`で読み込む

### 10-2. サードパーティモジュールのインストール  
- サードパーティモジュール
  - nginx本体のソースファイルを展開
  - コンパイルオプションの追加
  - nginx本体と一緒にコンパイルし直す  
  [サードパーティモジュール](https://www.nginx.com/resources/wiki/modules/)
- ダイジェスト認証モジュールのインストール
  - gccのインストールとソースコードのダウンロード
  - コンパイル（ダイジェスト認証モジュール追加版）
  - インストール（ダイジェスト認証モジュール追加版）
  - 起動スクリプトの作成
  - nginxサービスの起動
- ダイナミックモジュールとしてインストール（Githubに記載の方法）
  - ダイナミックモジュールとしてコンパイル
  - モジュールファイルのインストール
  - モジュールの有効化
  - モジュール読み込み時のエラーメッセージ
- ダイジェスト認証の設定
  - モジュール付属のhtdigest.pyスクリプトを利用した場合
  - htdigestツールを利用した場合

## 第11章 HAクラスタ構成
### 11-1. クラスタとは
- クラスタ：同じように動作する複数のサーバーを使い、サービスを提供するシステム
  - 負荷分散クラスタ：負荷分散で性能を向上する
  - HA(High Availability)クラスタ：冗長性を持たせて可用性を上げる
    - 稼働系
    - 待機系
  - HPC(High Performance Computing)クラスタ：スーパーコンピュータ

### 11-2. 冗長構成の考え方
- システムの安定性
  - 信頼性(Reliability) ： 障害が発生しにくく、安定して動作し、壊れにくい
  - 可用性(Availability) ： 利用できる時間が長く、逆に利用できなくなる時間は短い
  - 保守性(Serviceability) ： 状況把握や不具合修正がしやすい
- 計算式
  - 平均故障間隔：MTBF(Mean Time Between Failures)
  - 平均修復時間：MTTR(Mean Time To Repair)
  - 稼働率 = MTBF / (MTBF + MTTR)  
  初期不良や経年劣化故障バラツキを考慮していないので、計算値よりも実際の稼働率は低くなりがち
  - 単一障害点(SPoF:Single Point of Failure)：弱い部分

### 11-3. Linuxで使えるクラスタ構築用のソフトウェア
- ロードバランサ（負荷分散）：DNSラウンドロビン
- ロードバランサ（冗長化）：pacemaker、keepalived
- Webサーバー（負荷分散）：LVS(Linux virtual Server)
- Webサーバー（負荷分散・冗長化）：nginx、haproxy、Apache

### 11-4. ロードバランサの冗長化構成
- VIP(Virtual IP:仮想IP)：複数のサーバーで引き継いで使えるIPアドレス
- VIPによる可用性の向上
  - pacemaker
  - keepalived
    - インストール
    - クラスタの設定
    - OS起動時の設定

### 11-5. ロードバランサの負荷分散
- DNSラウンドロビン
  - DNSラウンドロビンのRule9問題
    - RFC3484で規定(Default Address Selection for Internet Protocol version6(IPv6))
    - IPv4でも同じアルゴリズムで動作
    - 9番目のルール：アクセス元のIPアドレスと最長一致を見て優先する宛先を決める  
    => 負荷分散クラスタに属するノードを全て同じネットワークに配置  
    => ネットワークの回線速度が問題 => CDNサービスの利用

### 11-6. ロードバランサの負荷分散と冗長化の共存
- Active-Stanby：稼働系と待機系
- Active-Active：普段、両方使いつつ、冗長化も行う構成
  - VIPを2つ用いてロードバランサの冗長化が可能

## 第12章 Webシステムのメンテナンス
### 12-1. メンテナンスの考え方
- nginx（無停止でできる）
  - nginxの設定変更
  - SSL/TLSの証明書の更新
  - バージョンアップ
- バックエンドのWebサーバー：ロードバランサの設定で切り替え
- ロードバランサのOS更新：クラスタソフトウェアによる切り替え
- 対応できないケース：メンテナンス画面の表示

### 12-2. サービス停止を伴うメンテナンス
- メンテナンスの流れ
  - メンテナンス開始
  - メンテナンスページに切り替え：503(Service temporarily Unavailable)
  - メンテナンス作業
  - テストページに切り替え：外部=>503、内部=>通常ページ
  - テスト
  - 通常ページに戻す
  - メンテナンス終了
- 設定は3つのファイルの切り替えが良い
  - normal.conf
  - mentenance.conf
  - test.conf

### 12-3. nginxによる切り替えを使った無停止でのメンテナンス
- メンテナンスの流れ
  - メンテナンス開始
  - ホストAを外す＋リロード
  - メンテナンス作業
  - ホストAを戻す＋ホストBを外す＋リロード
  - メンテナンス作業
  - ホストBを戻す＋リロード
  - メンテナンス終了

### 12-4. クラスタソフトウェアによる切り替えを使った無停止のメンテナンス
- メンテナンスの流れ
  - メンテナンス開始
  - 待機系 => standby
  - メンテナンス作業
  - 待機系 => unstandby
  - 稼働系 => standby
  - メンテナンス作業
  - 稼働系 => unstandby
  - メンテナンス終了