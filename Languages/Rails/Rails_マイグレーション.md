# Active Record マイグレーション
## 1. マイグレーションの概要  
データベーススキーマの継続的な変更 (英語) を、統一的かつ簡単に行なうための便利な手法
- マイグレーションではRubyのDSLを使用
  - 生のSQLを作成する必要がない
  - スキーマとスキーマへの変更をデータベースの種類に依存しない
  
- 1つ1つのマイグレーション：データベースの新しい'version'とみなすことができる  
  - マイグレーションによる変更が加わるたびにテーブル、カラム、エントリが追加または削除される
  - Active Recordは時系列に沿ってスキーマを更新する方法を知っている
  - 履歴のどの時点からでも最新バージョンのスキーマに更新することができる
  - Active Recordは`db/schema.rb`ファイルを更新し、データベースの最新の構造と一致させる

- idという主キーを自動的に追加
- 

## 2. マイグレーションを作成する
- 作成方法
  - 単独のマイグレーションを作成する
  - モデルを作成する
- 作成されるもの
  - `db/migration`ディレクトリに作成される
  - `YYYYMMDDHHMMSS_<ファイル名>.rb`の形式
  - クラス名は<ファイル名>と一致する
  ```
    YYYYMMDDHHMMSS_create_products.rb => CreateProducts
    YYYYMMDDHHMMSS_add_details_to_products.rb => AddDetailsToProducts
  ```  

### 2.1 単独のマイグレーションを作成する
#### カラムの追加・変更  
マイグレーション名が"AddXXX"のような形式
- 記述例
```
  $ rails generate migration AddPartNumberToProducts
```
- 作成されたマイグレーション
```
  class AddPartNumberToProducts < ActiveRecord::Migration[5.0]
    def change
    end
  end
```

#### テーブルの新規作成  
マイグレーション名が"CreateXXX"のような形式
- 記述例
```
  $ rails generate migration CreateProducts
```
- 作成されたマイグレーション
```
  class CreateProducts < ActiveRecord::Migration[5.0]
    def change
      create_table :products do |t|

        t.timestamps
      end
    end
  end
```

#### オプション
- カラム、型の追加
```
 $ rails generate migration AddPartNumberToProducts part_number:string
```
```
  def change
    add_column :products, :part_number, :string
  end
```
- インデックスの追加
```
  $ rails generate migration AddPartNumberToProducts part_number:string:index
```
```
  def change
    add_column :products, :part_number, :string
    add_index :products, :part_number
  end
```
- カラムの削除  
マイグレーション名が"RemoveXXX"のような形式
```
  $ rails generate migration RemovePartNumberFromProducts part_number:string
```
```
  def change
    remove_column :products, :part_number, :string
  end
```
- references (belongs_to も可) を指定
```
  $ rails generate migration AddUserRefToProducts user:references
```
```
  def change
    add_reference :products, :user, foreign_key: true
  end
```
- テーブル結合を生成
```
  $ rails g migration CreateJoinTableCustomerProduct customer product
```
```
  def change
    create_join_table :customers, :products do |t|
      # t.index [:customer_id, :product_id]
      # t.index [:product_id, :customer_id]
    end
  end
```

### 2.2 モデルを作成する  
新しいモデルを追加するマイグレーションには、関連するテーブルを作成する命令が含まれている
- 記述例
```
  $ rails generate model Product name:string description:text
```
- 作成されたマイグレーション
```
  class CreateProducts < ActiveRecord::Migration[5.0]
    def change
      create_table :products do |t|
        t.string :name
        t.text :description

        t.timestamps
      end
    end
  end
```

## 3. マイグレーションファイルの編集
### 3.1 changeメソッドを使う（基本）
- changeメソッド
  - add_column
  - add_foreign_key
  - add_index
  - add_reference
  - add_timestamps
  - change_column_default (:fromと:toの指定は省略できない)
  - change_column_null
  - create_join_table
  - create_table
  - disable_extension
  - drop_join_table
  - drop_table （ブロックを渡さなければならない）
  - enable_extension
  - remove_column（型を指定しなければならない）
  - remove_foreign_key（2番目のテーブルを指定しなければならない）
  - remove_index
  - remove_reference
  - remove_timestamps
  - rename_column
  - rename_index
  - rename_table
- 上記以外のメソッドを使う必要性が生じた場合
  - upとdownメソッドを明示的に書く
  - reversibleメソッドを利用する

#### カラムの型
|データ型|説明|
|---|---|
|string|文字列|
|text|長い文字列|
|integer|整数|
|float|浮動小数|
|decimal|精度の高い小数|
|datetime|日時|
|timestamp|より細かい日時|
|time|時間|
|date|日付|
|binary|バイナリデータ|
|boolean|Boolean型|

#### カラムオプション
|オプション|説明|デフォルト値|
|---|---|---|
|:null|nullを許可するか|true|
|:unique|一意性の制約||
|:default|デフォルトの値|| 
|:limit|カラムの桁数||
|:index|インデックス作成||
|:comment|コメント||
|:precision|数値の桁数||
|:scale|小数点以下の桁数|| 

## 4. マイグレーションの実行
### 4.1 実行
```
  $ rails db:migrate
  $ rails db:migrate VERSION=20080906120000（マイグレーションファイル名の冒頭に付いている数字）
```

### 4.2 ロールバック
```
  $ rails db:rollback
  $ rails db:rollback STEP=3（複数ロールバックしたい場合）
```

### 4.3 redo（ロールバック＋再マイグレーション）
```
  $ rails db:migrate:redo
  $ rails db:migrate:redo STEP=3（複数ロールバックしたい場合）
```

### 4.4 `bin/rails db:setup`  
データベースの作成、スキーマの読み込み、シードデータを用いてデータベースの初期化を実行

### 4.5 `bin/rails db:reset`  
データベースをdropして再度設定 = `rails db:drop db:setup`と同じ

## 5. シードデータ
- `db/seeds.rb`
```
  5.times do |i|
    Product.create(name: "Product ##{i}", description: "A product.")
  end
```
- `rails db:seed`を実行

## 6. マイグレーションの状態を参照
- `rails db:migrate:status`を実行

## 注意事項
- 既存のマイグレーションを変更しない  
  - 変更して再度マイグレーションをしても意味がない（ロールバックして再マイグレーション）
  - 上記の変更は、共同作業者・本番環境で問題を発生させる
  - 新しいマイグレーションを作成し、変更するのが正しい
- Active Recordと参照整合性
  - modelのvalidationでも整合性を図っているが、DB側でも制約をかける
- スキーマファイルは大事
  - `db/schema.rb`を自動生成
  - rails db:schema:loadでスキーマファイルを読み込む方が、高速かつエラーが起きにくい
  - Active Recordのオブジェクトにどのような属性があるのかを概観するのにも便利

## 参考資料
- [Railsガイド：Active Record マイグレーション](https://railsguides.jp/active_record_migrations.html)
- [Railsドキュメント：マイグレーション](https://railsdoc.com/migration)
- [もっとDBコメントを書こう！](https://speakerdeck.com/morimorihoge/20210618-chu-zhang-railsuotuti-in-yin-zuo-rails-number-34)
