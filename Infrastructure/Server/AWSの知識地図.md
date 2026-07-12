# AWSの知識地図
## 第1章　AWSの全体像を押さえる
### 1.1　ITインフラとクラウドを押さえる
#### 1.1.1　ITインフラを構成する要素
- サーバー
- データベース
- ネットワーク
  - LAN
  - WAN
  - インターネット
- TCP/IP
  - アプリケーション層（HTTP、HTTPS、DNS）
  - トランスポート層（TCP、UDP）
  - インターネット層（IP、ICMP）
  - ネットワークインターフェイス層（Ethernet層）
- ストレージ 
  - オブジェクトストレージ
  - ファイルストレージ
  - ブロックストレージ
#### 1.1.2　クラウド以前のITインフラ構築
- 自社で運営
- レンタルサーバー
- データセンターにサーバーを置く
#### 1.1.3　クラウドとはなにか
- ITインフラを構成する要素をインターネットを介してりようできるサービスの総称
- メリット
  - 初期費用がほとんどかからない
  - 従量課金制サービス
  - 短時間で調達できる
  - 最新サービス・アップデートに対応
  - 物理的距離を気にせず、グローバルに展開可能
  - 厳密なリソースの見積りが不要
#### 1.1.4　クラウドコンピューティングの3つのモデル
- プライベートクラウド
  - オンプレミス型：自社で物理的なリソースを持つ
  - ホスティング型：業者が提供するクラウド環境に組織専用のリソースを持つ
- パブリッククラウド
  - AWSなど
- ハイブリッドクラウド
  - 上記の併用
#### 1.1.5　AWSやインフラに関わる「XaaS」
- XaaS(X as a Service)：インターネットを介して提供されるクラウドサービスの総称
  - IaaS(Infrastructure as a Service)
  - PaaS(Platform as a Service)
  - SaaS(Software as a Service)
#### 1.1.6　クラウドを支える技術
- 仮想マシン（VM:Virtual Machine）
- ストレージ
- コンテナ
- 高可用性（HA:High Availability）：いつでもサービス継続
- 災害復旧（DR:Disaster Recovery）
  - Availability Zone
  - region
  - バックアップからの復旧
- スケーラビリティ（Scalability）
  - スケールアップ（scale-up）：サーバーのスペックを上げる
  - スケールアップ（scale-out）：サーバーの台数を増やす
- 冗長化
- SLA（Service Level Agreement）：サービスレベルの担保
### 1.2　AWSの思想・考え方を押さえる
#### 1.2.1　AWS の歴史をふりかえる
#### 1.2.2　「サーバーレス」の普及
- 利用者がサーバーを意識しない
#### 1.2.3　責任共有モデル
- 基盤側：AWS
- アプリケーション：利用者側
#### 1.2.4　AWSの課金モデル
- 従量課金モデル
#### 1.2.5　AWS Well-Architected Framework
- フレームワークを利用：信頼性・可用性・コスト効率が高いワークロードを設計・運用するためのベストプラクティス
  - 運用上の優秀性の柱
    - 運用をコードとして実行する
    - 小規模かつ可逆的な変更を頻繁に行う
    - 運用手順を定期的に改善する
    - 障害を予想する
    - 運用上のすべての障害から学ぶ
  - セキュリティの柱
    - 強力なアイデンティティ基盤の実装
    - トレーサビリティの実現
    - 全レイヤーでセキュリティを適用する
    - セキュリティのベストプラクティスを自働化する
    - 伝送中および保管中のデータの保護
    - データに人の手を入れない
    - セキュリティイベント（インシデント）に備える
  - 信頼性の柱
    - 障害から自動的に復旧する
    - 復旧手順をテストする
    - 水平方向にスケールしてワークロード全体の可用性を高める
    - キャパシティーを推測することをやめる
    - オートメーションで変更を管理する
  - パフォーマンス効率の柱
    - 最新テクノロジーの標準化
    - わずか数分でグローバル展開する
    - サーバーレスアーキテクチャを使用する
    - より頻繁に実験する
    - システムに対する精通の程度を考慮する
  - コスト最適化の柱
    - クラウド財務管理の実装
    - 消費モデルを導入
    - 全体的な効率を測定する
    - 差別化につながらない高負荷の作業に費用をかけるのをやめる
    - 費用を分析および属性化する
  - 持続可能性（サステナビリティ）の柱
    - 影響を把握する
    - サステナビリティ目標の設定
    - 使用率の最大化
    - より効率的な新しいハードウェアとソフトウェアの提供を予測して採用する
    - マネージドサービスを使用する
    - クラウドワークロードのダウンストリームの影響を軽減する
#### 1.2.6　クラウドの中でAWSを選ぶ理由
- サービスや機能が常にアップデートされる
- アップデートにユーザーの要望やフィードバックが積極的に盛り込まれる
- 数あるマネージドサービスをブロックのように組み合わせられる
- いつでもグローバルに展開できる
- 高いセキュリティが確保されている
- 継続的に値下げされている
#### 1.2.7　Design for Failure
- Everything fails all the time.

## 第2章　AWSでWeb環境を構築する
### 2.1　CLIによる構築の基本
#### 2.1.1　CUIとGUIのどちらを選ぶか
- CUI
  - メリット
    - 処理を自働化しやすい
    - テキストなので画面が変わらない
    - マニュアル化しやすく作業ミスを減らせる
    - パラメータ出力時に必要な情報をすぐに出せる
  - デメリット
    - コマンドなどの専門知識が必要
    - コマンドでも手入力ならミスの可能性がある
- GUI
  - メリット
    - 直感的でわかりやすい
    - 画像・動画編集で使いやすい
    - PCやスマホで利用者が使う分にはわかりやすい
  - デメリット
    - アップデートなどで画面が変わることがある
    - CUIと比べて処理が重くなることがある
#### 2.1.2　AWS CLIとは
#### 2.1.3　実行環境について
- AWS CloudShell
  - Amazon Linux2
