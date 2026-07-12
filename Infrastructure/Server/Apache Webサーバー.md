# Apache webサーバー 改定版
## 第1章 Apacheとwebサーバーを知ろう
### 1-1. Apacheって何？
- 世界で一番使われているWebサーバー（2021年8月時点では、2位：25%、1位：nginx）
- バージョン2.4が最新（2021年8月時点）

## 第2章 Apacheを動かそう
### 2-1. インストール
- [Apche公式](http://www.apache.org/)
- [日本語ドキュメント](http://httpd.apache.org/docs/2.4/ja/)
- インストール方法は環境に合わせて、ネットで調べる

### 2-4. Apacheを起動しよう
- 環境ごとに違うので、ネットで調べる
- Ubuntu20.04LTS
```
起動：sudo service apache2 start
終了：sudo service apache2 stop
```

## 第3章 ページを見せよう
### 3-1. 設定ファイルを見てみよう
- 環境ごとに設定ファイルの場所は異なる

### 3-2. 設定ファイルの構造を知ろう
- 設定ファイル
  - 全体の設定
  - 範囲の設定
    - セクションコンテナ：範囲を表す
    ```
      <Directory />
        Options FollowSymLinks
        AllowOverride None
        Require all denied
      </Directory>

      <Directory /usr/share>
        AllowOverride None
        Require all granted
      </Directory>

      <Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
      </Directory>

      <FilesMatch "^\.ht">
        Require all denied
      </FilesMatch>
    ```

- 優先順位
  - ディレクトリ、ファイル名、URLの順番で解釈される
  - 同じセクションコンテナは順番で解釈される
  - ディレクトリ：正規表現なし、正規表現ありの順番（ディレクトリ名が短い順番）
  - 競合：後で読み込みで上書き

- ディレクトリ単位で指定：`<Directory>`
  - ファイルシステムのディレクトリ名に対応
  - パスはフルパスで指定
  - パス名のサブディレクトリ全体に及ぶ
  - *?等のワイルドカード、正規表現、~（チルダ）も使用可能：`<FirectoryMatch>`

- ファイル単位で指定する：`<Files>`
  - ファイルシステムのファイル名に対応
  - *?等のワイルドカード、正規表現、~（チルダ）も使用可能：`<FilesMatch>`
  - 「.htaccess」など、決まったファイル名へのアクセス制限などで便利

- パス名で指定する：`<Location>`
  - URLのパス名に対応
  - *?等のワイルドカード、正規表現、~（チルダ）も使用可能：`<LocationMatch>`
  - 特定のURLのみでCGIやモジュールを有効にする場合に便利

- 他の設定ファイルを読み込む：`<Include>`
  - *?等のワイルドカードやディレクトリ名を使用可能
  - モジュールごとに設定ファイルをわけるときなどに便利

### 3-3. 初期設定をしよう
- ドキュメントルート：webページの起点
  - Ubuntu20.04LTS
    - /etc/apache2/sites-enabled/000-default.conf
    ```
      DocumentRoot /var/www/html
    ```

### 3-4. htmlファイルを置こう
- /var/www/htmlにindex.thmlを置く

### 3-5. 設定を反映させる
- 再起動しないと設定変更は反映されない
  - Ubuntu20.04LTS
  ```
    再起動：sudo service apache2 restart
  ```

### 3-6. アクセスの記録を確認しよう
- ログファイル
  - /var/log/apache2/access.log
  - /var/log/apache2/error.log

### 3-7. インデックスファイルを設定しよう
- DirectoryIndex
  - インデックスファイルの設定
  - Ubuntu20.04LTS
    - /etc/apache2/mods-available/dir.conf
    ```
      <IfModule mod_dir.c>
        DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
      </IfModule>
    ```

### 3-8. ほかの場所にあるファイルを見せるには
- Alias
  - Document root以外のファイルを見せる
  - http://172.21.223.250/icons/apache_pb.png
  - Ubuntu20.04LTS
    - /etc/apache2/mods-available/alias.conf
    ```
      Alias /icons/ "/usr/share/apache2/icons/"
    ```

### 3-9. 文字コードの設定を変更しよう
- AddDefaultCharset
  - 文字コードの設定
  - Ubuntu20.04LTS
    - /etc/apache2/conf-available/charset.conf
    ```
      #AddDefaultCharset UTF-8 ←ubuntuでは、設定されていないのがデフォルト
    ```

## 第4章 公開ディレクトリを追加しよう
### 4-1. ユーザーごとの公開ディレクトリを用意しよう
- ユーザーディレクトリを有効にする
  - ubuntu20.04LTS
  ```
    sudo a2enmod userdir
  ```

- ユーザーディレクトリーを確認する
  - /etc/apache2/mods-enabled/userdir.conf
    ```
      UserDir public_html
    ```

- ユーザーディレクトリをホームディレクトリ上に作る
  ```
    mkdir ~/public_html
    chmod 701 ~/public_html
    chmod +x ~
  ```

- public_html内にindex.htmlを作成する

- ユーザーごとの許可や禁止
  - /etc/apache2/mods-enabled/userdir.conf
    ```
      UserDir public_html
      UserDir enabled root ←許可
      UserDir disabled root ←禁止
    ```

### 4-2. ディレクトリごとの設定ファイルを使うには
- .htaccessファイル：元の設定を上書きできる
  - Ubuntu20.04LTS
    - /etc/apache2/mods-enabled/userdir.conf
      ```
        	<Directory /home/*/public_html>
            AllowOverride All ←追記
            # AllowOverride FileInfo AuthConfig Limit Indexes
            # Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
            # Require method GET POST OPTIONS
          </Directory>
      ```
      - AllowOverride All
        - サーバー負荷増大：必要ない、パフォーマンス重視の時は設定しない

### 4-3. 複数のサーバーに見せるには
- 仮想ホスト：複数のサイトを1つのサーバーで運営するための機能
  - 名前ベースの仮想ホスト：リクエストヘッダー内 Host:ヘッダー
  - IPベースの仮想ホスト：ネットワークカード(NIC)を複数持ち、異なるIPアドレスで運用

- 仮想ホスト（名前ベース）を設定する
  - Ubuntu20.04LTS
    - `sudo vi sites-available/<ドメイン名>.conf`
    - <ドメイン名>.conf
      ```
        <VirtualHost *:80>
          ServerName <ドメイン名>
          DocumentRoot /home/qzs04130/public_html
          ServerAdmin webmaster@<ドメイン名>
        </VirtualHost>
      ```
    - `sudo a2ensite <ドメイン名>`
  - hostsファイルの編集
    - C:\Windows\System32\drivers\etc

### 4-4. 仮想ホストに別名を付けるには
- ServerAlias：ホストに別名をつける
  - Ubuntu20.04LTS
    - <ドメイン名>.conf
      ```
        <VirtualHost *:80>
          ServerName <ドメイン名>
          ServerAlias <ドメイン別名>
          DocumentRoot /home/qzs04130/public_html
          ServerAdmin webmaster@<ドメイン名>
        </VirtualHost>
      ```
  - hostsファイルの編集
    - C:\Windows\System32\drivers\etc

- IPベースの仮想ホストに名前をつける
  - `_default_`で設定：いずれの仮想ホストにも該当しなかった場合適用される

### ホスト名を指定しないブラウザに対応するには
- ServerPathによる仮想ホストの識別  
  - Ubuntu20.04LTS
    - <ドメイン名>.conf
      ```
        <VirtualHost *:80>
          ServerName <ドメイン名>
          ServerAlias <ドメイン別名>
          DocumentRoot /home/qzs04130/public_html
          ServerAdmin webmaster@<ドメイン名>
          ServerPath /<パス名>/
        </VirtualHost>
      ```

## 第5章 アクセスを制御しよう
### 5-1. アクセス元を制限するには
- アクセス元に対して禁止や許可を設定する：`<Directory>`や`.htaccess`に書くことで設定できる
- Apache2.2
  - Ubuntu20.04LTS
    - `sudo vi sites-available/000-default.conf`
      - 000-default.conf
      ```
        <VirtualHost *:80>
          <Directory /var/www/html> ←ファイルパス名
            Order deny,allow  ← 後のものが優先：この場合、禁止→許可=ホワイトリスト
            Deny from all   ← 全部禁止
            Allow from 172.21.208.1 ← 172.21.208.1のみ許可
          </Directory>
        </VirtualHost>
      ```
  - 記述例
  ```
    ブラックリスト
      Order allow,deny
      Allow from all
      Deny from 172.21.208.1 
    ホワイトリスト
      Order deny,allow
      Deny from all
      Allow from 172.21.208.1 
      Allow from impress.co.jp ← ドメイン指定も可
  ```
    - `Allow from`、`Deny from`は複数書くことが可
      - AllowとDenyはOrderの記述順序で適用
      - 複数のAllow、DenyはOR条件
    - all：全て（通常は優先順位の低い方をall）
    - IPアドレス：IPアドレスで指定
    - IPアドレスの省略：192.168.1 => 192.168.1.0～192.168.1.255
    - IPアドレスとネットマスク：192.168.1.0/255.255.255.0 => 192.168.1.0～192.168.1.255
    - IPアドレスとネットマスクのビット数：192.168.1.0/24 => 192.168.1.0～192.168.1.255
    - ホスト名やドメイン名の一部：FQDN（ホスト名）で指定 ex)impress.co.jp

- Apache2.4
  - Ubuntu20.04LTS
    - `sudo vi sites-available/000-default.conf`
      - 000-default.conf
      ```
        <VirtualHost *:80>
          <Directory /var/www/html> ←ファイルパス名
            <RequireAll>
              Require all granted
            </RequireAll>
          </Directory>
        </VirtualHost>
      ```
  - 記述例
  ```
    ブラックリスト
      #Require all granted
      Require not ip 192.168.1.28 
    ホワイトリスト
      #Require all granted
      Require ip 192.168.1.28 
      Require host impress.co.jp ← ドメイン指定も可
  ```
    - `Require`ディレクティブを採用
      - `Allow`、`Deny`、`Order`ディレクティブは廃止
      - `<RequireAll>`：全ての条件に一致する
      - `<RequireAny>`：どれか一つの条件に一致する
      - `<RequireNone>`：全ての条件に一致しない
    - all：`Require all granted`、`Require all denied` 
    - IPアドレス：IPアドレスで指定 `Require ip`と記述
    - IPアドレスの省略：192.168.1 => 192.168.1.0～192.168.1.255
    - IPアドレスとネットマスク：192.168.1.0/255.255.255.0 => 192.168.1.0～192.168.1.255
    - IPアドレスとネットマスクのビット数：192.168.1.0/24 => 192.168.1.0～192.168.1.255
    - ホスト名やドメイン名の一部：FQDN（ホスト名）で指定 ex)impress.co.jp `Require host`と記述
    - `Require loacl`：loaclhost（同じマシン）に限定

