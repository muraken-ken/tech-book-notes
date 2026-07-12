# Rails namespace（名前空間）でviewを切り替える
## 手順
1. ルーティングの設定
2. コントローラーの作成
3. レイアウト／CSSの分離

## 1.ルーティングの設定
- config/routes.rb
```
  Rails.application.routes.draw do
    namespace :admin do
      root 'top#index'
    end

    namespace :user do
      root 'top#index'
    end
  end
```

## 2.コントローラーの作成
```
$ bin/rails g controller <namespace/コントローラー名>
$ bin/rails g controller admin/top
$ bin/rails g controller user/top
```

## 3. レイアウト／CSSの分離
### 3-1. レイアウトの分離
- `app/views/layouts/application.html.erb`をコピーして削除
- `app/views/layouts/admin.html.erb`を作成
- `app/views/layouts/user.html.erb`を作成
  - スタイルシート名、部分テンプレートの設定
```
  <!DOCTYPE html>
  <html>
    <head>
      <title><%= document_title %></title>
      <%= csrf_meta_tags %>
      <%= csp_meta_tag %>
      <%= stylesheet_link_tag '<スタイルシート名(3.3で指定)>', media: 'all', 'data-turbolinks-track': 'reload' %>
      <%= javascript_pack_tag 'application', 'data-turbolinks-track': 'reload' %>
    </head>

    <body>
      <%= render '<ヘッダー部分テンプレート>' %>
      <%= yield %>
      <%= render '<フッター部分テンプレート>' %>
    </body>
  </html>
```

### 3-2. cssの分離
- `app/assets/stylesheets/application.css`を削除
  - 内容
  ```
    /*
    *= require_tree .  # app/app/assets/stylesheetsディレクトリを指定
    *= require_self    # application.css記載のスタイルも適用
    */
  ```
- `app/assets/stylesheets/admin.css`を作成
- `app/assets/stylesheets/user.css`を作成
  - 変更内容(admin.cssの場合)
  ```
    /*
    *= require_tree ./admin  # app/app/assets/stylesheets/adminディレクトリを指定
    */
  ```

### 3-3. `config/initializers/assets.rb`の設定
- `config/initializers/assets.rb`
```
Rails.application.config.assets.version = '1.0'
Rails.application.config.assets.paths << Rails.root.join('node_modules')
Rails.application.config.assets.precompile += %w( admin.css user.css )  # この行を追加
```

### 3-4. `app/controllers/application_controller.rb`の設定
- `app/controllers/application_controller.rb`
  - 現在、使用しているコントローラーに基づいてレイアウトを切り替える
```
  class ApplicationController < ActionController::Base
    layout :set_layout

    private

      def set_layout
        if params[:controller].match(%r{\A(user|admin)/})
          Regexp.last_match[1]
        else
          'user'
        end
      end
  end
```