#### 2.1.4　権限を確認する
#### 2.1.5　本チュートリアルでおこなうこと
#### 2.1.6　よくあるコマンドのエラーを押さえる
#### 2.1.7　おもなLinuxコマンドを押さえる
#### 2.1.8　全体構成図を押さえる
### 2.2　ネットワークを構築する
#### 2.2.1　AWSにおける地理的な概念
- Regions（リージョン）
  - 複数のデータセンターがまとまった、世界中にある物理的な場所
  - 東京リージョン：ap-northeast-1
- Availability Zone（アベイラビリティゾーン）
  - リージョンの中に存在するデータセンターの集まり
  - ap-northeast-1a, 1c, 1d（ゾーンネーム）
  - apne1-az1（ゾーンID）
#### 2.2.2　ネットワーク関連の用語を押さえる
- VPC:Virtual Private Cloud
  - 仮想ネットワーク空間
- サブネット
  - セグメント（VPCの区切り）
  - アベイラビリティゾーンごとに配置
- インターネットゲートウェイ
  - インターネットにつながるゲート
- ルートテーブル
  - サブネット内からのルーティングを定義（サブネットからインターネットゲートウェイへのルーティング）
- パブリックサブネットとプライベートサブネット
  - パブリックサブネット：インターネットゲートウェイへのルーティング設定あり
  - プライベートサブネット：インターネットゲートウェイへのルーティング設定なし
- NATゲートウェイ
  - プライベートサブネットを外部ネットワークに接続する
  - 送信元のIPアドレスをNATゲートウェイのIPアドレスに置き換えて送信する
  - アベイラビリティゾーン上に構築する
  - 料金が発生する
#### 2.2.3　AWS CLIコマンドの基本構成を押さえる
- 基本構成
```
$ aws <コマンド> <サブコマンド> --<オプション> <指定するパラメータ>
```
- `aws`：固定コマンド
- `<コマンド>`：AWSのサービスを指定
- `<サブコマンド>`：サービスに対し行う操作
- `<オプション>`：操作に対する詳細情報
- `<パラメータ>`：詳細情報の中身
#### 2.2.4　VPCを作成する
```
$ aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc, Tags=[{key=Name, Value=aws-loadmap-vpc}]'
```
- `--cidr-block`：CIDR（サイダー）を指定する時に利用（VPC内で使用可能なIPアドレスの範囲指定）
  - `10.0.0.0`：割り当てるIPアドレスの範囲
  - `/16`：上記のアドレスのどの範囲をネットワークにし、どの範囲をホストに割り当てるか
  - 2進数
  ```
  11111111 11111111 00000000 00000000 # /16
  00001010 00000000 00000000 00000000 # 10.0.0.0 => 10.0 ネットワーク部（VPC）、0.0 割り当て可能
  ```
  - プライベートアドレス（範囲）
    - 10.0.0.0 - 10.255.255.255(10/8プレフィックス)
    - 172.16.0.0 - 172.31.255.255(172.16/12プレフィックス)
    - 192.168.0.0 - 192.168.255.255(192.168/16プレフィックス)
- `--tag-specifications`：タグの指定
- 作成したVPCを確認する
```
$ aws ec2 describe-vpcs \
  --filters "Name=tag:Name, Values=aws-loadmap-vpc" \
  --query "vpcs[].[State,VpcId]"
```
#### 2.2.5　サブネットを作成する
- パブリックサブネットを作成する
```
$ aws ec2 create-subnet \
  --vpc-id ${作成したVPC ID} \
  --availability-zone ap-northeast-1a \
  --cidr-block 10.0.0.0/24 \
  --tag-specifications 'ResourceType=subnet, Tags=[{key=Name, Value=aws-loadmap-public-subnet-a}]'

$ aws ec2 create-subnet \
  --vpc-id ${作成したVPC ID} \
  --availability-zone ap-northeast-1c \
  --cidr-block 10.0.1.0/24 \
  --tag-specifications 'ResourceType=subnet, Tags=[{key=Name, Value=aws-loadmap-public-subnet-c}]'
```
- プライベートサブネットを作成する
```
$ aws ec2 create-subnet \
  --vpc-id ${作成したVPC ID} \
  --availability-zone ap-northeast-1a \
  --cidr-block 10.0.10.0/24 \
  --tag-specifications 'ResourceType=subnet, Tags=[{key=Name, Value=aws-loadmap-private-subnet-a}]'

$ aws ec2 create-subnet \
  --vpc-id ${作成したVPC ID} \
  --availability-zone ap-northeast-1c \
  --cidr-block 10.0.11.0/24 \
  --tag-specifications 'ResourceType=subnet, Tags=[{key=Name, Value=aws-loadmap-private-subnet-c}]'
```
- 作成したVPCを確認する
```
$ aws ec2 describe-subnets \
  --filters "Name=vpc-id, Values=${作成したVPC ID}" \
  --query "subnets[].[Tags,CidrBlock]"
```
- パブリックサブネットに設定する
```
$ aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway, Tags=[{key=Name, Value=aws-loadmap-igw}]'
```  
```
$ aws ec2 attach-internet-gateway \
  --internet-gateway-id ${作成したインターネットゲートウェイ ID} \
  --vpc-id ${作成したVPC ID} 
```  
#### 2.2.6　ルーティングを設定する
```
$ aws ec2 create-route-table \
  --vpc-id ${作成したVPC ID} \
  --tag-specifications 'ResourceType=subnet, Tags=[{key=Name, Value=aws-loadmap-public-route-table}]'
```
- ルートテーブルを確認する
```
$ aws ec2 describe-route-table \
  --filters "Name=route-table-id, Values=${ルートテーブル ID}" \
  --query "RouteTables[].Routes" \
  --output table
```
- ルーティングを追加する
```
$ aws ec2 create-route \
  --route-table-id ${ルートテーブル ID} \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id ${インターネットゲートウェイ ID} \
```
- ルートテーブルを確認し、上記の結果反映を確認
- ルートテーブルをサブネットにアタッチする
```
$ aws ec2 attach-route-table \
  --subnet-id ${ap-northeast-1aのパブリックサブネット ID} \
  --route-table-id ${ルートテーブル ID}
$ aws ec2 attach-route-table \
  --subnet-id ${ap-northeast-1cのパブリックサブネット ID} \
  --route-table-id ${ルートテーブル ID}
```  
- 自動でパブリックIPアドレスを割り当てる
```
$ aws ec2 modify-subnet-attribute \
  --subnet-id ${ap-northeast-1aのパブリックサブネット ID} \
  --map-public-ip-on-launch
$ aws ec2 modify-subnet-attribute \
  --subnet-id ${ap-northeast-1cのパブリックサブネット ID} \
  --map-public-ip-on-launch
```
#### 2.3　サーバーを構築する
### 2.3.1　サーバーとは
#### 2.3.2　Amazon EC2とは
- Amazon EC2：サーバー構築の代表的なサービス
- インスタンスタイプと料金
  - t3.micro
    - t：インスタンスファミリー
    - 3：世代
    - micro：インスタンスサイズ
