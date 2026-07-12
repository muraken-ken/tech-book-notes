# Everyday Rails - RSpec によるRails テスト入門
## 1. イントロダクション
### 1-1. なぜRSpec なのか？
- RSpec を使うと読みやすいスペックが簡単に書ける

### 1-2. テストの原則
- テストの原則
  - テストは信頼できるものであること
  - テストは簡単に書けること
  - テストは簡単に理解できること（今日も将来も）
- 注意点
  - スピードは重視しない
  - テストの中では過度にDRY なコードを目指さない
- 一番大事なこと！
  - テストが存在すること

## 2. RSpec のセットアップ
- Gemfile
```
group :development, :test do
gem 'rspec-rails'
end
```
`bundle install`を実行
- .rspec  
```
  --require spec_helper
  --format documentation  #RSpec の出力をドキュメント形式に変更
  --warnings #Rubyの警告あり（必要に応じて）
```
`rspec --help`で他のオプションも調べられる
- binstub を使ってテストスイートの起動時間を速くする
```
$ bundle binstubs rspec-core
```
- config/application.rb(generatorの設定)
```
    config.generators do |g|
      # 色々な記述があるので、一番下に追記する
      g.test_framework :rspec,
                       fixtures: true,            #Varidationを通らない
                       view_specs: false,         #保守が大変=>統合テスト
                       helper_specs: false,
                       routing_specs: false,
                       controller_specs: false,   #非推奨化
                       request_specs: true,
                       system_specs: true
    end
    #必要に応じ、true、falseを変える
```
- 補足
  - 既存のtestディレクトリは削除する
  - viewテスト：信頼性の高いviewテストはコストが高い => 統合テスト

## 3. テストの記述
### 3-1. ベストプラクティス
- 基本
```
describe User do
  # 姓、名、メール、パスワードがあれば有効な状態であること
  it "is valid with a first name, last name, email, and password"
    
  # 名がなければ無効な状態であること
  it "is invalid without a first name"
    
  # 姓がなければ無効な状態であること
  it "is invalid without a last name"
    
  # メールアドレスがなければ無効な状態であること
  it "is invalid without an email address"
    
  # 重複したメールアドレスなら無効な状態であること
  it "is invalid with a duplicate email address"
    
  # ユーザーのフルネームを文字列として返すこと
  it "returns a user's full name as a string"
end
```
  - 期待する結果をまとめて記述（describe）している
  - example（it で始まる1行）一つにつき、結果を一つだけ期待している
  - どのexample も明示的である
  - 各example の説明は動詞で始まっている、should ではない
- describe、contextの使い分け
```
RSpec.describe Note, type: :model do
  #バリデーション
  describe "search message for a term" do
    context "when a match is found" do
    end
    context "when no match is found" do
    end
  end
end
```
  - describe：クラスやシステムの機能に関するアウトラインを記述
  - context：特定の状態に関するアウトラインを記述  
- beforeブロック（各テスト実行前に実行される）  
```
  RSpec.describe Note, type: :model do
    before do
      # このファイルの全テストで使用するテストデータをセットアップする
    end
    #バリデーション
    describe "search message for a term" do
      before do
        # 検索機能の全テストに関連する追加のテストデータをセットアップする
      end
      context "when a match is found" do
      end
      context "when no match is found" do
      end
    end
  end
```
  - before(:each)はAlias before
  - before(:all)、before(:suite)もあるが、基本beforeを使う
- afterブロック
  - exampleの後に処理をすることも可能
- テクニック
  - 期待する結果は能動形で明示的に記述すること
    - チェックする結果はexample 一つに付き一個だけ
  - 起きてほしいことと、起きてほしくないことをテストすること
  - 境界値テストをすること
  - 可読性を上げるためにスペックを整理すること

### 3-2. 基本構文
```
# 2と1を足すと3になること
it "adds 2 and 1 to make 3" do
expect(2 + 1).to eq 3
end
```
- to_not／not_to
```
expect(1 + 1).to_not eq 3 #to_notが一般的
```

### 3-3. マッチャ
- Equivalence（同等性）
```
expect(actual).to eq(expected)  # passes if actual == expected
expect(actual).to_not eql(expected)  # passes if actual != expected
```
- Identity（同一性）
```
expect(actual).to be(expected)    # passes if actual.equal?(expected)
expect(actual).to equal(expected) # passes if actual.equal?(expected)
```
- eq、eql、equal、beの違い
  - eq、eql：変数の値のみ（A == B）
  - equal、be：変数の値だけでなく、object_idも比較（A equal? B）
