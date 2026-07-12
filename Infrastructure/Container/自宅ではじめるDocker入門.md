# 自宅ではじめるDocker入門
## 1. Dockerのポイント
### 1-1. 仮想サーバより軽量な、コンテナ型の仮想技術、Docker
- 「Dockerとは」：Docker社が提供する、オープンソースの「コンテナ型 仮想化ソフト」
- 「仮想化とは」：実際のマシンを用意しなくても、仮想的にPCやサーバを作れる仕組み
- Dockerとコンテナ
  - 1つのDocker上にたくさんのコンテナを作成可能
  - コンテナ同士は、互いに影響を受けず、隔離された実行環境となる
- 1アプリ＝1コンテナで構成する
  - コンテナ：実行マシンとやり取りするだけの最低限の機能 => 軽量
  - | Apache |  | MySQL |  ｜Sendmail｜ <= それぞれをアップデート、入れ替え可 
- コンテナ
  - 「イメージ」からコンテナが生成される
  - イメージはDocker Hubに登録されている
  - イメージはカスタム化も可能
- サーバ運用
  - アプリのデータ保存部分はコンテナと別
  - データを保持したまま、コンテナの破棄、再構築が簡単
  - ダウンタイムを最小にできる利点がある
- 仮想マシンと仮想コンテナの違い
  - 仮想マシン：VMware、Virtual Box等
    - コンピュータそのものを仮想的に作り出す
    - 動作：遅い
    - 仮想マシンにOSをインストール（ゲストOS）し、その上でアプリが動く
  - コンテナ
    - ハードウェアをエミュレート（模倣）しない
    - OS本体も含まない
    - ホストOSが実行を行う
    - OSのオーバヘッドがなく、メモリ消費量が格段に少ない
- Dockerのメリット、デメリット
  - メリット
    - アプリが互いに影響を及ぼさない
      - ライブラリの依存関係など
    - 保守・運用コストを下げられる
      - コンテナは交換が簡単
      - ゲストOSがないので、OSのメンテが少ない

### 1-2. Dockerを動かすための環境
- Linux、Mac、Windowsで動作可能
- 日々、開発が進んでいるため、公式の情報を参照

