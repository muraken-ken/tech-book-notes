# RSpecの基本セットアップ
## 1. Gemfile
- `Gemfile`
```
  group :development, :test do
    gem 'rspec-rails', '~> 5.0', '>= 5.0.2'
    gem 'factory_bot_rails', '~> 6.2'
  end

  group :test do
    gem 'capybara', '>= 3.26'
    gem 'launchy', '~> 2.5' # エラー時の画像を残す
    gem 'selenium-webdriver'
    gem 'webdrivers'  # chrome driverを自動でアップデート
  end
```
- `bundle install`を実行

## 2. RSpecの設定
- `config/application.rb`
```
  config.generators do |g|
    # 色々な記述があるので、一番下に追記する
    g.test_framework :rspec,  # RSpecでのテスト
                      fixtures: true,            # Varidationを通らない => Factory_bot
                      view_specs: false,         # 保守が大変=>統合テスト
                      helper_specs: false,       # 必要に応じ、手動で追加で良い
                      routing_specs: false,
                      controller_specs: false,   #非推奨化
                      request_specs: true,
                      system_specs: true
                      # 必要に応じ、true、falseを変える
  end
```
- `.rspec`
```
  --require spec_helper
  --format documentation  #RSpec の出力をドキュメント形式に変更
  --warnings              #Rubyの警告あり（必要に応じて）
```
- `rails_helper.rb`
```
  require 'spec_helper'

  ENV['RAILS_ENV'] ||= 'test'

  require File.expand_path('../config/environment', __dir__)

  abort("The Rails environment is running in production mode!") if Rails.env.production?

  require 'rspec/rails'

  require 'capybara/rspec'

  begin
    ActiveRecord::Migration.maintain_test_schema!
  rescue ActiveRecord::PendingMigrationError => e
    puts e.to_s.strip
    exit 1
  end
  Dir[Rails.root.join('spec', 'support', '**', '*.rb')].sort.each { |f| require f }
  RSpec.configure do |config|
    config.fixture_path = "#{::Rails.root}/spec/fixtures"

    config.use_transactional_fixtures = true

    config.infer_spec_type_from_file_location!

    config.filter_rails_from_backtrace!

    # FactoryBot設定
    config.include FactoryBot::Syntax::Methods

    # js以外はrack_test（デフォルト）が高速
    config.before(:each, type: :system) do
      driven_by :rack_test
    end
    
    # chromeをheadlessモードで動かす
    config.before(:each, type: :system, js: true) do
      driven_by :selenium_chrome_headless
    end
    
    # RequestHelper読み込み
    config.include RequestHelpers, type: :request
    # config.include RequestHelpers, type: :helper
    # config.include SystemHelpers, type: :system
  end
```
- `sepc_helper.rb`
```
  RSpec.configure do |config|
    config.expect_with :rspec do |expectations|
      expectations.include_chain_clauses_in_custom_matcher_descriptions = true
    end

    config.mock_with :rspec do |mocks|
      mocks.verify_partial_doubles = true
    end

    config.shared_context_metadata_behavior = :apply_to_host_groups
  end
```