### 5-2. Webブラウザの種類を制限するには
- Apacheの環境変数：User-Agent（ブラウザ情報）を制限する
- Apache2.2
  - Ubuntu20.04LTS
    - `sudo vi sites-available/000-default.conf`
      - 000-default.conf
      ```
        <VirtualHost *:80>
          <Directory /var/www/html>
            BrowserMatch "Mozilla" mozilla
            Order Deny,Allow
            Deny from all
            Allow from env=mozila
          </Directory>
        </VirtualHost>
      ```

- Apache2.4
  - Ubuntu20.04LTS
    - `sudo vi sites-available/000-default.conf`
      - 000-default.conf
      ```
        <VirtualHost *:80>
          <Directory /var/www/html>
            <RequireAll>
              Require all denied
              Require expr %{HTTP_USER_AGENT} =~ /Mozilla/
            </RequireAll>
          </Directory>
        </VirtualHost>
      ```

### 5-3. リンクを制限するには
- リファラー：リンク元の情報
  - 自サイト以外からの画像の直リンク禁止など
  - リファラーは、ブラウザや設定で送られないケースもあり、閲覧不可となる
  - wgetはヘッダーを自由に設定できる
- Apache2.2
  - Ubuntu20.04LTS
    - `sudo vi sites-available/000-default.conf`
      - 000-default.conf
      ```
        <VirtualHost *:80>
          <Directory /var/www/html>
            SetEnvIf Referer "www\.dekiru\.jp" dekiru
            Order Deny,Allow
            Deny from all
            Allow from env=dekiru
          </Directory>
        </VirtualHost>
      ```