- その他のEC2インスタンス課金方法
  - リザーブドインスタンス：事前予約1～3年
  - Saving Plans：事前予約1～3年
  - スポットインスタンス：空いているリソースの活用（安いがいつでも使えない）
- AMI(Amazon Machine Image)
  - OS＋その上で動作するソフトウェア（ミドルウェア・アプリケーション）を含めたテンプレート
  - インスタンスのコピーや構築が容易
- EBS(Amazon Elastic Block Store)
  - EC2でOSやソフトウェアを保存するストレージ
  - ボリュームタイプ
    - SSDに保存
    - HDDに保存
  - EC2とは別料金
  - インスタンスストア：データが永続できないのであまり使われない（キャッシュなどでは有効）
- セキュリティグループ
  - EC2インスタンスにおけるファイアウォール
  - セキュリティグループルール
    - インバウンド（外→インスタンスへの通信）
    - アウトバウンド（インスタンス内→外への通信）
  - プロトコル
    - TCP・UDP、ICMP
- Amazon リソースネーム（ARN）
  - リソースを識別するための一意な名前
    - arn：ARNの接頭辞
    - パーティション：複数のリージョンをまとめたグループ
    - サービス名：AWSのサービス名
```
arn:${パーティション}:${サービス名}:${リージョン}:${AWSのアカウントID}:${リソースID}
arn:${パーティション}:${サービス名}:${リージョン}:${AWSのアカウントID}:${リソースタイプ}/${リソースID}
arn:${パーティション}:${サービス名}:${リージョン}:${AWSのアカウントID}:${リソースタイプ}:${リソースID}
```
- EC2インスタンスへの接続方法
  - SSHキーを用いた接続が一般的 SSH(Secure Shell) → 踏み台サーバーが必要
  - AWS System Manager
    - 認証：AWS IAM
    - SSH鍵の管理不要
