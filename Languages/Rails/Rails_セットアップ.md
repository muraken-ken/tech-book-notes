# Railsアプリセットアップ
## 1. 事前準備
- githubにリポジトリを作成（アプリ名と同じ）
## 2. アプリセットアップ
- アプリのフォルダを作成したいフォルダに移動
### 2.1 Rails newする
```
  rails new <app名> --skip-test-unit(RSpec使用時)
```

### 2.2 githubの初期設定
1. アプリフォルダに移動
2. github設定（githubの記載通り）
```
  $ git init
  $ git add -A
  $ git commit -m "Initialize"（コメントは任意）
  $ git branch -M main（mainにリネーム）
  $ git remote add origin <リポジトリ>
  $ git push -u origin main
  $ git co -b <branch名>（開発用ブランチに切り替え）
```

### 2.3 gemfileの編集
1. 不要なコメントを消す
2. gemの追加・変更（とりあえず以下があればOK）
```
  gem 'bcrypt', '~> 3.1', '>= 3.1.16'
  gem 'rails-i18n', '~> 6.0'
  gem 'rexml', '~> 3.1', '>= 3.1.9'   # これを入れないとRSpecでエラーが出る

  group :development, :test do
    gem "debug", ">= 1.0.0"
  end

  group :test do
    gem 'factory_bot_rails', '~> 6.2'
    gem 'rspec-rails', '~> 5.0', '>= 5.0.2'
  end

  # 以下は消す => システムのタイムゾーンを参照
  # Windows does not include zoneinfo files, so bundle the tzinfo-data gem
  gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```
3. bin/bundle

### 2.4 JavaScriptパッケージ群のインストール
```
  $ yarn => rails new時に実行されているのでここでは不要
```

### 2.5 Railsサーバー起動
- `rails s` => Railsが起動した！

### 2.6 タイムゾーンとロケールの設定
### 2.7 ジェネレータ設定
- `config/application.rb`
```
  module LearningApp
    class Application < Rails::Application
      config.load_defaults 6.1

      config.time_zone = "Tokyo"
      config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '*.{rb,yml}').to_s]
      config.i18n.default_locale = :ja

      config.generators do |g|
        g.skip_routes true        # routes.rbを変更しない
        g.helper false            # helperを作成しない
        g.assets false            # CSS、JavaScriptを作成しない
        g.test_framework :rspec   # RSpecでのテスト
        g.controller_spec false   # コントローラテストは作成しない
        g.view_spec false         # viewのテストは作成しない
      end
    end
  end
```

### 2.8 RSpecの設定
- インストール
```
  $ bin/rails g rspec:install
```
- `.rspec`の編集
```
--format documentation  # 結果をドキュメント形式で表示
```