- Apache2.4
  - Ubuntu20.04LTS
    - `sudo vi sites-available/000-default.conf`
      - 000-default.conf
      ```
        <VirtualHost *:80>
          <Directory /var/www/html>
            SetEnvIf Referer "www\.dekiru\.jp" dekiru
            <RequireAll>
              Require all denied
              Require env dekiru
            </RequireAll>
          </Directory>
        </VirtualHost>
      ```

### 5-4. エラーメッセージを変更するには
- ステータスコード：3桁の数字でレスポンス状況を知らせる
  - Ubuntu20.04LTS
    - `sudo vi conf-available/localized-error-pages.conf`
      - localized-error-pages.conf
      ```
        ErrorDocument 404 /404.html
        ErrorDocument 404 "直接メッセージを書くことも可能"
      ```

### 5-5. パスワードをかけるには
- Apacheで使用できる認証
  - 基本認証：htpasswdコマンドを使用
  - DB認証：mod_authn_dbm
  - LDAP認証：mod_authnz_ldap
  - ダイジェスト認証：mod_digest、htdigestコマンドを使用
- 基本認証：ユーザー名とパスワード
  - Authorization:ヘッダーにユーザー名とパスワード付加し、認証
    - ログアウトできない（ブラウザの再起動で再度認証）
    - httpでは盗聴に注意
    - 個人ディレクトリ=> .htpasswdなどでファイルを作成し、.htaccessで管理  
    .htで始まるファイルは見られない設定  
    - 数が多くなったら、mod_auth_dbmなどでDB管理も可能
- 認証ファイルを作成 => `/etc/apache2/sites-available`
  - `htpasswd -c passwd muraken` => `htpasswd -c <設定ファイル名> <ユーザー名>`
    - `New password:`、`Re-type new password:`を入力
- 設定ファイルの編集 => `sudo vi sites-available/000-default.conf`
  ```
    <Directory "var/www/html">
      AuthType Basic ←基本認証
      AuthName "Password for www.dekiru.jp"
      AuthUserFile /etc/apache2/sites-available/passwd ←上記で作成したファイル
      Require valid-user ←許可ユーザー
    </Directory>
  ```

### 5-6. 認証してグループ単位で許可するには
- ユーザーグループ：テキストファイルで管理できるので楽
  - ユーザーグループファイルの作成 => `/etc/apache2/sites-available`
    - `vi groupauth` ←名前は任意
    ```
      dekiru: muraken
    ```
- 設定ファイルの編集 => `sudo vi sites-available/000-default.conf`
  ```
    <Directory "var/www/html">
      AuthType Basic ←基本認証
      AuthName "Password for www.dekiru.jp"
      AuthUserFile /etc/apache2/sites-available/passwd
      AuthUserFile /etc/apache2/sites-available/groupauth ←上記で作成したファイル
      Require group dekiru ←許可ユーザー
    </Directory>
  ```

### 補足：モジュールの有効化
- `Invalid command 'AuthGroupFile'` が出た理由 => AuthGroupFileのモジュールが有効化されていない
  - モジュールの有効化：`sudo a2enmod <モジュール名>`
  - モジュールの無効化：`sudo a2dismod <モジュール名>`
    - モジュール名無しの場合、対応するモジュール名の一覧が出る（Interractiveモード）

### 5-7. アクセス制限と認証を組み合わせるには
- Apache2.2
  ```
    <Directory /var/www/html>
      Order deny,allow
      Deny from all
      Allow from impress.co.jp
    
      AuthType Basic
      AuthName "Password for www.dekiru.jp"
      AuthUserFile /etc/apache2/sites-available/passwd
      Require valid-user

      Satisfy all ←AND条件
      Satisfy any ←OR条件
    </Directory>
  ```

- Apache2.4
  ```
    <Directory /var/www/html>
      AuthType Basic
      AuthName "Password for www.dekiru.jp"
      AuthUserFile /etc/apache2/sites-available/passwd
      <RequireAll> ←AND条件
        Require host impress.co.jp
        Require valid-user
      </RequireAll>
      <RequireAny> ←OR条件
        Require host impress.co.jp
        Require valid-user
      </RequireAny>
    </Directory>
  ```

### 5-8. URLが変更になったときは
- リダイレクト：レスポンス300番台＋Location:ヘッダー
  - 設定方法 => `sudo vi sites-available/000-default.conf`
    - 一時的（302 Found）
      ```
        Redirect /test/ http://www.dekiru.jp/~qzs04130/
        Redirect <アクセス先> <リダイレクト先>
      ```  
    - 永続的（301 Moved Permanently）
      ```
        Redirect permanent /test/ http://www.dekiru.jp/~qzs04130/
        Redirect permanent <アクセス先> <リダイレクト先>
      ```
    - 応用（正規表現）
      ```
        Redirect ^/test/(.*)$ http://www.dekiru.jp/~qzs04130/$1 ←(.*)が$1に代入される
        Redirect <アクセス先> <リダイレクト先>
      ```

### 5-9. ページで言語を自動で切り替えるには
- コンテントネゴシエーション：リクエストヘッダー => 文字コード、データタイプを切り替える
  - Accept-Language
  - Accept-Charset
  - Accept-Encoding
- 設定方法 => `sudo vi sites-available/000-default.conf`
  ```
    <Directory "/var/www/html">
      Options FollowSymLinks MultiViews
    </Directory>      
  ```  
  - Addtypeの設定：`mods-available/mime.conf`
    ```
      AddLanguage ja .ja
      AddCharset shift_jis .sjis
    ```
  - `/var/www/html`に <ファイル名>.html.jaなどを置き、アクセスする