#### 2.3.3　セキュリティグループを作成する
- 接続を自身の環境に限定
- パブリックIP確認用URL：`https://checkip.amazonaws.com/`
```
$ aws ec2 create-security-group \
  --group-id ${作成したセキュリティグループID} \
  --protocol tcp \
  --port 80 \
  --cidr ${自身のパブリックIP}/32
```
- HTTP通信の許可
```
$ aws ec2 authorize-security-group-ingress \
  --description "Security group for aws loadmap ec2" \
  --group-name "aws-loadmap-ec2-sg" \
  --vpc-id ${作成したVPC ID} \
  --tag-specifications 'ResourceType=security-group, Tags=[{key=Name, Value=aws-loadmap-ec2-sg}]'
```
- 確認
```
$ aws ec2 describe-security-group \
  --filters "Name=tag:Name, Values=aws-loadmap-ec2-sg" \
  --query "SecurityGroup[].IpPermissions"
```
#### 2.3.4　インスタンスプロファイルを作成する
- IAMロールをEC2インスタンスに渡すために必要な機構
- 信頼ポリシーを作成する(vim等で作成)
```
$ vim ec2-role-trust-policy-.json
```
- `ec2-role-trust-policy.json`
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Service": "ec2.amazonaws.com" },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
```
$ aws iam create-role \
  --role-name aws-loadmap-ec2-role \
  --assume-role-policy-document file://ec2-role-trust-policy.json
```
- IAMポリシーを設定する（マネージドなポリシーを使用）
```
$ aws iam attach-role-policy \
  --role-name aws-loadmap-ec2-role \
  --policy-arn arn:aws:iam:policy/AmazonSSMManagedInstanceCore
```
- 確認
```
$ aws iam list-attached-role-policies \
  --role-name aws-loadmap-ec2-role
```
- インスタンスプロファイルを作成する
```
$ aws iam create-instance-profile \
  --instance-profile-name aws-loadmap-ec2-instance-profile
```
- インスタンスプロファイルとIAMロールの紐付け
```
$ aws iam add-role-to-instance-profile \
  --instance-profile-name aws-loadmap-ec2-instance-profile \
  --role-name aws-loadmap-ec2-role
```
- 確認
```
$ aws iam get-instance-profile \
  --instance-profile-name aws-loadmap-ec2-instance-profile \
  --query "InstanceProfile.Roles[].[RoleName,AssumeRoleProfileDocument]"
```
#### 2.3.5　EC2を起動する
- Amazon Linux2のAMI IDを確認
```
$ aws ssm get-parameters \
  --names /aws/service/ami-smazon-linux-latest/amzn-ami-hvm-x86_64-gp2 \
  --query "Parameters[].Value"
```
- インスタンスの起動
```
$ aws ec2 run-instances \
  --image-id ${上記で確認したAMI ID} \
  --count 1 \
  --instance-type t3.micro \
  --security-group-ids ${セキュリティグループID} \
  --subnet-id ${パブリックサブネットID} \
  --iam-instance-profile Name=aws-loadmap-ec2-instance-profile \
  --block-device-mappings "DeviceName=/dev/xvda,Ebs={VolumeSize=8,DeleteOnTermination=true,VolumeType=gp3}" \
  --tag-specifications "ResourceType=instance,tags=[{key=Name,Value=aws-loadmap-ec2-1}]"
```
- インスタンスの稼働確認
```
$ aws ec2 describe-instance \
  --filters "Name=tag:Name, Values=aws-loadmap-ec2-1" \
  --query "Reservations[].instance[].InstanceId"
```
- インスタンスのステータスを確認する
```
$ aws ec2 describe-instance-status \
  --instance-ids ${インスタンスID}
```
#### 2.3.6　Systems ManagerでEC2へ接続する
- 接続対象のインスタンス情報を確認する
```
$ aws ec2 describe-instances \
  --instance-ids ${インスタンスID} \
  --query "Reservations[].instances[].PrivateIpAddress"
  [
    "10.0.x.x"
  ]
```
- インスタンスIDに接続する
```
$ aws ssm start-session \
  --target ${作成したインスタンス ID}
```
- プライベートIPアドレスを確認する
```
$ ip addr show eth0
```
#### 2.3.7　EC2上でWebサーバーを構築する
- Apacheをインストールする
```
$ sudo yum update -y
$ sudo yum install -y httpd
```
- Apacheを起動する
```
$ sudo systemctl start httpd
# $ sudo systemctl status httpd プロセスの確認
# $ sudo systemctl stop httpd プロセスの停止
```
```
$ sudo systemctl enable httpd # インスタンスの起動に合わせてプロセスも起動
# $ sudo systemctl is-enabled httpd インスタンスの起動に合わせてプロセスも起動する状態かを確認
# $ sudo systemctl disable httpd プロセスの停止 インスタンスの起動に合わせてプロセスも起動する設定の解除
```
- サーバーへアクセスする
```
$ exit # Cloud Shellに戻る
$ aws ec2 describe-instances \
  --instance-ids ${インスタンスID} \
  --query "Reservations[].Instances[].PublicIpAddress"
```
- アクセス
```
  http://x.x.x.x
```
- ブラウザの表示を変える
```
$ sudo chmod 777 /var/www/html
$ sudo echo $(curl https://checkip.amazonaws.com/) > /var/www/html/index.html
```
#### 2.3.8　AMIでもう1台のEC2を立ち上げる
- AMIを取得する
```
$ aws ec2 create-image \
  --instance-id ${インスタンスID} \
  --name "aws-loadmap-ec2-ami" \
  --description "AMI for aws loadmap" \
  --tag-specifications "ResourceType=image, Tags=[{key=Name, Value=aws-loadmap-ec2-ami}]"
```
- 取得状況の確認（時間がかかる）：available, pending, failed
```
$ aws ec2 describe-images \
  --filters "Name=tag:Name, Values=aws-loadmap-ec2-ami" \
  --query "Images[].State"
```
- AMI IDの確認
```
$ aws ec2 describe-images \
  --filters "Name=tag:Name, Values=aws-loadmap-ec2-ami" \
  --query "Images[].ImageId"
```
- 2台目のEC2を立ち上げる
```
$ aws ec2 run-instances \
  --image-id ${取得したAMI ID} \
  --count 1 \
  --instance-type t3.micro \
  --security-group-ids ${セキュリティグループID} \
  --subnet-id ${パブリックサブネットID（アベイラビリティゾーンは異なる）} \
  --iam-instance-profile Name=aws-loadmap-ec2-instance-profile \
  --block-device-mappings "DeviceName=/dev/xvda,Ebs={VolumeSize=8,DeleteOnTermination=true,VolumeType=gp3}" \
  --tag-specifications "ResourceType=instance,tags=[{key=Name,Value=aws-loadmap-ec2-2}]"
```
- パブリックIPアドレスの変更
```
$ aws ssm start-session \
  --target ${作成したインスタンス ID}
```
```
$ sudo echo $(curl https://checkip.amazonaws.com/) > /var/www/html/index.html
```
#### 2.3.9　ALBでEC2×2台をひもづける
- Elastic Load Balancing(ELB)
  - Classic Load Balancing(CLB)：前世代のELB
  - Application Load Balancing(ALB)：L7層のアプリケーション層でルーティング、セキュリティグループで制限可
  - Network Load Balancing(NLB)：L4層のトランスポート層でルーティング、セキュリティグループで制限不可
- ALBを構築する
```
$ aws ec2 create-security-group \
  --description "Security group for aws loadmap alb" \
  --group-name "aws-loadmap-alb-sg" \
  --vpc-id ${作成したVPC ID} \
  --tag-specifications 'ResourceType=security-group, Tags=[{key=Name, Value=aws-loadmap-alb-sg}]'
```
```
$ aws ec2 authorize-security-group-ingress \
  --group-id ${作成したALBのセキュリティグループID} \
  --protocol tcp \
  --port 80 \
  --cidr ${自身のパブリックIP}/32
```
- ALBからの通信を許可する
```
$ aws ec2 authorize-security-group-ingress \
  --group-id ${EC2側のセキュリティグループID} \
  --protocol tcp \
  --port 80 \
  --source-group ${ALB側のセキュリティグループ}
```
- ALBの作成（時間がかかる：10分ほど）
```
$ aws elbv2 create-loadmap-balancer \
  --name aws-loadmap-alb \
  --subnets ${ap-northeast-1aのパブリックサブネットID} ${ap-northeast-1cのパブリックサブネットID}
  --security-groups ${ALB側のセキュリティグループID} \
  --tags key=Name, Value=aws-loadmap-alb 
```
- ALB作成状況
```
$ aws elbv2 describe-loadmap-balancers \
  --names aws-loadmap-alb \
  --query LoadBalancers[].[LoadBalancerArn.State]
```
- ターゲットグループを作成する
```
$ aws elbv2 create-target-group \
  --name aws-loadmap-alb-tg \
  --protocol HTTP \
  --port 80 \
  --vpc-id "#{作成したVPC ID}" \
  --health-check-interval-seconds 10 \
  --health-check-timeout-seconds 3 \
  --healthy-threshold-count 2 \
  --tags key=Name,Value=aws-loadmap-alb-tg
```
- ターゲットグループのARNを確認
```
$ aws elbv2 describe-target-groups \
  --names aws-loadmap-alb-tg \
  --query TargetGroups[].TargetGroupArn
```
- インスタンス2台を登録
```
$ aws elbv2 register-target \
  --target-group-arn ${ターゲットグループのARN} \
  --targets Id=${登録するインスタンスID} Id=${登録するインスタンスID}
```
- リスナーを作成する
```
$ aws elbv2 create-listener \
  --loadmap-balancer-arn ${作成したALBのARN} \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward, TargetGroupArn=${作成したターゲットグループのARN}
```
- ヘルスチェックの状況を確認する
```
$ aws elbv2 describe-target-health \
  --target-group-arn ${ターゲットグループのARN}
```
= ALBにアクセスする
```
$ aws elbv2 describe-load-balancers \
  --names aws-loadmap-alb \
  --query LoadBalancers[].DNSName
```
#### 2.3.10　Elastic IPアドレス(EIP)でIPアドレスを固定する
- IPアドレスを固定で運用
  - Elastic IPアドレス(EIP)
