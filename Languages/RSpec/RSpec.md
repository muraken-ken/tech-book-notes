# RSpec
## 1. RSpecとは
### 1-1. RSpec公式ドキュメント
- [RSpec公式ドキュメント](https://rspec.info/)
- 歴史<br>
  - 2005年にSteven Bakerの実験として始まる
  - 2006年にDavid Chelimskyがプロジェクトリーダー
  - 2007年5月：RSpec 1.0
  - 2008年末：Chad Humphriesが新しいスペックランナーであるMicronautを作成
  - 2010年初頭：RSpec 1.3
  - 2010年10月：RSpec 2.0
  - 2014年6月：RSpec 3.0
  - 2020年3月：RSpec Rails 4.0
- RSpecの構成ライブラリ
  - [rspec-core](https://github.com/rspec/rspec-core)
  - [rspec-expectations](https://github.com/rspec/rspec-expectations)
  - [rspec-mocks](https://github.com/rspec/rspec-mocks)
  - [rspec-rails](https://github.com/rspec/rspec-rails)

### 1-2. RSpecの特徴
- テスト駆動開発(TDD)とビヘイビア駆動開発(BDD)
  - テスト駆動開発（test-driven development; TDD）<br>
  テストを書く => コードを書く => 開発効率が向上、テスト欠落なし
  - ビヘイビア駆動開発（behavior-driven development; BDD）<br>
  テスト駆動開発から派生したソフトウェア開発手法で「ソフトウェアの振る舞い（behavior）を自然言語（英語）で記述する」ことが重要<br>
  「テスト + 仕様書」になっている
- Rubyでのテストフレームワーク
  - Minitest：Test::Unitと互換性を保ちつつ、BDDを取り入れたもの。Rails標準。
  - RSpec：BDDをコンセプトにしたテストフレームワーク。Railsで主流。

## 2. rspec-rails
### 2-1. rspec-railsとは

rspec-railsは、RSpecテストフレームワークをRuby on Railsに導入し、デフォルトのテストフレームワークであるMinitestに代わるドロップインツールとして提供します。

RSpecでは、テストは単にアプリケーション コードを検証するスクリプトではありません。つまり、アプリケーションがどのように動作するかをわかりやすく説明したものです。

### 2-2. rspec-railsのバージョン
- `rspec-rails` 5.x for Rails 6.x.
- `rspec-rails` 4.x for Rails from 5.x or 6.x.
- `rspec-rails` 3.x for Rails earlier than 5.0.
- `rspec-rails` 1.x for Rails 2.x.

### 2-3. インストール
1. `Gemfile`の`:development`と`:test`に`rspec-rails`を加える
```
# Run against this stable release
group :development, :test do
  gem 'rspec-rails', '~> 5.0.0'
end
```
(これを :development group に追加することは厳密には必要ではありませんが、これがないと、ジェネレータや rake のタスクの前に RAILS_ENV=test を付けなければなりません)

2. ダウンロートとインストール
```
# Download and install
$ bundle install
```
```
# Generate boilerplate configuration files
# (check the comments in each generated file for more information)
$ rails generate rspec:install
      create  .rspec
      create  spec
      create  spec/spec_helper.rb
      create  spec/rails_helper.rb
``` 

### 2-4. 使用方法
- `rails generate`での作成

```
# RSpec hooks into built-in generators
$ rails generate model user
      invoke  active_record
      create    db/migrate/20181017040312_create_users.rb
      create    app/models/user.rb
      invoke    rspec
      create      spec/models/user_spec.rb

# RSpec also provides its own spec file generators
$ rails generate rspec:model user
      create  spec/models/user_spec.rb

# List all RSpec generators
$ rails generate --help | grep rspec
```

- specの実行
```
# Default: Run all spec files (i.e., those matching spec/**/*_spec.rb)
$ bundle exec rspec
# Run all spec files in a single directory (recursively)
$ bundle exec rspec spec/models
# Run a single spec file
$ bundle exec rspec spec/controllers/accounts_controller_spec.rb
# Run a single example from a spec file (by line number)
$ bundle exec rspec spec/controllers/accounts_controller_spec.rb:8
# See all options for running specs
$ bundle exec rspec --help
```
  - オプション：bundle exec rspecが冗長すぎる場合は、bin/rspecでbinstubを生成し、それを代わりに使用することができます。
  ```
  $ bundle binstubs rspec-core
  ```

- RSpecの記載例
```
RSpec.describe 'Post' do           #
  context 'before publication' do  # (almost) plain English
    it 'cannot have comments' do   #
      expect { Post.create.comments.create! }.to raise_error(ActiveRecord::RecordInvalid)  # test code
    end
  end
end
```

- 便利なRailsのマッチャ<br>
|RSpec matcher|Delegates to|Available in|Notes|
|---|---|---|---|
|be_a_new||all|primarily intended for controller specs|
|render_template|assert_template|request / controller / view|use with expect(response).to|
|redirect_to|assert_redirect|request / controller|use with expect(response).to|
|route_to|assert_recognizes|routing / controller|use with expect(...).to route_to|
|be_routable|	|routing / controller|use with expect(...).not_to be_routable|
|have_http_status| ||request / controller / feature|
|match_array| |all|for comparing arrays of ActiveRecord objects|
|have_been_enqueued| |all|requires config: ActiveJob::Base.queue_adapter = :test|
|have_enqueued_job|	|all|requires config: ActiveJob::Base.queue_adapter = :test|

- RSpec Railsの機能について
  - [Cucumberの公式ドキュメント](https://relishapp.com/rspec/rspec-rails/docs)

### 2-5. テストの種類
- 10種類のテスト
|Spec type|Corresponding Rails test class|
|---|---|
|model||	
|controller|ActionController::TestCase|
|mailer|ActionMailer::TestCase|
|job||
|view|ActionView::TestCase|
|routing||
|helper|ActionView::TestCase|
|request|ActionDispatch::IntegrationTest|
|feature|
|system|ActionDispatch::SystemTestCase|

注：これはチェックリストではありません。

100人の開発者にアプリケーションのテスト方法を聞いても、100通りの答えが返ってくるでしょう。

RSpec Railsでは、優れたテスト方法を推奨するために考え抜かれた機能を提供していますが、「正しい」方法はありません。最終的には、テストスイートをどのように構成するかは、あなた次第です。

- System specs, feature specs, request specsの違い
  - System specs(Rails 5.1以上)

    受入テスト、ブラウザテスト、エンドツーエンドテストとも呼ばれるシステムスペックは、人間のクライアントの視点からアプリケーションをテストするものです。テストコードは、ユーザーのブラウザでのやり取りを確認します。

  - Feature specs

    Railsにシステムテスト機能が導入される前は、エンドツーエンドのテストを行うための唯一の仕様タイプがfeature specでした。RSpecチームは現在、システム仕様を公式に推奨しています。（Systemテストより設定が煩雑）

  - Request specs

    リクエストスペックは、マシンクライアントの視点からアプリケーションをテストするためのものです。HTTPリクエストで始まり、HTTPレスポンスで終わるため、System specよりも高速ですが、アプリのUIやJavaScriptを検証することはできません。

    リクエストスペックは、コントローラスペックに代わるハイレベルな仕様です。実際、RSpec 3.5では、RailsチームとRSpecチームの両方が、リクエストスペックのような機能テストを優先して、コントローラを直接テストすることを推奨しています。

    リクエストスペックを書くときには、"あるHTTPリクエスト（動詞＋パス＋パラメータ）に対して、アプリケーションはどのようなHTTPレスポンスを返すべきか？"という質問に答えるようにしましょう。
  
  - System specの公式推奨について
    - [officially recommends system specs](https://rspec.info/blog/2017/10/rspec-3-7-has-been-released/#rails-actiondispatchsystemtest-integration-system-specs)

      Rails 5.1では、システムテストと呼ばれる新しい種類のテストが追加されました。これらのテストはcapybaraとRailsを包み込み、フロントエンドのjavascriptからデータベースに至るまでのフルスタックテストを可能にします。

      長い間、RSpecには似たような統合を提供するFeature specがありました。Feature specとSystem specの間には、いくつかの重要な違いがありますので、列挙します。

      javascript対応のドライバ（seleniumやpoltergeistなど）をfeature specで使用すると、テストはRailsアプリとは別のプロセスで実行されます。これは、テストとテスト対象のコードがデータベース トランザクションを共有できないことを意味します。そのため、RSpecに組み込まれたデータベースの変更をロールバックするメカニズムを使用できず、database cleanerのようなgemが必要になります。システムテストでは、Railsチームがこのようなことがないように努力してくれているので、追加のgemを必要とせずにRSpecのメカニズムを安全に使用することができます。

      RSpecのFeature specでは、デフォルトでRack::Testのcapybaraドライバを使用しています。javascript対応のテストブラウザを使用したい場合は、capybaraの設定を管理する必要があります。長い間、seleniumのような高度な統合では、この点を修正するのが難しいことが証明されてきました。システム仕様のデフォルトはseleniumを使用しています。この設定の難しさは、あなたに代わってcapybaraとwebdriverをchromeで操作するrailsによって隠されています。

      そのため、Rails 5.1のユーザーには、完全なアプリケーション統合テストのためにFeature specよりもSystem specを書くことを推奨しています。