### 5-10. サーバーの種類を隠すには
- サーバー情報、エラー情報は、攻撃の対象となる可能性を上げる場合がある。（固有の脆弱性など）
  - ServerTokens：サーバー情報を隠す  
    |ServerToken|表示|
    |---|---|
    |Prod|Apach|
    |Major|Apach/2|
    |Minor|Apach/2.4|
    |Minimal|Apach/2.4.41|
    |OS :default|Apach/2.4.41(Ubuntu)|
    |Full|Apach/2.4.41(Ubuntu)|  
  - ServerSignature：エラー情報を隠す
    |ServerToken|表示|
    |---|---|
    |On :default|表示あり|
    |Off|表示なし|
    |EMail|表示あり＋管理者メールリンク|  
  - 設定方法 => `/etc/apache2/conf-available/security.conf`
    ```
      ServerTokens OS
      ServerSignature On
    ```

### 5-11. データの種類を追加するには
- AddType：拡張子とMIMEタイプを表で管理
  - 設定方法 => `mods-available/mime.conf`
    ```
      AddType application/epub+zip .epub
    ```
  - デフォルト設定 => `/etc/mime.types`

### ロボットからのアクセスを制限するには
- robot.txt：クローラーへの指定（強制力はない）
  ```
    User-agent: * ←全てのロボット
    Disallow /cgi-bin/
    Disallow /data/
    Disallow /~pub/
  ```  
  - robot.txtからの情報漏れにも注意
- HTMLのmetaタグによる指定
  ```
    <meta name="ROBOT" content="NOINDEX">     登録しない
    <meta name="ROBOT" content="NOFOLLOW">    リンクを辿らない
    <meta name="ROBOT" content="NOARCHIVE">   キャッシュに保存しない
    <meta name="ROBOT" content="NOINDEX,NOFOLLOW,NOARCHIVE">  まとめて指定
  ```

## 第6章 CGIを使おう
### 6-1. CGIってなに
- CGI:Common Gateway Interface  
Webサーバーが外部プログラムを呼び出して、ユーザーの入力に応じたページを表示する。  
例）検索、掲示板、ブログなど => Perl、PHP、Rubyなど
  - 通常  
  ```
    ユーザー → Apache → ファイル
             ←        ←
  ```  
  - CGI  
  ```
    ユーザー → Apache → CGIプログラム
             ←        ← 実行結果をHTML
  ```  
  - CGIプログラムのセキュリティ
    - 負荷増大
    - 意図しないデータによる不正処理 <= 入力データのサニタイズ（無効化）

### 6-2. CGIを使えるようにするには
- ScriptAlias：CGIスクリプトが単純なファイルとして公開されないようにする
- AddHandler：実行すべきファイルの拡張子を設定
- CGI用ファイルの置き場
  - 公開ディレクトリに置かない
  - ScriptAliasでCGIとして公開
- Ubuntu20.04LTS
  - `/etc/apache2/conf-available/serve-cgi-bin.conf`
    ```
          ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
      <Directory "/usr/lib/cgi-bin">
        AllowOverride None
        Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
        Require all granted
      </Directory>
    ```
  - `/etc/apache2/mods-available/mime.conf`
    ```
      AddHandler cgi-script .cgi
    ```
  - `a2enmod cgid`コマンドでcgi設定をONする

### 6-3. CGIを使ってみよう
- CGIスクリプトを置く => `/usr/lib/cgi-bin`
  ```
    #!/usr/bin/perl
    print "Content-type: text/html\r\n";
    print "\r\n";
    print "<html>\r\n";
    print "<body>\r\n";
    print "<p>Hello, users!</p>";
    print "</body>\r\n";
    print "</html>\r\n";
  ```
- 実行権限を与える => `chmod +x hello.cgi`
- クライアントでアクセス => `www.dekiru.jp/cgi-bin/hello.cgi`

### 6-4. ユーザーディレクトリでCGIを使うには
- 設定を変更する
  ```
    ScriptAliasMatch ^/~([a-zA-Z0-9_-]+)/cgi-bin/(.*) /home/$1/cgi-bin/$2
    <Directory /home/*/cgi-bin/>
      options +ExecCGI
    </Directory>
  ```
- スクリプトを置く
  ```
    $mkdir ~/cgi-bin
    $chmod 701 ~/cgi-bin
    hello.cgiを上記ディレクトリに配置
  ```
- 実行権限を与える => `chmod +x ~/cgi-bin/hello.cgi`
- クライアントでアクセス => `www.dekiru.jp/~<ユーザー名>/cgi-bin/hello.cgi`

### 6-5. 状況に応じて動作させるには
- 環境変数の利用：CGIから状況を知ることができる
- CGIスクリプトを置く => `/usr/lib/cgi-bin`
  ```
    #!/usr/bin/perl
    print "Content-type: text/html\r\n";
    print "\r\n";
    print "<html>\r\n";
    print "<body><p>\r\n";
    while(($key, $val) = each %ENV) {
      print "$key: $val<br>\r\n";
    }
    print "</p></body>\r\n";
    print "</html>\r\n";
  ```
- 実行権限を与える => `chmod +x env.cgi`
- クライアントでアクセス => `www.dekiru.jp/cgi-bin/env.cgi`
- 環境変数の一覧を表示
|環境変数|意味|
|---|---|
|DOCUMENT_ROOT|サーバーのドキュメントルートフォルダ|
|SERVER_SOFTWARE|Apacheのバージョンなど|
|AUTH_TYPE|認証方式|
|CONTENT_TYPE|リクエストのMIMEタイプ|
|CONTENT_LENGTH|リクエストの長さ|
|HTTP_ACCEPT|Accept:ヘッダーの値|
|HTTP_ACCEPT_CHARSET|Accept_Charset:ヘッダーの値|
|HTTP_ACCEPT_LANGUAGE|Accept_Language:ヘッダーの値|
|HTTP_HOST|HOST:ヘッダーの値|
|HTTP_REFERER|Referer:ヘッダーの値|
|HTTP_USER_AGENT|User-Agent:ヘッダーの値|
|QUERY_STRING|GETメソッドで指定された引数|
|REMOTE_ADDR|クライアントのIPアドレス|
|REMOTE_USER|認証で指定されたユーザー名|
|REQUEST_METHOD|メソッド|
|REQUEST_URI|リクエストのパス|