- Comparisons（比較）
```
expect(actual).to be >  expected
expect(actual).to be >= expected
expect(actual).to be <= expected
expect(actual).to be <  expected
expect(actual).to be_within(delta).of(expected)
```
- Regular expressions（正規表現）
```
expect(actual).to match(/expression/)
```
- Types/classes（タイプ／クラス）
```
expect(actual).to be_an_instance_of(expected) # passes if actual.class == expected
expect(actual).to be_a(expected)              # passes if actual.kind_of?(expected)
expect(actual).to be_an(expected)             # an alias for be_a
expect(actual).to be_a_kind_of(expected)      # another alias
```
- Truthiness（真実性）
```
expect(actual).to be_truthy   # passes if actual is truthy (not nil or false)
expect(actual).to be true     # passes if actual == true
expect(actual).to be_falsy    # passes if actual is falsy (nil or false)
expect(actual).to be false    # passes if actual == false
expect(actual).to be_nil      # passes if actual is nil
expect(actual).to_not be_nil  # passes if actual is not nil
```
- Predicate matchers
```
expect(actual).to be_xxx         # passes if actual.xxx?
expect(actual).to have_xxx(:arg) # passes if actual.has_xxx?(:arg)
```
- Expecting errors（エラー想定）
```
expect { ... }.to raise_error
expect { ... }.to raise_error(ErrorClass)
expect { ... }.to raise_error("message")
expect { ... }.to raise_error(ErrorClass, "message")
```
- [他のマッチャ情報](https://github.com/rspec/rspec-expectations)

### 3-4. バリデーションテスト
- テスト駆動開発  
バリデーションテストは書くべき


## 4. モデルスペック
### 4-1. 作成
```
$ bin/rails g rspec:model user
```

### 4-2. 構文
```
RSpec.describe User, type: :model do
  # 姓、名、メール、パスワードがあれば有効な状態であること
  it "is valid with a first name, last name, email, and password" do
    user = User.new(
      first_name: "Aaron",
      last_name: "Sumner",
      email: "tester@example.com",
      password: "dottle-nouveau-pavilion-tights-furze",
      )
    expect(user).to be_valid
   end
end
```

## 5. 意味あるテストデータの作成
### 5-1. Factory 対 fixture
- fixture：Railsデフォルト
  - 別ファイルのため、可読性が下がる
  - ActiveRecordを使用しないため、脆く壊れやすい
    - バリデーション等は無視される！
- Factory Bot：おすすめ
  - Factory Botも詳細は別ファイル
  - 別ファイル、同ファイルはケースバイケースで使い分け

### 5-2. セットアップ
- Gemfile
```
  group :development, :test do
    gem "rspec-rails"
    gem "factory_bot_rails"
  end
```  
`bundle install`でインストール  
- spec/support/factory_bot.rb
```
  RSpec.configure do |config|
    config.include FactoryBot::Syntax::Methods
  end
```
`rails_helper.rb`で`require`する

### 5-3. アプリケーションにファクトリを追加する
```
$ bin/rails g factory_bot:model user
```
- spec/factories/users.rb
```
  FactoryBot.define do
    factory :user do
      first_name "Aaron"
      last_name "Sumner"
      email "tester@example.com"
      password "dottle-nouveau-pavilion-tights-furze"
    end
  end
```
- 使用例
```
FactoryBot.build(:user)   #インスタンス生成／高速
FactoryBot.create(:user)  #DBに生成（永続）
```
```
FactoryBot.build(:user, first_name: nil) #属性をひとつだけ変える
```
- sequence
```
  FactoryBot.define do
    factory :user do
      first_name "Aaron"
      last_name "Sumner"
      sequence(:email) { |n| "tester#{n}@example.com" }
      password "dottle-nouveau-pavilion-tights-furze"
    end
  end
```
- 重複を無くす
  - ファクトリの継承
  - trait
  - コールバック
- 注意事項
  - ファクトリを使うとテスト中に予期しないデータが作成
  - 無駄にテストが遅くなったりする
- [FactoryBot公式](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md)

## 6. コントローラスペック

コントローラーテストはソフト非推奨化 => 統合テストへ(Request spec)

## 7. フィーチャースペック

フィーチャースペックはシステムテストに移行

## 8. RSpecテスト
- Model Spec／Request spec／System specを中心に書いていく

## 参考資料
- [【初心者向け】テストコードの方針を考える（何をテストすべきか？どんなテストを書くべきか？）](https://qiita.com/jnchito/items/2a5d3e15761fd413657a)
- [使えるRSpec入門・その1「RSpecの基本的な構文や便利な機能を理解する」](https://qiita.com/jnchito/items/42193d066bd61c740612)
- [使えるRSpec入門・その2「使用頻度の高いマッチャを使いこなす」](https://qiita.com/jnchito/items/2e79a1abe7cd8214caa5)
- [使えるRSpec入門・その3「ゼロからわかるモック（mock）を使ったテストの書き方」](https://qiita.com/jnchito/items/640f17e124ab263a54dd)
- [使えるRSpec入門・その4「どんなブラウザ操作も自由自在！逆引きCapybara大辞典」](https://qiita.com/jnchito/items/607f956263c38a5fec24)