- EIPをAWSアカウントに割り当てる
```
$ aws ec2 allocate-address \
  --tag-specifications "ResourceType=elastic-ip, Tags=[{key=Name, Value-aws-loadmap-elp-1}]"
$ aws ec2 allocate-address \
  --tag-specifications "ResourceType=elastic-ip, Tags=[{key=Name, Value-aws-loadmap-elp-2}]"
```
- EIPをEC2インスタンスに関連づける
```
$ aws ec2 allocate-address \
  --instance-id ${aws-loadmap-ec2-1のインスタンスID} \
  --allocation-id ${aws-loadmap-eip-1のAllocationId}

$ aws ec2 allocate-address \
  --instance-id ${aws-loadmap-ec2-2のインスタンスID} \
  --allocation-id ${aws-loadmap-eip-2のAllocationId}
``` 
- 確認
```
$ aws ec2 describe-address \
  --filters "Name=tag.Name, Values=aws-loadmap-eip-1" \
  --query "Addresses[].[InstanceId.publicIp]"
```
- サーバーログイン
```
$ aws ssm start-session --target ${対象のインスタンスID}
```
#### 2.3.11　Auto Scalingとは
- ヘルスチェック
  - EC2ヘルスチェック
  - ELBヘルスチェック
#### 2.3.12　スケーリングのオプション
- 手動
- オートヒーリング：現状維持
- スケジュールに基づくスケーリング
- 受容に基づくスケーリング
- 予測スケーリング
#### 2.3.13　Auto Scalingを導入する前に検討すべきこと
- DBなどEC2の後ろにある処理を気にする
- IPアドレスの枯渇
- スパイクアクセスには堪えられないケースもある
- EC2インスタンス、アプリケーションをステートレスにする
- 構成管理
  - ゴールデンイメージ
#### 2.3.14　Auto Scalingを体験する
- 起動テンプレート
  - `launch-template.json`
  ```
  {
    "IamInstanceProfile": {
      "Name": "ami-loadmap-ec2-instance-profile"
    },
    "ImageId": "ami-0e3861265a3a335cc",
    "InstanceType": "t3.micro",
    "BlockDeviceMappings": [
      {
        "DeviceSize": 8,
        "Ebs": {
          "VolumeSize": 8,
          "DeleteOnTermination": true,
          "VolumeType": "gp3"
        }
      }
    ],
    "SecurityGroupIds": [
      "${作成したセキュリティグループID}"
    ],
    "TagSpecifications": {
      {
        "ResourceType": "instance",
        "Tags": [
          {
            "Key": "Name",
            "Value": "aws-loadmap-asg-instance"
          }
        ]
      }
    }
  }
  ```