### 6-6. クライアントからデータを受け取るには
- GET：データはURLの一部としてCGIに渡す。長さに制限あり
  - `input_get.cgi`
    ```
      #!/usr/bin/perl
      print "Content-type: text/plain\r\n";
      print "\r\n";
      $data = $ENV{'QUERY_STRING'};
      $data =~ tr/+/ /;
      $data =~ s/%([0-9A-Fa-f][0-9A-Fa-f])/pack('H2',$1)/eg;
      print $data;
    ```
  - 実行権限を与える => `chmod +x input_get.cgi`
  - `input_get.html`
    ```
      <html>
      <body>
      <form method="GET" action="/cgi-bin/input_get.cgi">
        <p>名前： <input type="text" name="name" size=20></p>
        <p>郵便番号： <input type="text" name="zipcode" size=10></p>
        <p><input type="submit" value="送信" name="submit"></p>
      </form>
      </body>
      </html>
    ```
  - 送信データ
    ```
      name=hide&zipcode=123-4567&submit=送信
    ```
- POST：データはリクエストの中に含まれて渡される。データ量の制限なし。
  - `input_post.cgi`
    ```
      #!/usr/bin/perl
      print "Content-type: text/plain\r\n";
      print "\r\n";
      read (STDIN, $data, $ENV{'CONTENT_LENGTH'});
      $data =~ tr/+/ /;
      $data =~ s/%([0-9A-Fa-f][0-9A-Fa-f])/pack('H2',$1)/eg;
      print $data;
    ```
  - 実行権限を与える => `chmod +x input_post.cgi`
  - `input_post.html`
    ```
      <html>
      <body>
      <form method="POST" action="/cgi-bin/input_post.cgi">
        <p>名前： <input type="text" name="name" size=20></p>
        <p>郵便番号： <input type="text" name="zipcode" size=10></p>
        <p><input type="submit" value="送信" name="submit"></p>
      </form>
      </body>
      </html>
    ```
  - 送信データ
    ```
      name=hide&zipcode=123-4567&submit=送信
    ```

### 6-7. クライアントにデータを覚えさせるには
- Cookie：ユーザーのマシン上のファイルにCookieを保存させることができる（単純なテキスト）
- Cookieの実現手順
  - CGIからのメッセージに`Set-Cookie:`というヘッダーでCookieを保存
  - 次回以降、同じCGIにアクセスする場合はCookieの値を送信する
  - ユーザー設定でCookieを保存しない設定も可能 => 送信されない場合もある
    ```
      クライアント  サーバー
      => GET / HTTP/1.1
      <= HTTP/1.1 200 OK
         SetーCookie: count=1
      => GET / HTTP/1.1
         Cookie: count=1
    ```
  - Set-Cookie:ヘッダーの項目
    ```
      count=1; expires=Tue, 07-Oct-2012 20:08:14 GMT; domain=www.dekiru.jp; path=/;
    ```
    |項目|内容|
    |---|---|
    |名前=値|「名前=値」をクライアントに覚えさせる|
    |Expires=日付|Cookieの有効期限|
    |Domain=ドメイン名|サーバーのドメイン名。省略＝サーバーのホスト名|
    |Path=パス名|サーバーのパス名。省略＝現在のURLパス|
    |secure|指定時はHTTPSによる通信時のみ送信|
  - `count.cgi`
    ```
      #!/usr/bin/perl
      my $count = 1;
      foreach my $c (split(/; */, $ENV{'HTTP_COOKIE'})){
        if($c =~ /^count=(.*)$/){
          $count = $1;
          last;
        }
      }
      print "Content-type: text/html\r\n";
      printf "Set-Cookie: count=%d\r\n", $count + 1;
      print "\r\n";
      print "<html>\r\n";
      print "<body>\r\n";
      print "<p>Your access count: $count</p>\r\n";
      print "</body>\r\n";
      print "</html>\r\n";
    ```
  - 実行権限を与える => `chmod +x count.cgi`
  - クライアントでアクセス => `www.dekiru.jp/cgi-bin/count.cgi`

### 6-8. CGIでページにフィルタをかけるには
- Action設定：MIMEタイプに応じたCGIを呼び出すことが可能
  - `txturl.cgi`
    ```
      #!/usr/bin/perl
      print "Content-type: text/html\r\n";
      print "\r\n";
      print "<html><body><pre>\r\n";
      open(my $fd, $ENV{'PATH_TRANSLATED'}) or die;
      while(<$fd>){
        s/&/&amp;/g;
        s/</&lt;/g;
        s/>/&gt;/g;
        s|https?://[\w/\@\$()!?&%\#:;.,~'=:+-]+|<a href="*&">$&</a>|g;
        print $_;
      }
      close($fd);
      print "</pre></body></html>\r\n";
    ```
  - 実行権限を与える => `chmod +x count.cgi`
  - `url.txt`
    ```
      Impress Japan
      http://www.impressjapan.jp/
    ```
  - `/etc/apache2/mods-available/actions.conf`
    ```
      Action text/plain /cgi-bin/txturl.cgi
    ```
    - `sudo a2enmod actions`で設定ON
  - クライアントでアクセス => `www.dekiru.jp/url.txt`

### 6-9. エラーになったCGIの通信を確認するには
- ScriptLog：CGIエラーは大量の情報になる可能性があり、ログを単独で出力させることも必要
  - この設定は必要な時だけ行う
  - 記録するサイズ制限：ScriptLogLengthディレクティブ、ScriptLogBufferディレクティブなど
  - 設定
    ```
      ScriptLog /tmp/cgi.log
    ```

### 6-10. SSIを使えるようにするには
- SSI：HTMLファイルに特定の形式でコマンドを書き、処理する仕組み
  - SSIはむやみに設定しない => 処理負荷増
