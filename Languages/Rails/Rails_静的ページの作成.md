# Rails 静的ページの作成
## 手順
1. ルーティングの設定
2. コントローラとアクションの作成
3. ERBテンプレートの作成
4. requests specを書く（TDDなら先に書くのが理想）
5. ヘッダー、フッターの分割（部分テンプレート）
6. ヘルパーメソッドの定義

## 1. ルーティングの設定
```
  root 'static_pages#index'
```

## 2. コントローラとアクションの作成
```
  $ bin/rails g controller <コントローラー名（複数形＋頭文字を大文字）>
  $ bin/rails g controller StaticPages
```
- `app/controllers/static_pages_controller.rb`
```
  class StaticPagesController < ApplicationController
    def index
      render action: 'index'
    end
  end
```

## 3. ERBテンプレートの作成
- `app/views/static_pages/index.html.erb`の作成
```
  <% @title = 'トップページ' %>
  <h1><%= @title %></h1>
```

## 4. requests specを書く
- `spec/requests/static_pages_spec.rb`
```
  it 'returns the success' do
    get root_path
    expect(response).to be_successful
  end
```

## 5. ヘッダー、フッターの分割（部分テンプレート）
- `/app/views/shared`フォルダを作成：一般的
- `/app/views/shared/_header.html.erb`を作成
```
  <header>
    <p>header</p>
  </header>
```
- `/app/views/shared/_footer.html.erb`を作成
```
  <footer>
    <p>fotter</p>
  </footer>
```
- `/app/views/layouts/application.html.erb`を編集
```
  <body>
    <%= render 'shared/header' %>
    <%= yield %>
    <%= render 'shared/footer' %>
  </body>
```

## 6. ヘルパーメソッドの定義  
ERBテンプレートの中で使用できるメソッドを「ヘルパーメソッド」と呼ぶ  
自分で定義したメソッドを使うことも可能
- `app/helpers/application_helper.rb`にて定義
```
  module ApplicationHelper
    def document_title
      if @title.present?
        "#{@title} - sample_app"
      else
        "sample_app"
      end
    end
  end
```
- `/app/views/layouts/application.html.erb`を編集
```
  <title><%= document_title %></title>
```