- 起動テンプレートの作成
```
$ aws ec2 create-launch-template \
  --launch-template-name aws-loadmap-template \
  --launch-template-data file://launch-template.json
```
- 起動テンプレートのIDを確認
```
$ aws ec2 describe-launch-template \
  --launch-template-names aws-loadmap-template \
  --query "LaunchTemplate[].LaunchTemplateId"
```
- AutoScalingグループを作成(インスタンスの起動には数分はかかる)
```
$ aws AutoScaling create-auto-scaling-group \
  --launch^template LaunchTemplateId=${作成した起動テンプレート}, Version='$Latest' \
  --auto-scaling-group-name aws-loadmap-asg \
  --min-size 1 \
  --desired-capacity 2 \
  --max-size 2 \
  --vpc-zone-identifier "${作成したap^northeast-1aのパブリックサブネットID}, "${作成したap^northeast-1cのパブリックサブネットID},"
```
- 確認
```
$ aws ec2 describe-instance \
  --query "Reservations[].Instances[].InstanceId" \
  --filter "Name=instance-state-code.Values=16"
```
- AutoScalingグループのインスタンス台数を増やす
```
$ aws autoscaling update-auto-scaling-group \
  --auto-scaling-group-name aws-loadmap-asg \
  --max-size 5 \
  --describe-capacity 5
```
- 確認
```
$ aws ec2 describe-instance \
  --query "Reservations[].Instances[].InstanceId" \
  --filter "Name=instance-state-code.Values=16"
```
### 2.4　データベースを構築する
#### 2.4.1　データベースとは
#### 2.4.2　AWSでどのRDBを選択するか
- Amazon Aurora
- インスタンスとストレージガ別コンポーネンツになっている
- ストレージが最大128TBまで自動的に拡張する
#### 2.4.3　実際にデータベースを構築する
- サブネットグループを作成する（private subnet）
```
$ aws rds create-db-subnet-group \
  --db-subnet-group-name aws-loadmap-rds-subnet-group \
  --db-subnet-group-description "Rds subnet group for aws loadmap" \
  --subnet-ids '["${作成したap-northeast-1aのプライベートサブネットID},${作成したap-northeast-1cのプライベートサブネットID}"]'
``` 
- 確認する
```
$ aws ec2 describe-db-subnet-groups \
  --db-subnet-group-name aws-loadmap-rds-subnet-group
```
- セキュリティグループを作成する
```
$ aws ec2 create-security-group \
  --description "Security group for aws loadmap aurora" \
  --group-name "aws-loadmap-aurora-sg" \
  --vpc-id "${作成したVPC ID}" \
  --tag-specifications "ResourceType=security-group, tags=[{key=name,Value=aws-loadmap-aurora-sg}]"
```
- ルールの付与
```
$ aws ec2 authorize-security-group-ingress \
  --group-id ${AuroraのセキュリティグループID} \
  --protocol tcp \
  --port 3306 \
  --source-group ${EC2側のセキュリティグループID}
```
- Auroraクラスターを構築する
```
$ aws rds create-db-cluster \
  --db-cluster-identifier aws-loadmap-aurora-cluster \
  --engine aurora-mysql \
  --engine-version 8.0 \
  --master-username ${user-name} \
  --master-user-password ${password} \
  --db-subnet-group-name aws-loadmap-rds-subnet-group \
  --vpc-security-group-ids ${作成したAuroraのセキュリティグループID} \
  --tags "Key=Name,Value=aws-loadmap-aurora-cluster"
```
- 確認する
```
$ aws rds describe-db-cluster
  --deb-cluster-identifier aws-loadmap-aurora-cluster
```
- インスタンスを作成する
```
$ aws rds create-db-instance \
  --db-instance-identifier aws-loadmap-aurora-instance \
  --deb-cluster-identifier aws-loadmap-aurora-cluster \
  --engine aurora-mysql \
  --db-instance-class db.t4g.medium \
  --tag "Key=Name, Value=aws-loadmap-aurora instance1"

$ aws rds create-db-instance \
  --db-instance-identifier aws-loadmap-aurora-instance \
  --deb-cluster-identifier aws-loadmap-aurora-cluster \
  --engine aurora-mysql \
  --db-instance-class db.t4g.medium \
  --tag "Key=Name, Value=aws-loadmap-aurora instance2"
```
- 確認する
```
$ aws rds describe-db-instance
  --db-instance-identifier aws-loadmap-aurora-instance1
$ aws rds describe-db-instance
  --db-instance-identifier aws-loadmap-aurora-instance2
```
- 接続テスト
```
$ aws ssm start-session \
  --target ${作成したインスタンスID}
```
- MySQLをインストール
```
$ sudo yum install -y mysql
```
- EC2からAuroraに接続
```
$ mysql -u ${作成したuser名} -p -h ${RDSのエンドポイント}
```
- 確認する
```
$ aws ssm start-session --target i-xxxxx
$ sudo su -
$ amazon-linux-extras install -y php8.0
$ yum install -y php-mbstring php-xml
$ cd /var/www/html
```
- phpMyAdminのインストール
```
# wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-language.tar.gz
# mkdir phpMyAdmin
# tar -xzf phpMyAdmin-latest-all-language.tar.gz -C phpMyAdmin --strip-components 1
# rm -f phpMyAdmin-latest-all-language.tar.gz
# cd phpMyAdmin
# vim config.sample.inc.php
# mv config.sample.inc.php config.inc.php
# systemctl restart php-fpm
# systemctl restart httpd
```
- アクセス
```
http://インスタンスのパブリックIP/phpMyAdmin
```
- DB作成の確認
```
$ mysql -u ${作成したuser名} -p -h ${RDSのエンドポイント}
```
#### 2.4.4　アプリケーションからDBへ接続してみる
### 2.5　リソースの削除
#### 2.5.1　リソースを確認する
#### 2.5.2　各リソースを削除するコマンド
- RDS Auroraインスタンスの削除
```
$ aws rds delete-db-instance \
  --db-instance-identifier aws-loadmap-aurora-instance1
$ aws rds delete-db-instance \
  --db-instance-identifier aws-loadmap-aurora-instance2
```
- RDS Auroraクラスターの削除
```
$ aws rds delete-db-cluster \
  --db-cluster-identifier aws-loadmap-aurora-cluster \
  --skip-final-snapshot
```
- RDSサブネットグループの削除
```
$ aws rds delete-db-subnet-group \
  --db-subnet-group-name aws-loadmap-subnet-group
```
- ALBの削除
```
$ aws elbv2 delete-load-balancer \
  --load-balancer-arn ${対象となるELBのARN}
```
- ターゲットグループの削除
```
$ aws elbv2 delete-target-group \
  --target-group-arn ${対象となるターゲットグループのARN}
```
- EC2インスタンスの削除
```
$ aws ec2 terminate-instances \
  --instance-ids ${対象となるインスタンスID} ${対象となるインスタンスID}
```
- EIPの解放
```
$ aws ec2 release-address \
  --allocation-id ${対象となるEIPの割り当てID}

$ aws ec2 release-address \
  --allocation-id ${対象となるEIPの割り当てID}
```
- AutoScalingグループの削除
```
$ aws autoscaling delete-auto-scaling-group \
  --auto-scaling-group-name aws-loadmap-asg
```
- 起動テンプレートの削除
```
$ aws ec2 delete-launch-template \
  --launch-template-id ${対象となる起動テンプレートのID}
```
- RDSで使用したセキュリティグループの削除
```
$ aws ec2 delete-security-group \
  --group-id ${対象となるセキュリティグループのID}
```
- EC2で使用したセキュリティグループの削除
```
$ aws ec2 delete-security-group \
  --group-id ${対象となるセキュリティグループのID}
```
- ALBで使用したセキュリティグループの削除
```
$ aws ec2 delete-security-group \
  --group-id ${対象となるセキュリティグループのID}
```
- インターネットゲートウェイの削除
```
$ aws ec2 detach-internet-gateway \
  --internet-gateway-id ${対象となるインターネットゲートウェイのID} \
  --vpc-id ${対象となるVPCのID}

$ aws ec2 delete-internet-gateway \
  --internet-gateway-id ${対象となるインターネットゲートウェイのID} \
```
- サブネットの削除
```
$ aws ec2 delete-subnet \
  --subnet-id ${対象となるサブネットのID}
```
- ルートテーブルの削除
```
$ aws ec2 delete-route-table \
  --route-table-id ${対象となるルートテーブルのID}
```
- VPCの削除
```
$ aws ec2 delete-vpc \
  --vpc-id ${対象となるVPCのID}
```
- インスタンスプロファイルとIAMロールの関連付け解除
```
$ aws iam remove-role-from-instance-profile \
  --instance-profile-name aws-loadmap-ec2-instance-profile \
  --role-name aws-loadmap-ec2-role
```
- インスタンスプロファイルの削除
```
$ aws iam delete-instance-profile \
  --instance-profile-name aws-loadmap-ec2-instance-profile
```
- IAMロールとIAポリシーの関連付け解除
```
$ aws iam detach-role-policy \
  --role-name aws-loadmap-ec2-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore
```
- IAMロールの削除
```
$ aws iam delete-role \
  --role-name aws-loadmap-ec2-role
```
- AMIの削除
```
$ aws ec2 deregister-image \
  --image-id ${対象のAMI ID}
```
- EC2スナップショットの削除
```
$ aws ec2 delete-snapshot \
  --snapshot-id ${対象のスナップショットID}
```
## 第3章　安全にAWSを使うための基礎知識を押さえる
### 3.1 IAMの基本を押さえる
#### 3.1.1　クラウドサービスのリスクを押さえる
- 機密情報の漏洩による企業信頼の喪失、金銭的損失
- 第三者の攻撃への踏み台として利用されることによる、企業信頼の喪失
- システムやデータの破壊・喪失によるシステムの停止、ビジネスの機会損失
#### 3.1.2　IAMとは
- IAM(AWS Identity and Access Management)
#### 3.1.3　IAMユーザー
- AWSにアクセスするためのIDの役割
  - AWSマネジメントコンソールへログイン（パスワード認証）
  - プログラムによるアクセス（アクセスキーとシークレットキーを用いたアクセス）