- AddOutputFilter設定
  - `/etc/apache2/mods-available/mime.conf`
    ```
        AddType text/html .shtml
      <IfModule mod_include.c>
        AddOutputFilter INCLUDES .shtml
      </IfModule>
    ```
  - `/etc/apache2/apache2.conf`
    ```
      <Directory /var/www/>
        Options Indexes FollowSymLinks Includes
        AllowOverride None
        Require all granted
      </Directory>
    ```
  - `sudo a2enmod include`

### 6-11. ファイルやコマンドの出力を組み込むには
- SSIコマンド
  - include：特定のファイル内容をページに組み込む
    - `include.shtml`
      ```
        <html>
        <body>
        <p>SSI sample page.</p>
        <!--#include file="footer.html" -->
        </body>
        </html>
      ```
    - `footer.html`
      ```
        <hr />
        <address>
        www.dekiru.jp
        </address>
      ```
    - クライアントでアクセス => `www.dekiru.jp/include.shtml`
  - exec：特定プログラムの実行結果をページに組み込む
    - コマンドの出力結果を組み込む
      - `uptime.shtml`
        ```
          <html>
          <body>
          <p>Current uptime</p>
          <pre><!--#exec cmd="uptime" --></pre>
          </body>
          </html>
        ```
      - クライアントでアクセス => `www.dekiru.jp/uptime.shtml`
      - execはセキュリティに注意：意図しない情報が表示されないよう注意
        - Options IncludesNoExecの指定も可能
    - CGIの出力を組み込む
      - `hello2.cgi`
        ```
          #!/usr/bin/perl
          print "Content-type: text/html\r\n";
          print "\r\n";
          print "<p>Hello, World!</p>";
        ```
      - 実行権限を与える => `chmod +x hello2.cgi`
      - `cgi.shtml`
        ```
          <html>
          <body>
          <p>CGI sample.</p>
          <!--#exec cgi="/cgi-bin/hello2.cgi" -->
          </body>
          </html>
        ```
      - クライアントでアクセス => `www.dekiru.jp/cgi.shtml`
- SSIを条件で変えるには
  ```
    <!--#if expr="条件" -->
    <!--#elif expr="条件" -->
    <!--#else -->
    <!--#endif -->
  ```
- 主なSSIコマンド
|SSIコマンド|機能|
|---|---|
|include virtual="～"|URLで指定した先を組み込む|
|echo var="～"|変数の値を表示|
|set var="～" value="～"|変数の値を設定する|
|printenv|変数一覧を表示する|
|config timefmt="～"|日時の表示フォーマットを変更する|
|fsize="～"|ファイルサイズの表示|

## 第7章 モジュールで拡張しよう
### 7-1. モジュールってなに？
- Apacheのモジュール：機能の単位 Apache = core + modules
- 追加方法
  - Apacheをビルドする際に組み込む
  - ビルド後に動的に追加：mod_so.cが必要
    - DSO(Dynamic Shared Object)動的共有オブジェクト
- 一覧表示
  - `apachectl -l`

### 7-2. モジュールの追加とコンパイル
- モジュールの追加
  - モジュールをApacheの実行ファイルに組み込む
  - DSOモジュールをインストール
    - 単独でコンパイル
    - Apacheと同時にコンパイル
- インストールされているモジュールを有効にする場合
  - `sudo a2enmod <モジュール名>`
- OSで用意されているモジュールをインストールする場合
  - `sudo apt-get install <モジュール名>`
- DSOモジュールをコンパイルする場合
  - `cd <モジュールのディレクトリ>`
  - `<Apachをインストールしたディレクトリ>/bin/apxs -cia <コンパイルするファイル名>`
  - `ls <Apachをインストールしたディレクトリ>/modules`
  - 設定ファイルの`LoadModule`に追加されていることを確認
- 組み込みモジュールを設定する場合
  - ソースのディレクトリに移動
  - `./configure --enable-suexec --with-suexec-docroot=/home --enable-info`
  - `make`
  - `make install`

### 7-3. データを圧縮して送るには
- mod_deflate：UbuntuではデフォルトでON設定
- `/etc/apache2/mods-available/deflate.conf`
  ```
    <IfModule mod_deflate.c>
      <IfModule mod_filter.c>
        AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css
        AddOutputFilterByType DEFLATE application/x-javascript application/javascript application/ecmascript
        AddOutputFilterByType DEFLATE application/rss+xml
        AddOutputFilterByType DEFLATE application/xml
      </IfModule>
    </IfModule>
  ```
  - その他のディレクティブ
  |デレクティブ|意味|
  |---|---|
  |DeflateBufferSize|一度に圧縮するデータの大きさ|
  |DeflateCompressionLevel|圧縮レベル（1～9）|
  |DeflateFilterNote|圧縮費をログに記録できるように追加|

### 7-4. 違うURLでアクセスさせるには
- mod_rewrite：アクセスしたURLを別のURLに変換
- Ubuntu20.04LTS
  - `sudo a2enmod rewrite`
  - 設定ファイル
    ```
      <IfModule mod_rewrite.c>
      	RewriteEngine On
	      RewriteRule ^/(hello|env|count)\.html$ /var/www/cgi-bin/$1.cgi
      </IfModule>
    ```
  - クライアントでアクセス => `www.dekiru.jp/hello.html`

### 7-5. PHPを使うには
- mod_php：Apacheにモジュールを組み込んでプログラミング言語を利用できるようにする
- Ubuntu20.04LTS
  - `sudo apt install libapache2-mod-php7.4`
  - `/var/www/html/now.php`
    ```
      <html>
      <head><title>PHP Test</title></head>
      <body>
      <p><?php echo date('D, d M Y H:i:s T', time()); ?></p>
      </body>
      </html>
    ```
  - クライアントでアクセス => `www.dekiru.jp/now.php`

