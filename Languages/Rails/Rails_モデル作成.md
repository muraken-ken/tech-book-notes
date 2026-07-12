# Rails モデルの作成
## 手順
  1. モデルの作成
  2. マイグレーション
  3. パスワードのハッシュ化（必要な場合）
  4. 一覧、登録、編集、削除機能の実装  
    1. ルーティング設定  
    2. コントローラーの作成  
    3. コントローラーの記述（最低限）  
    4. viewファイル記述例

## 1. モデルの作成
```
  $ bin/rails g model <モデル名（単数形＋頭文字を大文字）>
  $ bin/rails d model <モデル名（単数形＋頭文字を大文字）>  // 削除する場合
  $ bin/rails g model User
```

## 2. マイグレーション
- [Rails_マイグレーション](Rails_マイグレーション.md)
```
  class CreateUsers < ActiveRecord::Migration[6.1]
    def change
      create_table :users do |t|
        t.string :email, null: false  # メールアドレス
        t.string :password_digest     # パスワード

        t.timestamps
      end
      add_index :users, 'LOWER(email)', unique: true
    end
  end
```
```
 $ bin/rails db:migrate
 $ bin/rails db:rollback    // 元に戻す場合
```

## 3. パスワードのハッシュ化
- `bcrypt`が`gemfile`に記載済み、`bin/bundle`済み
```
  class User < ApplicationRecord
    has_secure_password
  end
```
- [BCryptの使い方](BCryptの使い方.md)

## 4. 一覧、登録、編集、削除機能の実装
### 4-1. ルーティング設定
- `config/routes.rb`
```
  resources :users
```

### 4-2. コントローラーの作成
```
  $ bin/rails g controller <モデル名（複数形＋頭文字を大文字）>
  $ bin/rails g controller Users
```

### 4-3. コントローラーの記述（最低限）
- `app/controller/users_controller.rb`
```
  class UsersController < ApplicationController
    def index
      @users = User.all
    end

    def new
      @user = User.new
    end

    def create
      @user = User.new(user_params)
      if @user.save
        redirect_to @user
      else
        render action: 'new'
      end
    end

    def show
      @user = User.find(params[:id])
    end

    def edit
      @user = User.find(params[:id])
    end

    def update
      @user = User.find(params[:id])
      if @user.update(user_params)
        redirect_to @user
      else
        render action: 'edit'
      end
    end

    def destroy
      User.find(params[:id]).destroy
      redirect_to root_url
    end

    private

      def user_params
        params.require(:user).permit(:email, :password, :password_confirmation)
      end
  end
```

### 4-4. viewファイル記述例
- [Rails_form_withヘルパーメソッド](Rails_form_withヘルパーメソッド.md)
- `app/view/users/index.html.erb`
```
  <ul>
    <% @users.each do |user| %>
      <li>
        <%= link_to user.email, user %>
      </li>
    <% end %>
  </ul>
```
- `app/view/users/new.html.erb`, `app/view/users/edit.html.erb`
```
  <%= form_with model: @user do |f| %>
    <%= f.label :email %>
    <%= f.email_field :email %>
    <br>
    <%= f.label :password %>
    <%= f.password_field :password %>
    <br>
    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation %>
    <br>
    <%= f.submit %>
  <% end %>
```
- `app/view/users/show.html.erb`
```
  <%= @user.email %>
  <%= link_to 'edit', edit_user_path %>
  <%= link_to 'delete', @user, method: :delete, data: { confirm: 'You sure?' } %>
```