- IAMユーザーは利用者ごとに作成する（トレース可能とするため）
#### 3.1.4　IAMユーザーグループ
- 複数のIAMユーザーをグループ化する機能
- 使用例
  - 役割に応じたIAMユーザーグループを作成する
  - 各IAMユーザーグループに適切なアクセス権限を割り当てる
  - IAMユーザーを役割に応じたIAMユーザーグループに所属させる
#### 3.1.5　IAMポリシー
- AWSサービスやリソースへのアクセス権限を設定
  - ポリシードキュメントをJSON形式で記述する
  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": "ec2:Describe",
        "Resource": "*"
      },
    ]
  }
  ```
  - IAMコンソールのGUIで作成する
- 明示的な拒否が優先
- 暗黙的な拒否
  - 明確に許可も拒否もされなかった場合
- AWS管理ポリシーとカスタマー管理ポリシー
  - AWS管理ポリシー：AWSがあらかじめ用意した
  - カスタマー管理ポリシー：ユーザーが用意した
#### 3.1.6　IAMロール
- IAMユーザーと同様にアタッチされたポリシーで許可されたアクセス権限を得るしくみ
  - IAMユーザー：人、IAMロール：権限を必要とする人
  - IAMユーザー：永続、IAMロール：期限あり
  - IAMユーザー：リソース不可、IAMロール：リソース不可
- 信頼ポリシー
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Service": "ec2.amazonaws.com" },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
- AssumeRole
  - sts:AssumeRole：一時的な認証情報
#### 3.1.7　最小権限を付与する
### 3.2　AWSアカウントの保護
#### 3.2.1　ルートユーザーとは
- AWSアカウントと紐付け
  - 全ての権限（超強力）
  - 日常的な利用は非推奨
#### 3.2.2　MFAの設定
- MFA(multi-factor-authentication)：多要素認証
  - ルートユーザーなど強力な権限のユーザーには必須
#### 3.2.3　ほかのAWSアカウントへのアクセスにスイッチロールを利用する
- 他アカウントのIAMユーザーも設定可能
  - IAMユーザーの管理数を減らせる
### 3.3　セキュアな環境のためにやっておくべきこと
#### 3.3.1　ルートユーザーや強い権限をもつユーザーの使用を控える
- 気をつけるキーワード
  - Administrator
  - Power
  - FullAccess
  - create
- IAM Access Analyzerによるポリシーの生成
#### 3.3.2　ソースコード内に認証情報をハードコーディングしない
- 以下の情報はダメ
  - アクセスキーとシークレットアクセスキー
  - サーバーやデータベースにアクセスする際のパスワード
  - SSH鍵
- ParqmeterStore機能やAWS Secrets Managerの利用を推奨
#### 3.3.3　認証情報のローテーション
#### 3.3.4　リソースへのアクセスを限定する
#### 3.3.5　自分のアカウントに何が起こったかを記録しておく
- CloudTrail
  - AWSアカウント内のAPIリクエストを記録するサービス
- AWS Config：AWSのリソースへの変更を記録
- VPCフローログ：IPトラフィックを記録する機能
#### 3.3.6　請求アラートを設定する
### 3.4　不正利用の警告通知があったらやるべきこと
#### 3.4.1　無視してはいけないAWSからの通知
#### 3.4.2　おもな不正利用通知のパターン
### 3.5　安全なAWS利用を支えるサービス・ツール
#### 3.5.1　AWS WAF
- Web Application Firewall(WAF)：SQLインジェクション、XSS攻撃など
#### 3.5.2　GuardDuty
- AWSの脅威検知サービス
#### 3.5.3　Trusted Advisor
- AWSのベストプラクティスにのっとった環境の利用をするための情報提供
#### 3.5.4　Security Hub
- Trusted Advisorより高度なチェックが実施可能
#### 3.5.5　AWS Vault
- IAMの認証情報をOSのキーストアに保存するオープンソースソフトウェア
#### 3.5.6　git-secrets
- 認証情報をGitのリポジトリに混入させないためのオープンソース
## 第4章　現場でAWSを使いこなすサービスを押さえる
### 4.1　マネージドサービスを上手に活用する
#### 4.1.1　マネージドサービスを利用する利点
- 初期構築・開発コストを抑えられる
- 運用コストを抑えられる
- 専門的な予備知識がなくても使える
### 4.2　インフラストラクチャサービスを押さえる
#### 4.2.1　コンピューティング
- Amazon EC2
- Amazon Lightsail：機能を削ぎ落としシンプルなVPS（WordPress、Redmineなど）
- Amazon Lambda：アプリケーションコードをデプロイして使用可能
- Amazon App Runnner：フルマネージドなコンテナデプロイ・実行サービス
- ロケーションの拡張
  - AWS Local Zones：地理的な拡張
  - AWS WaveLength：通信キャリアの5Gエッジロケーションへの拡張
  - AWS Outposts：オンプレミスへ設置したハードウェアへの拡張
#### 4.2.2　コンテナ
- Amazon Elastic Container Registry(ECR)
  - Dockerコンテナイメージの保存・管理・デプロイ
- Amazon Elastic Container Service(ECS)
  - コントロールプレーン
- Amazon Elastic Kubernetes Service(EKS)
  - コントロールプレーン
- AWS Fargate
  - データプレーン
#### 4.2.3　データベース
- Amazon RDS/Amazon Aurora
  - Amazon Auroraの対応DB
  - MySQL互換
  - PostgreSQL互換
  - Amazon RDSの対応DB
  - MySQL
  - MariaDB
  - PostgreSQL
  - Oracle DB
  - Microsoft SQL Server
- Amazon DynamoDB
  - キー・バリュー型のフルマネージドサービス
- Amazon Keyspaces
  - OSSキー・バリュー型DB Apache Cassandra互換のマネージドサービス
- Amazon ElasticCache
  - インメモリデータベース（memcachedとRedis）
- Amazon DocumentDB
  - MongoDBと互換のドキュメントデータベース
- Amazon Timestream
  - 時系列データベース：IoTセンサーデータ、ログなど
- Amazon Neptune
  - グラフ構造データ
- Amazon Quantum Ledger Database(QLDB)
  - 過去データや変更履歴の保持
#### 4.2.4　ストレージ
- オブジェクトストレージサービス
  - Amazon Simple Storage Service(S3)
- 共有ファイルシステム
  - Amazon Elastic File System(EFS)
  - Amazon FSx
- ブロックストレージ
  - Amazon Elastic Block Store(EBS)：EC2からのローカルボリューム
  - Amazon EC2インスタンスストア：インスタンスに固有に付与
#### 4.2.5　ネットワーク
- Amazon Virtual Private Cloud(VPC)
- Amazon CloudFront
  - コンテンツ配信：Contents Delivery Network(CDN)
- 外部との接続
  - AWS Transit Gateway：複数のVPCや拠点を接続するHUB
  - AWS Direct Connect：専用線
  - AWS Site to Site VPN：インターネット経由接続
  - AWS Client VPN：クライアント端末からの接続
- ネットワーク・通信の管理
  - Amazon VPC IP Address Manager：IPアドレス管理
  - AWS Network Firewall：ファイアウォール
  - AWS Firewall Manager：セキュリティ管理サービス
  - AWS Cloud Map：クラウドリソース検出サービス 
### 4.3　アプリケーション開発のサービスを押さえる
#### 4.3.1　開発ツール
- AWS Cloud9：IDE
- AWS Codeシリーズ
  - AWS CodeCommit：Gitリポジトリホスティングサービス（S3保存）
  - AWS CodeBuild：コードのビルド・テスト
  - AWS CodeDeploy
  - Code Pipeline
#### 4.3.2　アプリケーション統合サービス
- Amazon EventBridge：イベントを通じてアプリケーション間を接続・連携するサービス
- Amazon Simple Notification Service(SNS)：A2AtoとA2Pのフルマネージドサービス
- Amazon Simple Queue Service(SQS)：プル型メッセージキューイングサービス
- Amazon MQ:メッセージブローカー
- AWS Step Functions：分散アプリケーション・マイクロサービスを構築・実行できる
- Amazon API Gateway：RESTful APIをデプロイできるサービス
- Amazon AppSync：GraphQL APIサービス
### 4.4　データ分析と機械学習のサービス
#### 4.4.1　データ分析の流れ
- データの収集
- データレイクに一元化する
- 分析しやすい形に処理する
- データを分析する
- 分析結果を可視化する
#### 4.4.2　データ収集のサービス
- Amazon kinesis
- Amazon Managed Streaming for Apache kafka(MSK)
#### 4.4.3　データ処理・ETLサービス
- AWS Glue
- Amazon EMR
#### 4.4.4　データ分析サービス
- Amazon Athena
- Amazon Redshift
#### 4.4.5　データ可視化サービス
- Amazon QuickSight
- Amazon OpenSearch Service
#### 4.4.6　機械学習とAIサービスの全体像
#### 4.4.7　AIサービス
- Amazon Comprehend
- Amazon Forecast
- Amazon Fraud Detector
- Amazon Kendra
- Amazon Lex
- Amazon Personalize
- Amazon Polly
- Amazon Rekognition
- Amazon Textract
- Amazon Transcribe
- Amazon Translate
- Amazon CodeGuru
- Amazon DevOpsGuru
#### 4.4.8　機械学習サービス
- Amazon SageMaker
#### 4.4.9　機械学習フレームワーク・インフラストラクチャ
- AWS Deep Learning Containers
- AWS Deep Learning AMIs
### 4.5　メディア配信のサービス
#### 4.5.1　動画配信の基礎
- 取込
- 変換
- 配信
#### 4.5.2　AWSの動画配信サービス
- AWS Media Services
  - AWS Elemental MediaConnect
  - AWS Elemental MediaConvert
  - AWS Elemental MediaLive
  - AWS Elemental MediaPackage
  - AWS Elemental MediaStore
  - AWS Elemental MediaTailor
#### 4.5.3　動画配信アプライアンス
- AWS Elemental Live
- AWS Elemental Server
- AWS Elemental Delta
- AWS Elemental Conductor
- AWS Elemental Link
### 4.6　エンタープライズにおける活用
#### 4.6.1　ビジネス向けアプリケーション
- AWS Directory Service
- AWS WorkSpaces
- AWS AppStream 2.0
- AWS WorkDocks
- Amazon WorkMail
- Amazon Chime
#### 4.6.2　サーバーとデータ移行
- 移行の管理
  - AWS Application Discovery Service
  - AWS Migration Hub
- サーバー移行
  - AWS Application Migration Service(AWS MGN)
- データベース移行
  - AWS Database MigrationService(DMS)
- データ移行
  - AWS Transfer Family
  - AWS DataSync
  - AWS Snow Family
### 4.7　IoTのサービスを押さえる
#### 4.7.1　AWS IoT Core
- IoTデバイスの通信
#### 4.7.2　その他のIoTサービス
- AWS IoT Analytics
- AWS Iot Events
- AWS IoT Device Defender
- AWS IoT Device Management
#### 4.7.3　デバイスの利用
- AWS IoT Greengrass
- Amazon FreeRTOS
- AWS IoT 1-Click