### 7-6. 動的なコンテンツをキャッシュするには
- mod_cache：動的なコンテンツをキャッシュすることで高速化
- Ubuntu20.04LTS
  - `sudo a2enmod cache`
  - `sudo a2enmod cache_disk`
  - `/var/www/html/now.php`
    ```
      <?php header('Expires: ' . gmdate('D, d M Y H:i:s T', time() + 600)); ?>
      <html>
      <head><title>PHP Test</title></head>
      <body>
      <p><?php echo date('D, d M Y H:i:s T', time()); ?></p>
      </body>
      </html>
    ```
  - クライアントでアクセス => `www.dekiru.jp/now.php`

### 7-7. リバースプロキシをたてるには
- mod_proxy：アクセスごとにサーバーを振り分けるなど
- Ubuntu20.04LTS
  - `sudo a2enmod proxy`
  - `/etc/apache2/mods-available/proxy.conf`
    ```
      <IfModule mod_proxy.c>
        ProxyPass /app http://192.168.1.102/app
        ProxyPassReverse /app http://192.168.1.102/app
      </IfModule>
    ```

### 7-8. そのほかのApacheモジュールを知ろう
- mod_security：Webアプリケーションファイアウォール
- mod_ext_filter：外部コマンドでコンテンツをフィルター
- mod_speling：ファイル名のスペルミスを修正（大文字／小文字の区別なし）
- mod_QoS：リクエストのQoS制御（ユーザーごとの帯域幅制限など）
- mod_pagespeed：ページを最適化して表示を高速化
- mod_proxy_balancer：ロードバランサー

## 第8章 SSLに対応しよう
### 8-1. 暗号化されたWebサーバーを構築するには
- SSL(Secure Socket Layer)：SSL/TLS(Transport Layer Security)  
なりすまし、盗聴、改ざんを防ぐことができる
  - 相互認証：CA(Certificate Authority：認証局)を介した認証
  - データの完全性：電子署名で確認
  - プライバシー：通信を暗号化することで盗聴を防止
- ApacheでSSLを利用する
  - OpenSSL：SSLの機能を提供するOSS
  - mod_ssl：OpenSSLを使って、ApacheをSSLに対応させるモジュール
- CAに認証してもらう仕組み
  - ブラウザが持っているCAの鍵情報 → 証明書の確認
- 本格的にSSLを利用したWebサーバーを運営するには
  - 信頼できる認証局で証明書を発行してもらう
  - 手順
    - CSR(certificate Signing Request)ファイルをCAに送る
    - 証明書(Certificate)が送付される
    - 証明書をCSRと同じディレクトリに置き、ssl.confのSSLCertificateFileで設定

### 8-2. ApacheからSSLを使えるようにするには
- mod_ssl：OpenSSLを使って、ApacheをSSLに対応させるモジュール
- Ubuntu20.04LTS
  - mod_sslを有効化 `sudo a2enmod ssl`
  - SSL用仮想ホストを有効化 `sudo a2ensite default-ssl`

### 8-3. テスト用の証明書を作成するには
- サーバー証明書の作成
- Ubuntu20.04LTS
  - `cd /etc/ssl/private`
  - Webサーバーの鍵を作る
    - `openssl genrsa -aes3 -out server.key 1024` ←1024では短い、2048で作成すべき。aes3 ← aes128。
    - パスフレーズを入力
  - 証明書を作る
    - `openssl req -new -days 3650 -key server.key -out server.csr`
    - 必要事項を入力
  - テスト用に自己署名する
    - `openssl x509 -req -signkey server.key -days 3650 -in server.csr -out server.crt`
    - パスフレーズを入力
  - 鍵のパスフレーズを解除する
    - `mv server.key server.key.bak`
    - `openssl rsa -in server.key.bak -out server.key`
    - パスフレーズを入力
  - Apacheの設定
    - `/etc/apache2/sites-available/default-ssl.conf`
      ```
        ServerAdmin webmaster@localhost
		    ServerName www.dekiru.jp:443

        SSLCertificateFile	/etc/ssl/private/server.crt        <-privateのアクセス権を変えないとエラー
		    SSLCertificateKeyFile /etc/ssl/private/server.key
        SSLCipherSuite @SECLEVEL=0:HIGH:MEDIUM:!MD5:!RC4:!3DES ←1024バイトで鍵を作ったので、暫定策
      ```

## 第9章 ログを活用しよう
### 9-1. ログをチェックしよう
- ログチェックのポイント
  - 正しく動作しているかどうか  
  access_logのステータスコード、error_logを定期的に確認する
  - アクセス数を把握する  
  アクセス数を把握することで、サーバーの性能や回線速度の改善を行う
  - 変なアクセスがないかどうか  
    - アクセス元に注意する  
    特定のアクセス元から不自然なアクセスがないかを定期的に確認する
    - アクセス先に注意する  
    存在しないパスに意味もなくアクセス、特定の文字列リクエストが続く場合は、攻撃を意図したアクセスの可能性がある

