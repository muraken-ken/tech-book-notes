# Rails 自動ログイン機能の実装(2)
- [Rails_ログイン機能の実装](Rails_ログイン機能の実装.md)
- [Rails_自動ログイン機能の実装(1)](Rails_自動ログイン機能の実装(1).md)

## 安全性について
- Rails_自動ログイン機能の実装(1)では、ユーザーIDをcookiesメソッドで永続化
  - sessionメソッドで作成した一時cookiesは自動的に暗号化
  - cookiesメソッドで永続化した場合、セッションハイジャックという攻撃を受ける可能性あり
- 対策
  - 記憶トークン → 暗号化 → 記憶ダイジェスト → トークン認証
- 基本的には、こちらの方法を基本とする

## 自動ログイン機能の実装
### 手順
1. 記憶ダイジェストをUserモデルに追加
2. Fromオブジェクトの修正
3. Viewの追加
4. session_helperの修正
5. Sessionsコントローラの修正

### 1. 記憶ダイジェストをUserモデルに追加
```
  $ bin/rails g migration add_remember_digest_to_users
```
- `db/migrate/[timestamp]_add_remember_digest_to_users.rb`
```
  class AddRememberDigestToUsers < ActiveRecord::Migration[6.1]
    def change
      add_column :users, :remember_digest, :string
    end
  end
```
```
  $ bin/rails db:migrate
```
- `app/models/user.rb`
```
  class User < ApplicationRecord
    attr_accessor :remember_token
    has_secure_password

    # 渡された文字列のハッシュ値を返す
    def self.digest(string)
      cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST : BCrypt::Engine.cost
      BCrypt::Password.create(string, cost: cost)
    end

    # Randomなtokenを生成
    def self.new_token
      SecureRandom.urlsafe_base64
    end

    # remember_digestに記憶する（sessionの永続化）
    def remember
      self.remember_token = User.new_token
      update_attribute(:remember_digest, User.digest(remember_token))
    end

    # 渡されたtokenがdigestと一致すればtrueを返す
    def authenticated?(remember_token)
      return false if remember_digest.nil?

      BCrypt::Password.new(remember_digest).is_password?(remember_token)
    end

    # ユーザーのログイン情報を破棄する
    def forget
      update_attribute(:remember_digest, nil)
    end
  end
```

### 2. Fromオブジェクトの修正
- `app/form/login_form.rb`
```
  class LoginForm
    include ActiveModel::Model

    attr_accessor :email, :password, :remember_me

    def remember_me?
      remember_me == '1'
    end
  end
```

### 3. Viewの追加
- `app/view/sessions/new.html.erb`
```
<%= form_with model: @form, url: :session do |f| %>
  <div>
    <%= f.label :email %>
    <%= f.email_field :email %>
  </div>
  <div>
    <%= f.label :password %>
    <%= f.password_field :password %>
  </div>
  <div>
    <%= f.check_box :remember_me %>
    <%= f.label :remember_me, '次回から自動でログインする' %>
  </div>
  <div>
    <%= f.submit 'ログイン' %>
  </div>
<% end %>
```

### 4. session_helperの修正
- `app/helpers/sessions_helper.rb`
```
  module SessionsHelper

    # 渡されたユーザーでログインする
    def log_in(user)
      session[:user_id] = user.id
    end

    # 記憶トークンcookieに対応するユーザーを返す
    def current_user
      if (user_id = session[:user_id])
        @current_user ||= User.find_by(id: user_id)
      elsif (user_id = cookies.signed[:user_id])
        user = User.find_by(id: user_id)
        if user&.authenticated?(cookies[:remember_token])
          log_in user
          @current_user = user
        end
      end
    end

    # ユーザーがログインしていればtrue、その他ならfalseを返す
    def logged_in?
      !current_user.nil?
    end

    # ユーザーのセッションを永続的にする
    def remember(user)
      user.remember
      cookies.permanent.signed[:user_id] = user.id
      cookies.permanent[:remember_token] = user.remember_token
    end

    # 永続的セッションを破棄する
    def forget(user)
      user.forget
      cookies.delete(:user_id)
      cookies.delete(:remember_token)
    end

    # 現在のユーザーをログアウトする
    def log_out
      forget(current_user)
      session.delete(:user_id)
      @current_user = nil
    end
  end
```

### 5. Sessionsコントローラの修正
```
  class SessionsController < ApplicationController
    def new
      @form = LoginForm.new
    end

    def create
      @form = LoginForm.new(login_form_params)
      if @form.email.present?
        user = User.find_by('LOWER(email) = ?', @form.email.downcase)
        if user&.authenticate(@form.password)
          @form.remember_me? ? remember(user) : forget(user)
          log_in(user)
          flash.notice = 'ログインしました。'
          redirect_to :root
        else
          flash.now.alert = 'メールアドレスまたはパスワードが正しくありません。'
          render :new
        end
      else
        render :new
      end
    end

    def destroy
      log_out if logged_in?
      redirect_to :root
      flash.notice = 'ログアウトしました。'
    end

    private

      def login_form_params
        params.require(:login_form).permit(:email, :password, :remember_me)
      end
  end
```