## 2. Dockerを実際に動かそう
### 2-1. Dockerのインストール
- Ubuntuの標準化バージョン（書籍の方法）
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install docker.io
``` 
  - インストールは簡単
  - Dockerの最新版とはタイムラグあり
- Docker公式（docker-ce）
  - [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)を参照
  - 最新版なので、今回はこの方法で導入
- Dockerの起動と終了
```
$ sudo service docker start
$ sudo service docker stop
$ sudo service docker restart
$ sudo systemctl start docker
$ sudo systemctl stop docker
$ sudo systemctl restart docker
```
- dockerの状態確認
```
$ sudo service docker status
$ sudo systemctl status docker
```

起動、終了等については、Linuxのディストリビューション毎に公式ドキュメントを参照

### 2-2. Dockerの基本 イメージについて学ぶ<br>
[Docker コマンド](https://docs.docker.jp/engine/reference/commandline/index.html)
- イメージとコンテナの関係
  - イメージ：コンテナの元となるライブラリ、コマンドなどが格納されたパッケージ
  - イメージ => 「run」 => コンテナ
- イメージを取得する
```
docker pull [オプション] 名前[:タグ]
# docker pull ubuntu
```
  - タグ：イメージ名の後ろに「:」で区切られた値（バージョン）
  - タグ省略：:latestが適用される
- 取得したイメージを確認する
```
docker images [オプション] [リポジトリ[:タグ]]
[-a]  全てのイメージを表示（デフォルトは中間コンテナを非表示）
[-q]  整数値の ID のみ表示
# docker images -a
```
- イメージを削除する
```
docker rmi [オプション] イメージ [イメージ...]
# docker rmi ubuntu | docker rmi イメージID
イメージを全部削除
# docker image prume
# docker rmi $(docker images -aq)
```

### 2-3. イメージからコンテナを作る
- イメージからコンテナを作る
```
docker run [オプション] イメージ [コマンド] [引数...]
新しいコンテナを実行する命令
[-i]      コンテナの STDIN にアタッチ
[-t]      疑似ターミナル (pseudo-TTY) を割り当て
[--name=""]   コンテナに名前を割り当て
# docker run -it ubuntu /bin/bash
```
- コンテナを終了する
```
# exit
```
- コンテナの状態を確認する
```
docker ps [オプション]
コンテナの一覧
[-a]    全てのコンテナを表示 (デフォルトは実行中コンテナのみ表示)
[-l]    最後に作成したコンテナを表示 (どのような状態でも)
[-q]    整数値の ID のみ表示
# docker ps     実行中のコンテナのみ
# docker ps -a  全てのコンテナを表示
```
- 停止中のコンテナを再開する
```
docker start [オプション] コンテナ [コンテナ...]
１つまたは複数のコンテナを起動する
[-a]    STDOUT、STDERR にアタッチする
[-i]    コンテナの STDIN にアタッチ
# docker start -ai コンテナID
最後に作成したコンテナの起動
# docker start -ai $(docker ps -lq)
```
- コンテナを削除する
```
docker rm [オプション] コンテナ [コンテナ...]
１つまたは複数のコンテナを削除
# docker rm コンテナID
```
- 一括で停止コンテナを削除する（なるべく使わない）
```
# docker container prune
```
  - 停止中のデータボリューム・コンテナも消去されるので注意が必要

### 2-4. Dockerコンテナの性質
- 同じコンテナか違うコンテナかの区別
  - コンテナIDが同じ ＝ 同じコンテナ
  - コンテナIDが違う ＝ 違うコンテナ
- コンテナに重要なデータは残さない
  - 破棄前提で重要なデータは残さない
  - データは、データボリューム領域に接続して保存する

### 2-5. Dockerコンテナにアプリケーションを入れてみる
- ubuntuコンテナにpingをインストールする
```
# docker run -it --name=ping_in_ubuntu ubuntu /bin/bash
# apt update
# apt install iputils-ping
```
- イメージやコンテナのデータは軽くする<br>
apt updateで更新したリポジトリデータは構築時には必要だが、運用時は不要。
```
# apt clean
# rm -rf /var/lib/apt/lists/*
```
- アタッチとデタッチ
  - デタッチ：フォアグラウンド => バックグラウンド
    - [Ctrl]+[P]、[Ctrl]+[Q]を押す
  - アタッチ：バックグラウンド => フォアグラウンド
  ```
  docker attach [オプション] コンテナ
  実行中のコンテナにアタッチする
  # docker attach コンテナID
  ```

### 2-6. コンテナをイメージ化する
- コンテナをイメージに変換する
```
docker commit [オプション] コンテナ [リポジトリ[:タグ]]
コンテナの変更を元に新しいイメージを作成
# docker commit コンテナID ping_in_ubuntu
```
- dockerイメージのイメージ名とタグについて
  - タグを省略 => :latest
  - 2度目のcommit => 最新は:latest、最新以外はイメージ名、タグ名がnone
  - noneでは、分かりづらいので、タグ名を付加しておく
  ```
  docker tag [オプション] 名前[:タグ] 名前[:タグ]
  リポジトリ内のイメージにタグ付け
  # docker tag ping_in_ubuntu:latest ping_in_ubuntu:ver1.00
  ```
    - :ver1.00と:latestの2つができるように見えるが、IDは同じ
    - commitすると新しい方が:latestになり、:ver1.00は残る
    - タグを消去：`docker rmi イメージ名:タグ名`
- イメージのファイル化
  - tarファイル：Linuxで最も使われるアーカイブ・ファイル
  - docker save
  ```
  docker save [オプション] イメージ [イメージ...]
  １つまたは複数のイメージを tar アーカイブに保存
  # docker save ping_in_ubuntu:latest > ping_save.tar
                ↑イメージIDだとload時にnoneになる
  ```
    - ベースレイヤーと各レイヤー情報をtarファイルに保存
    - 共通のベースイメージは共有リソースとして使われる
    - 管理できるレイヤー数に上限があるので注意
  - docker export
  ```
  docker export [オプション] コンテナ
  コンテナのファイルシステム内容を tar アーカイブとして出力
  # docker export コンテナID > ping_export.tar
  ```
    - 最終的なコンテナの内部構成をtarファイルに保存
    - 共有リソースは活かされない
    - レイヤーは単一（イメージの起点になる）
- イメージファイルの取り込み
  - docker load
  ```
  docker load [オプション]
  tar アーカイブからイメージを読み込み
  # docker load < ping_save.tar
  ```
    - イメージIDは同じ
    - CREATE時刻もSAVE時の時刻
    - 開発チーム内での分担時はsaveしたデータを使用する
  - docker import
  ```
  docker import ファイル|URL|- [リポジトリ[:タグ]]
  空のファイルシステム・イメージを作成し、tar ボールの内容を読み込む
  # docker import < ping_export.tar
  ```
    - イメージIDが別物になる
    - CREATE時刻もimport時の時刻になる
    - exportしたファイルを共通イメージとして使うと各人でバラバラのIDになる
  - 運用上の工夫
    - export => import => イメージ名：タグ名 => save => 配布

## 3. Dockerの基本を学習する
### 3-1. 公式のApacheイメージを取得する
- インストール方法
  - OSのコンテナ作成 => アプリをインストール（設定工数必要）
  - ソフトウェア入りのイメージを使う（手軽・軽量）<= 今回利用
  ```
  # docker pull httpd   httpd：Apacheの起動・停止のサービス名
  ```

### 3-2. Dockerのポート制御
- 通信経路
  - 通常：OS => アプリ
  - Docker：ホストOS => Docker Engine => コンテナ（アプリ）
- ポートのリレー
  - ホストOSが待ち受けするポート => コンテナが待ち受けするポート
  ```
  docker run [オプション] イメージ [コマンド] [引数...]
  新しいコンテナを実行する命令
  [-p]    コンテナのポートをホスト側に公開
  # docker run -p ホストOSポート:コンテナポート
  ```

### 3-3. コンテナをバックグラウンドで実行する
- httpdコンテナをバックグラウンドで実行する
```
docker run [オプション] イメージ [コマンド] [引数...]
新しいコンテナを実行する命令
[-d]    コンテナをバックグラウンドで実行し、コンテナ ID を表示
# docker run -p 8080:80 -d httpd
```
  - STATUS：Up（稼働）
  - PORTS：0.0.0.0:8080->80/tcp
- Webブラウザから接続して確認する
  - IPアドレス確認
  ```
  $ ip address  =>  eth0がIPアドレス
  ```
  - 「It works!」が表示
- コンテナの終了と再開（-d：デタッチモードで実行したコンテナ）
```
docker stop [オプション] コンテナ [コンテナ...]
コンテナに SIGTERM を送信し、一定期間後に SIGKILL を送信
[-t=10]   kill で停止する前に待機する秒数
docker start [オプション] コンテナ [コンテナ...]
１つまたは複数のコンテナを起動する
[-a]    STDOUT、STDERR にアタッチする
[-i]    コンテナの STDIN にアタッチ
# docker stop コンテナID
# docker ps -a
# docker start コンテナID
# docker ps
```
- Webサーバーを複数台設置する場合
  - 通常：IPアドレスを複数持たせてIPアドレスで区別 <= Dockerではできない
  - Docker：nginxのリバースプロキシで振り分ける方法が使われる（第5章）

### 3-4. コンテナからホストOSのファイルにアクセスする<br>
コンテナは「破棄」される前提 => ログデータ等をコンテナ内で保持はダメ
- データを永続的に保存する方法
  - Dockerの管理外にあるホストOS側のディレクトリに保管
  - 「永続コンテナ」というデータ専用コンテナに入れ保管
- マウントを使用して他の領域に保存
  - マウント：OSとは異なるデバイスにある領域をOS状のディレクトリ階層に組み込み、アクセスできるようにする技術（CD-ROMドライブなど）
  - マウントポイント：OS上にある別のデバイスにつながる結合点
  - マウントディレクトリ：つながった先のディレクトリ
  - マウントする：ディレクトリをつなぐ
- コンテナ内データのマウント先
  - コンテナ内のディレクトリ => OS上のディレクトリ
  - コンテナ内のディレクトリ => Docker上のデータボリューム
- Dockerの管理外にあるホストOS側のディレクトリに保管する方法
  - コンテナ(Apache)：/usr/local/apache2/htdocs
  - ホストOS：/root/WebContents/index.html
  ```
  docker run [オプション] イメージ [コマンド] [引数...]
  新しいコンテナを実行する命令
  [-v]=[ホスト側ソース:]コンテナ側送信先[:<オプション>]
  # docker run -v /root/WebContents/:/usr/local/apache2/htdocs/ -p 80:80 -d httpd
  ```
- ホストOSに永続データを保存することは推奨されない
  - Dockerコンテナ側とホストOS側でユーザーアカウント、権限の違いで意図しない動作をする可能性がある
  - ホストOSの環境に依存することで、コンテナを他のDockerに移動する足かせになる
  - Dockerfile（第4章）のコンテナ自動生成では、ホストOSのディレクトリにマウント不可
  - 推奨されないのは、「永続データの出力のみ」、マウント操作は非常によく使う
  - Docker推奨
    - データボリューム領域を作成し、保存する
    - データボリュームをコンテナ化したデータボリューム・コンテナを作成し、保存する

### 3-5. データボリューム
- データボリュームを作る
```
docker volume create [オプション]
ボリュームを作成
[--name=]   ボリューム名を指定  <= 名前を付けないと後でわからなくなる
# docker volume create --name=datavolume
```
- データボリュームの確認
```
docker volume ls [オプション]
ボリュームの一覧
[-q]    ボリューム名のみ表示
# docker volume ls
```
- データボリュームをマウントしたコンテナを作る（ubuntu）
```
# docker run -it -v datavolume:/tmp/ ubuntu /bin/bash
# touch /tmp/data.txt
# exit
# docker rm コンテナID
# docker run -it -v datavolume:/tmp/ ubuntu /bin/bash
# ls /tmp/    => data.txtが存在する
```
- 匿名ボリュームに注意
```
# docker run -it -v /tmp/ ubuntu /bin/bash
```
  - データボリューム名をつけずにデータボリュームを作成可能
  - 分かりづらいので、名前を付けるのを忘れない
- データボリュームの管理
  - データボリュームの削除：利用しているコンテナの削除が先
  ```
  docker volume rm [オプション] ボリューム [ボリューム...]
  ボリュームを削除
  # docker volume rm ボリューム名
  ```
  - データボリュームの一括削除
  ```
  docker volume prune [オプション]
  未使用のローカルボリュームを削除
  ```

### 3-6. データボリューム・コンテナ
- データボリューム・コンテナの仕組み
  - データボリュームとマウントポイント
    - マウントパスが同じコンテナ：データを参照可能
    - マウントパスが異なるコンテナ：データの共有不可
  - データの共有 => マウントパスの統一が必要
  - データボリューム・コンテナ：データボリュームへのマウントパスを統一したコンテナ
    - コンテナ => データボリューム
    - コンテナ =>  データボリューム・コンテナ => データボリューム
- データボリューム・コンテナを作る
```
# docker volume create --name=datavolume  <=↓だけでデータボリュームも作成される
# docker run --name=data-container -v datavolume:/tmp ubuntu
```
- アプリケーション・コンテナがデータボリューム・コンテナを使用可能にする
```
docker run [オプション] イメージ [コマンド] [引数...]
新しいコンテナを実行する命令
[--volumes-from=[]]   指定したコンテナからボリュームをマウント
# docker run --volumes-from data-container -it ubuntu /bin/bash
```
- データボリューム・コンテナの特殊性
  - 作り方は通常のコンテナと類似
  - 使用する場合は、利用するコンテナで「volumes-from」で指定
  - 稼働していなくても利用できる
    - 全削除の際に注意
    ```
    docker container prune  <= データボリューム・コンテナも削除
    ```
    - 削除してもデータは消えない
    - 再度、同じデータボリューム、同じ名前のコンテナを作れば復旧可能

### 3-7. 応用編）busyboxを使った、軽量なデータ・コンテナを作成する
- データボリューム・コンテナは軽量OSを使う
  - 最低限の機能で十分
  - 軽量のOSを選択するべき
  - busybox、alpineなど
  ```
  # docker pull busybox
  # docker run --name=bb-container -v bb-volume:/shared busybox
  # docker run --volumes-from bb-container -it ubuntu /bin/bash
  ```
  - 軽量 <=> 同じOSでリソース共有のバランスも大事

### 3-8. Dockerのデータボリュームの内容をバックアップ・リストアする
- データボリュームをバックアップ・リストアする方法
  - バックアップ用コンテナを作成する<br>
  データボリューム・コンテナ => マウント先ディレクトリを指定 => ホストOSのディレクリに書き出す
  - リストア用コンテナを作成する<br>
  ホストOSのディレクリのファイル => マウント先ディレクトリを指定 => データボリューム・コンテナに展開
- バックアップ
```
# docker run --volumes-from dtat-container -v /root/:/backup ubuntu tar cvf /backup/container.bkup.tar -C / tmp
```
  - docker run：コンテナ作成、実行
  - --volumes-from dtat-container：データボリューム・コンテナに接続
  - -v /root/:/backup：ホストOS /root/、バックアップコンテナ /backup をマウント
  - ubuntu：ubuntuイメージを指定
  - tar cvf /backup/container.bkup.tar -C / tmp：/に移動してtmpを対象に、/backupにcontainer.bkup.tarを作成
  ```
  tar [オプション] アーカイブ
  [-c]    新しくアーカイブを作成
  [-x]    アーカイブを展開
  [-v]	  作成・展開するアーカイブのファイルリストを表示
  [-f]	  アーカイブファイルを指定
  [-C]    tar -cvf アーカイブファイル名 -C アーカイブ元ディレクトリ アーカイブ元ファイル名
  ```
  - バックアップコンテナは、`docker start`で再利用が可能
- リストア
```
# docker run --volumes-from dtat-container -v /root/:/backup ubuntu tar xvf /backup/container.bkup.tar -C /
```
  - docker run：コンテナ作成、実行
  - --volumes-from dtat-container：データボリューム・コンテナに接続
  - -v /root/:/backup：ホストOS /root/、バックアップコンテナ /backup をマウント
  - ubuntu：ubuntuイメージを指定
  - tar xvf /backup/container.bkup.tar -C：/に移動して、/backupにcontainer.bkup.tarを展開（tmpフォルダが作られる）
- データボリュームはどこにある
```
docker inspect [オプション] コンテナ|イメージ|タスク [コンテナ|イメージ|タスク...]
コンテナあるいはイメージかタスクの低レベル情報を表示
# docker inspect データボリューム名
```

## 4. Dockerを活用する
### 4-1. Dockerfileを作ってみよう
- Dockerfile：コマンド操作をバッチ処理する<br>
以下の一連の作業を自動で行う「設計図」のようなもの
  - イメージの取得
  - コンテナの生成
  - アプリケーションの導入
  - 新規イメージの生成 など
  - 同じ環境を他のホストOSでも簡単に準備が可能
- Dockerfileの注意点
  - ファイル名：Dockerfile
  - Dockerfileを配置したディレクトリには余計なものは置かない
  - コマンドはなるべく1命令でまとめる：レイヤー数の上限
  - Docker Daemon：Daemonは中核プログラム、Client => Daemonで処理
  - 除外ファイルについて：.dockerignoreファイルを作成
  ```
  # コメント
  \*/temp*
  \*/*/temp*
   temp? 
  ```
- Dockerfileを作ってみよう
  - 新規ディレクトリとDockerfileの作成
  - Dockerファイルの中身
  ```
  # ubuntuの取得
  FROM ubuntu
  # リポジトリの更新後、pingインストール
  RUN atp-get update && apt-get install -y iputils-ping
  ```
    - apt と apt-get：aptはターミナル用、Dockerfileではapt-getを使用
  - イメージの作成（Dockerfileの実行）
  ```
  docker build [オプション] パス | URL | -
  パスにあるソースコードから新しいイメージを構築
  [-t]    '名前:タグ' 形式で名前とオプションのタグを指定
  # docker build -t ping-image .
  ```
  - 確認
  ```
  # docker images
  # docker run -it ping-image /bin/bash
  # ping localhost
  ```
  - Dockerfileのコマンド間違い
  ```
  docker logs [オプション] コンテナ
  コンテナのログを取得
  # docker logs コンテナID    => logで最後の挙動が見える、デバッグ
  ```

### 4-2. Dockerfileの書き方と注意事項
- Dockerfileの書式
```
# コメント
命令 引数
```
  - 命令：大文字で書く <= 大文字／小文字の区別はない
  - 引数：小文字で書く <= 大文字／小文字の区別はない
  - エスケープ文字：「\」
  ```
  RUN apt-get update && apt-get install -y \
      iputils-ping \
      openssh-server  <= アルファベット順に書く
  ```
- 形式
  - Shell形式：シェルを通してコマンドが実行、環境変数も使える
  ```
  命令 引数
  RUN apt-get update
  ```
  - Exec形式：実行バイナリファイルを直接実行
  ```
  命令 ["実行バイナリ","パラメータ1","パラメータ2"]
  RUN ["/bin/bash","-c"," apt-get update"]
  ```
- Dockerfileの命令一覧<br>
[Dockerfile リファレンス](https://docs.docker.jp/engine/reference/builder.html#)
  - FROM
  ```
  FROM <image>[:<tag>] [AS <name>]
  ベース・イメージ を設定
  # ubuntuの取得
  FROM ubuntu:latest
  ```
  - MAINTAINER（非推奨：廃止予定）
  ```
  MAINTAINER <name>
  ビルドされるイメージの Author フィールドを設定
  ※LABEL命令を使用
  ```
  - RUN
  ```
  RUN <command>
  RUN ["executable", "param1", "param2"]
  現在のイメージの最上位の最新レイヤーでコマンドを実行
  ```
  - CMD
  ```
  CMD command param1 param2 (シェル形式)
  CMD /bin/bash
  CMD ["executable","param1","param2"] (exec 形式)
  CMD ["/bin/bash"]
  ```
    - Dockerfileで一度だけ指定可能
    - 複数あった場合は最後のCMDが有効
    - docker runの引数で上書きされる
  - LABEL
  ```
  LABEL <key>=<value> <key>=<value> <key>=<value> ...
  イメージに対してメタデータを追加
  LABEL imageversion=1.0 \
        maintainer=sho\ asai
  ```
    - 値に空白などを含める場合：「\」でエスケープ
  - EXPOSE
  ```
  EXPOSE <port> [<port>...]
  コンテナの実行時にリッスンするポートを指定
  EXPOSE 80
  ```
    - ホストOS経由で外部から接続：docker run -p xx:yy
  - ENV
  ```
  ENV <key> <value>
  ENV <key>=<value> ...
  環境変数 <key> に <value> という値を設定
  ENV WORDPRESS＿DB＿HOST=mysql-container \
      WORDPRESS＿DB＿USER=root \
      WORDPRESS＿DB＿PASSWORD=dbpass01
  ```
  - COPY（コピー）
  ```
  COPY <src>... <dest>
  COPY ["<src>",... "<dest>"] 
  <src> からファイルやディレクトリを新たにコピーして、コンテナ内のファイルシステムのパス <dest> に追加
  COPY httpd.conf /usr/local/apache2/conf/httpd.conf
  COPY ["httpd.conf","/usr/local/apache2/conf/httpd.conf"]
  ```
  - ADD（展開）
  ```
  ADD <src>... <dest>
  ADD ["<src>",... "<dest>"] 
  <src> に示されるファイル、ディレクトリ、リモートファイル URL をコピーして、イメージ内のファイルシステム上のパス <dest> にこれらを加える
  ADD Contents.tar /user/local/apache2/htdoc/
  ADD ["Contents.tar","/user/local/apache2/htdoc/"]
  ```
    - パスは絶対パス、相対パス（WORKDIRから）で指定
    - 「*も使える」
  - ENTRYPOINT
  ```
  ENTRYPOINT ["executable", "param1", "param2"] （exec 形式、推奨）
  ENTRYPOINT command param1 param2 （シェル形式）
  ```
    - exec形式：CMDと組み合わせても使える
    ```
    ENTRYPOINT ["/bin/bash", "-c"] 
    docker run -it イメージ pwd
    => docker run -it イメージ /bin/bash -c pwd 
    ```
    - 必須の引数を自動で定義も可能
    ```
    ENTRYPOINT ["/bin/bash", "-c"] 
    CMD ["echo no-value"] <= docker runで引数が不足の場合使用される
    ```
  - VOLUME
  ```
  VOLUME /data
  VOLUME ["/data"]
  指定された名前を使ってマウントポイントを生成する
  VOLUME /tmp
  VOLUME ["/tmp"]
  ```
  - WORKDIR
  ```
  WORKDIR /path/to/workdir
  WORKDIR ["/path/to/workdir"]
  ワークディレクトリを設定する
  WORKDIR /tmp1 # 絶対パス => /tmp1
  WORKDIR tmp2  # 相対パス => /tmp1/tmp2
  ```
    - WORKDIR 命令は Dockerfile 内にて複数利用することが可能
    - ディレクトリ指定に相対パスが用いられた場合、そのパスは、直前の WORKDIR 命令からの相対パス
  - ONBUILD
  ```
  ONBUILD INSTRUCTION
  ONBUILD [INSTRUCTION]
  イメージに対して トリガ 命令（trigger instruction）を追加する
  => build時に実行するコマンド
  ```
    - 2つのDockerfileを用いて2段階にイメージを生成する際に使用
    ```
    FROM httpd
    ONBUILD COPY httpd.conf /usr/local/apache2/conf/httpd.conf
    ```
    ```
    FROM httpd-image
    ADD Contents.tar /usr/local/apache2/htdoc/
    ```
      - 1つ目のファイルをbuildしてhttpd-imageを作成する
      - この時点でイメージの作成のみでCOPYは未実行
      - このイメージをbuildしてイメージを作る際にONBUILDが最初に実行
      - 続けてADDが実行される
      - 変更の多いファイルを2段目に持っていくことでルソースの削減

### 4-3. Dockerfileの落し穴 キャッシュ
- 複数のDockerfileのbuild時、共通部分はキャッシュを使う
  - 作成済イメージは、キャッシュとして再利用する
  - Dockerfileを使った構築も同様：命令ごとに中間イメージが作られる
  ```
  FROM ubuntu # 中間イメージA
  RUN apt-get update && apt-get insatll -y iputils-ping # 中間イメージB
  CMD /bin/bash # 中間イメージC
  ```
  ```
  FROM ubuntu # 省略
  RUN apt-get update && apt-get insatll -y iputils-ping # 省略
  CMD ping localhost  # 中間イメージB => 中間イメージD
  ```
    - 高速化
    - リソース消費の抑制
- キャッシュによるbuildの弊害と回避策
  - リポジトリ、インストールするアプリが更新された場合
    - 古いキャッシュを使い続け更新されない
  - 回避策
    - キャッシュを使わない
    ```
    docker build [オプション] パス | URL | -
    パスにあるソースコードから新しいイメージを構築
    [-t]    '名前:タグ' 形式で名前とオプションのタグを指定
    --no-cache    イメージ構築時にキャッシュを使わない
    docker build -t イメージ名 . --nocache=true
    ```
    - Dockerfileの記述を変更する
    ```
    FROM ubuntu
    LABEL lastupdate=2018-06-01
    RUN apt-get update && apt-get insatll -y iputils-ping
    CMD /bin/bash 
    ```
      - LABELなどを記述し、buildのたびに書き換える
      - 変更されたLABEL以降は新たにイメージが作成される
      - 変更が少なく、DLに時間がかかるものは先頭側に記述
      - 更新が必要な部分をLABEL以降に記述する
  - キャッシュが使われない命令
    - COPY／ADD：キャッシュが使われない命令
    - COPY／ADDが前にあると以降は全てイメージが新たに作成される

## 5. Dockerでいろいろなコンテナを作ってみよう
### 5-1. ApacheをDockerfileでビルドしてみる
- 手順
  1. 「httpd.conf」の取出し
  ```
  docker run [オプション] イメージ [コマンド] [引数...]
  新しいコンテナを実行する命令
  [--rm]    コンテナ終了時、自動的に削除
  一時コンテナを作成
  # docker run --rm -v /root/httpd/:/tmp/ -it httpd /bin/bash
  httpd.confファイルをコンテナの/tmp/（/root/httpd/）にコピー
  # cp /usr/local/apache2/conf/httpd.conf /tmp/
  # exit
  ```
  2. 「httpd.conf」のログ出力設定を変更する
  ```
  #CustomLog "logs/access_log" combined
  ↓
  CustomLog "logs/access_log" combined
  ```
  3. Dockerfileを作成する
  ```
  # イメージを取得
  FROM httpd
  # ホストOSにあるindex.htmlを、ApacheのDocumentRootにセット
  COPY ./index.html /usr/local/apache2/htdocs/
  # 編集したhttpd.confをApacheのデフォルトhttpd.confに上書き
  COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf
  # Dockerに対して80番ポートを使用する通知
  EXPOSE 80
  ```
  ```
  # docker build -t apache-image .
  ```
  4. データボリューム・コンテナを作成する
  ```
  # docker run -v apachelog-volume:/usr/local/apache2/logs --name=apachelog-container busy box
  ```
  5. httpdコンテナを作成する
  ```
  # docker run --volumes-from apachelog-container -p 80:80 -d --name=apache-container apache-image
  ```
  6. 動作確認をする
    - ブラウザで確認
    - ログを確認
    ```
    # docker run --rm --volumes-from apache-container -it ubuntu /bin/bash
    # more /usr/local/apache2/logs/access_log
    # exit
    ```

### 5-2. nginxをリバースプロキシにしたApacheの構築
- 手順
  1. nginxからリバースプロキシ設定に必要なconfファイルを取り出す
  ```
  一時コンテナを作成
  # docker run --rm -v /root/rproxy/:/tmp/ -it nginx /bin/bash
  confファイルをコンテナの/tmp/（/root/rproxy/）にコピー
  # cp /etc/nginx/conf.d/default.conf /tmp/
  # exit
  ```
  2. nginxのconfファイルに設定を行う
  ```
  location {
      proxy_pass http://web-container/;
  }
  ```
  3. nginxのDockerfileを作成する
  ```
  # nginxイメージの取得
  FROM nginx
  # 設定したconfファイルをコンテナに送る
  COPY ./default.conf /etc/nginx/conf.d/default.conf
  # 使用ポートの通知
  EXPOSE 80
  ```
  ```
  # docker build -t rproxy-image .
  ```
  4. httpdのDockerfileを作成する:
  ```
  # イメージを取得
  FROM httpd
  # ホストOSにあるindex.htmlを、ApacheのDocumentRootにセット
  COPY ./index.html /usr/local/apache2/htdocs/
  # Dockerに対して80ポートを使用する通知
  EXPOSE 80
  ```
  ```
  # docker build -t web-image .
  ```
  5. Docker上でnetworkを作る
  ```
  # docker network create web-network
  ```
  6. httpdコンテナを作る
  ```
  # docker run --name=web-container  --net=web-network -d web-image
  ```
  7. nginxコンテナを作る
  ```
  # docker run --name=rproxy-container --net=web-network -p 80:80:-d rproxy-image
  ```

### 5-3. MySQL+phpMyAdminを構築する
- 手順
  1. 事前準備（ディレクトリ、ネットワーク、データボリューム・コンテナ）
  ```
  # mkdir /root/mydatabase
  # cd /root/mydatabase
  # docker network create mysql-nw
  # docker volume create --name=db-volume
  # docker run -v db-volume:/var/lib/mysql/ --name=db-container busybox
  ```
  2. MySQLのDockerfileを作成する
  ```
  # MySQLのイメージの取得
  FROM mysql
  # MySQLのrootアカウントにパスワードを設定
  ENV MYSQL_ROOT_PASSWORD=dbpass01
  # 文字コードとしてutf8mb4をセット
  CMD ["mysqld",\
  "--character-set-server=utf8mb4","--collation-server=utf8mb4_unicode_ci","--default_authentication_plugin=mysql_native_password"]
  ```
  ```
  # docker build -t mysql-image .
  ```
  3. MySQLコンテナの作成
  ```
  # docker run --volumes-from=db-container --name=mysql-container --net=mysql-nw -d mysql-image 
  ```
  4. デバッグ、確認
  ```
  docker logs [オプション] コンテナ
  コンテナのログを取得
  # docker logs mysql-container
  ```
  ```
  docker exec [オプション] コンテナ コマンド [引数...]
  実行中のコンテナでコマンドを実行
  [-i]    アタッチしていなくても STDIN をオープンにし続ける
  [-t]    疑似ターミナル (pseudo-TTY) の割り当て
  # docker exec -it mysql-container /bin/bash
  # mysql -uroot -pdbpass01
  mysql> status
  ```
  ```
  # docker run --rm --volumes-from db-container -it ubuntu /bin/bash
  # ls -l /var/lib/mysql/
  ```
  5. phpMyAdminのDockerfileを作成する
  ```
  # phpmyadminの取得
  FROM phpmyadmin/phpmyadmin
  # phpmyadminの環境変数の設定
  ENV PMA_HOST=mysql-container \
      PMA_USER=root \
      PMA_PASSWORD=dbpass01
  ```
  ```
  # docker build -t pma-image .
  ```
  6. phpMyAdminのコンテナを作成する
  ```
  docker run --net=mysql-nw --name=pma-container -p 8080:80 -d pma-image
  ```
  7. 動作確認
  ```
  # docker ps
  ```
  8. コンテナ破棄、再作成
  ```
  # docker stop mysql-container pma-container
  # docker container prune
  ```
  ```
  # docker run -v db-volume:/var/lib/mysql/ --name=db-container busybox
  # docker run --volumes-from=db-container --name=mysql-container --net=mysql-nw -d mysql-image
  # docker run --net=mysql-nw --name=pma-container -p 8080:80 -d pma-image
  ```

### 5-4. WordPressを構築する
- 手順
  1. MySQLサーバの構築（前述のコンテナをそのまま使用）
  2. WordPressのDockerfileを作成する
  ```
  # wordpressの取得
  FROM wordpress
  # wordpressの環境変数の設定
  ENV WORDPRESS_DB_HOST=mysql-container \
      WORDPRESS_DB_USER=root \
      WORDPRESS_DB_PASSWORD=dbpass01
  ```
  ```
  # docker build -t wp-image .
  ```
  3. WordPressのコンテナを作成する
  ```
  docker run --net=mysql-nw --name=wp-container -p 80:80 -d wp-image
  ```

### 5-5. Redmineを構築する
- 手順
  1. MySQLサーバの構築（前述のコンテナをそのまま使用）
  2. phpMyAdminでRedmineのDBを作成しておく
  3. RedmineのDockerfileを作成する
  ```
  # redmineイメージの取得
  FROM redmine
  # redmineの環境変数の設定
  ENV REDMINE_DB_MYSQL=mysql-container \
      REDMINE_DB_USERNAME=root \
      REDMINE_DB_PASSWORD=dbpass01
  ```
  ```
  # docker build -t rm-image .
  ```
  4. Redmineコンテナを作成する
  ```
  # docker run --net=mysql-nw --name=rm-container -p 3000:3000 -d rm-image
  ```

## 6. Docker Composeを使ってみよう
### 6-1. Docker Composeとは
- Docker Compose
  - 「Docker-compose.yml」という定義ファイルで複数のコンテナを操作
  - 起動、停止、削除も可能
- Dockerfileとの違い
  - Dockerfile：イメージ取得、設定 => イメージ作成／1つ／機能の一部
  - docker compose：イメージ取得、設定、コンテナ実行／複数／拡張機能

### 6-2. Docker Composeのインストールと使い方
- インストール
```
# apt install docker-compose
```
- docker-compose.yml
```
version: "2"
services:
  ub-test: <=コンテナ名
    image: ubuntu <= 内容
    tty: true
    stdin: true
    command: /bin/bash
```
- コマンド
```
up [オプション] [サービス...]
[-d]    デタッチド・モード: バックグラウンドでコンテナを実行
# docker-compose up
# docker-compose up -d
```
```
run [オプション] [-e キー=バリュー...] サービス [コマンド] [引数...]
# docker-compose run ub-test
```
```
ps [オプション] [サービス...]
[-q]    ID のみ表示
# docker-compose ps
```
```
logs [オプション] [サービス...]
# docker-compose logs
```

### 6-3. Docker Composeの練習
- Apacheをdocker-composeでbuildする
  1. 「httpd.conf」の取出し
  2. 「httpd.conf」のログ出力設定を変更する
  3. docker-compose.ymlを作成する
  ```
  version: "2"
  services:
    #コンテナ名の設定
    web-container:
      #イメージの取得
      image: httpd
      #ローカルのファイルをコンテナにマウント
      volumes:
        - ./index.html:/usr/local/apache2/htdocs/index.html
        - ./httpd.conf:/usr/local/apache2/conf/httpd.conf
      #-pに相当する部分
      ports:
        - 80:80
      #EXPOSEに相当する部分
      expose:
        - 80
      volumes_from:
        - log-container
    #logコンテナの設定
    log-container:
      image: busybox
      volumes:
        - log-volume:/usr/local/apache2/logs/
  #データボリュームの作成
  volumes:
    #データボリューム名
    log-volume:
  ```
  4. 起動する
  ```
  # docker-compose up -d
  # docker-compose stop
  ```
- nginxをリバースプロキシしたApacheをdocker-composeで構築する
  1. Dockerfileと設定ファイルを流用する
  2. docker-compose.ymlを作成する
  ```
  version: "2"
  services:
    #nginxのコンテナ
    rproxy-container:
      build: ./rproxy
      ports:
        - 80:80
    #Apacheのコンテナ
    web-container:
      build: ./web
  ```
  3. 起動する
  ```
  # docker-compose up -d
  # docker-compose stop
  ```

### 6-4. MySQL、phpMyAdmin、WordPress、Redmineのコンテナをdocker-compose.ymlで作る
- docker-compose.yml
```
version: "2"
services:
  #データボリュームコンテナの設計
  db-container:
    image: busybox
    volumes:
      - db-volume:/var/lib/mysql/
  #MySQLの設計
  mysql-container:
    image: mysql
    environment:
      - MYSQL_ROOT_PASSWORD=dbpass01
      - MYSQL_DATABASE=redmine
    command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci --default-authentication-plugin=mysql_native_password
    volumes_from:
      - db-container
    depends_on:
      - db-container
    restart: always
  #phpMyAdminの設計
  pma-container:
    image: phpmyadmin/phpmyadmin
    environment:
      - PMA_HOST=mysql-container
      - PMA_USER=root
      - PMA_PASSWORD=dbpass01
    ports:
      - 8080:80
    depends_on:
      - mysql-container
    restart: always
  #WordPressの設計
  wp-container:
    image: wordpress
    environment:
      - "WORDPRESS_DB_HOST=mysql-container"
      - "WORDPRESS_DB_USER=root"
      - "WORDPRESS_DB_PASSWORD=dbpass01"
    ports:
      - "80:80"
    depends_on:
      - "mysql-container"
    restart: always
  #Redmineの設計
  rm-container:
    image: redmine
    environment:
      - REDMINE_DB_MYSQL=mysql-container
      - REDMINE_DB_USERNAME=root
      - REDMINE_DB_PASSWORD=dbpass01
    ports:
      - 3000:3000
    depends_on:
      - mysql-container
    restart: always
#データボリュームの設計
volumes:
  db-volume:
```
- 起動
```
# docker-compose up -d
```