### 9-2. アクセスログの記録項目を変えるには
- LogFormat、CustomeLog
- Ubuntu20.04LTS
  - `/etc/apache2/apache2.conf`
    ```
      LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
      LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
      LogFormat "%h %l %u %t \"%r\" %>s %O" common
      LogFormat "%{Referer}i -> %U" referer
      LogFormat "%{User-agent}i" agent
    ```  
    [ログの設定](https://httpd.apache.org/docs/2.4/ja/logs.html)  
    [ログ フォーマット](https://httpd.apache.org/docs/2.4/ja/mod/mod_log_config.html#%E3%83%95%E3%82%A9%E3%83%BC%E3%83%9E%E3%83%83%E3%83%88%E6%96%87%E5%AD%97%E5%88%97)
  - `/etc/apache2/sites-available/000-default.conf`
    ```
      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined
    ```

### 9-3. 指定した条件でログを分けるには
- 環境変数による制御：不必要なログを別ファイルにわけることで効率的なログ確認を行う
- 設定方法
  ```
    SetEnvIf Request_URI "\.(gif|jpg|png)$" nolog
    SetEnvIf Remote_Addr 192.168.1. nolog

    SetEnvIf Request_URI "^/default.ida" worm nolog
    SetEnvIf Request_URI "NULL\.ida" worm nolog
    SetEnvIf Request_URI "root\.exe" worm nolog
    SetEnvIf Request_URI "cmd\.exe" worm nolog

    CustomLog log/access_log common env=!nolog
    CustomLog log/worm_log common env=worm
  ```

### 9-4. エラーログの記録項目を変えるには
- LogLevel：エラー情報のレベルを変更できる
- Ubuntu20.04LTS
  - `/etc/apache2/apache2.conf`
    ```
      LogLevel warm
    ```  
    [LogLevel](https://httpd.apache.org/docs/2.4/ja/mod/core.html#loglevel)

### 9-5. ログ中のIPアドレスをホスト名に変えるには
- logresolve：コマンドでlogファイルのIPアドレスを変換して別ファイルに出力
  ```
    logresolve < access_log > resolved_log
  ```
- HostnameLookupsデレクティブ：記録時に名前解決してくれるが、負荷が大きいので使用しない

### 9-6. Cookieでユーザーを追跡するには
- mod_usertrack：Cookie情報を元に同一ユーザーのアクセスログに同じIDが振られる
- Ubuntu20.04LTS
  - 設定を有効化 `sudo a2enmod usertrack`
  - 設定
    ```
      <IfModule mod_usertrack.c>
        CookieTracking On
        CookieExpires "2 weeks"
        LogFormat "%{cookie}n %t %r" cookielog
        CustomLog log/cookie_log cookielog
      </IfModule>
    ```

### 9-7. ログを一日単位で切り替えるには
- OS標準機能を使う場合：logrotate
  - `/etc/logrotate.d/apache2`
- rotatelogsコマンドを使う場合
  - `/etc/apache2/sites-available/000-default.conf`
    ```
      CustomLog "|/usr/sbin/rotatelogs /var/log/apach2/access_log 86400" common
    ```

### 補足. アクセス解析ツール
- ログ解析：フリーソフト等
- アクセス解析用サーバー：Google Analyticsなど

## 第10章 サーバー管理をしよう
### 10-1. セキュリティに気をつけよう
- セキュリティ対策
  - インターネットのサーバーはスキャンや攻撃を受ける
  - Apacheで気をつけること
    - Apacheのセキュリティホール => アップデート
    - 設定ミス
    - CGI、PHPなどスクリプトの脆弱性
      - クロスサイトスクリプティング(XSS)
      - SQLインジェクション
      - session乗っ取り
      - コマンド実行：サニタイズ忘れ
  - その他の危険
    - アクセス権限の管理
    - ログインパスワードの管理

### 10-2. 動作状況をWebブラウザで確認するには
- mod_status：サーバーの動作状況を知る
  - Ubuntu20.04LTS
    - 設定を有効化 `sudo a2enmod atatus`
    - `/etc/apache2/mods-available/status.conf`
      ```
        <Location /server-status>
          SetHandler server-status
          Require local
          Require ip 172.21.208.1
        </Location>
      ```
    - クライアントでアクセス => `www.dekiru.jp/server-status`
    - クライアントでアクセス => `www.dekiru.jp/server-status?auto` => 簡易形式
- mod_info：サーバーの設定情報を知る
  - Ubuntu20.04LTS
    - 設定を有効化 `sudo a2enmod info`
    - `/etc/apache2/mods-available/info.conf`
      ```
        <Location /server-info>
          SetHandler server-info
          Require local
          Require ip 172.21.208.1
        </Location>
      ```
    - クライアントでアクセス => `www.dekiru.jp/server-info`
    - クライアントでアクセス => `www.dekiru.jp/server-info?list` => モジュール一覧
    - クライアントでアクセス => `www.dekiru.jp/server-info?server` => サーバー本体情報
    - クライアントでアクセス => `www.dekiru.jp/server-info?mod_ssl.c` => モジュールの情報

### 10-3. サーバーの動作状況を確認するには
- プロセスの状態を見る：psコマンド
  - CPUの負荷の状態を見る
    - `uptime` => load average
  - プロセスの状態を見る
    - `ps ax` => a：自分以外、x：デーモンなど
    - `ps axu` => u：ユーザー名などの詳細
  - ネットワークカードの状態をみる
    - `ifconfig`
  - ネットワークが繋がっているかを確認する
    - `ping www.impress.co.jp`
  - ネットワークに関する状態を見る
    - `netstat -l`
  - OSのログを見る
    - `/var/log/`フォルダを参照

### 10-4. Webブラウザのキャッシュを制御するには
- キャッシュ：高速に表示、サーバー負荷減 => 静的なページ
  - キャッシュの仕組み
    - If-Modified-Since:ヘッダー：時刻情報で更新を判断する
    - If-None-Match:ヘッダー：コンテンツの内容で判断する
  - サーバーからの制御
    - Last-modified:ヘッダー
    - ETag:ヘッダー
    - Cache-Control:、Pragma:、Expires:ヘッダーなど
  - ETagと負荷分散の注意点
    - ロードバランサーを使用 => ETagがうまく働かない場合あり
  - HTMLファイルによる制御
    - metaタグでHTTPヘッダーと同様に制御が可能
    - Webブラウザによるので、サーバーの設定通りに行かないケースもある

### 10-5. 性能を向上させるには
- パフォーマンスチューニング
  - 不要な設定を削れば性能は向上する
    - 使っていないモジュール
    - ディレクティブの複雑な表現
    - 正規表現
    - .htaccessの利用
  - ベンチマークでApacheの性能を測定しよう
    - `ab -c 10 -n 100 http://localhost/` => ab(Apache Bench)
  - Keep-Aliveの調整：接続したままでいる設定
  - 待ち受けプロセス数の調整：プロセス数多 => 負荷増、プロセス数少 => 待ち増
  - ApacheのMPM(Multi-Processing Module)：Linux => prefork より高性能 => worker,event ただし、対応していないモジュールもある
  - Webページの構成を見直す：スクリプト、CSSの見直し
  - CGIを見直す
  - サーバーの構成を見直す：CDN(Contents Delivery Network)、ロードバランサー、SSD、メモリ追加など
  