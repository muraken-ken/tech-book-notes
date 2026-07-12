# Rails 自動ログイン機能の実装
- [Rails_ログイン機能の実装](Rails_ログイン機能の実装.md)

## 自動ログイン機能の実装
### 手順
1. Fromオブジェクトの修正
2. Viewの追加
3. Sessionsコントローラの修正
4. current_userメソッドの修正

### 1. Fromオブジェクトの修正
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

### 2. Viewの追加
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

### 3. Sessionsコントローラの修正
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
          if @form.remember_me?
            cookies.permanent.signed[:user_id] = user.id
          else
            cookies.delete(:user_id)
            log_in user
          end
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
      cookies.delete(:user_id)
      session.delete(:user_id)
      redirect_to :root
      flash.notice = 'ログアウトしました。'
    end

    private

      def login_form_params
        params.require(:login_form).permit(:email, :password, :remember_me)
      end
  end
```

### 4. cuurent_userメソッドの修正
- `app/helpers/sessions_helper.rb`
```
  module SessionsHelper
    def log_in(user)
      session[:user_id] = user.id
    end

    def current_user
      if user_id = cookies.signed[:user_id] || session[:user_id]
        @current_user ||= User.find_by(id: user_id)
      end
    end
  end
```
