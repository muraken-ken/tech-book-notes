# Rails ログイン機能の実装
## login機能の実装
### 手順
1. ルーティングの設定
2. Sessionsコントローラの作成
3. Fromオブジェクトの作成
4. log_inメソッド、current_userメソッドの定義
5. Sessionsコントローラの記述
6. Viewの作成

### 1. ルーティングの設定
- `config/routes.rb`
```
  resource :session, only: %i[new create destroy]
```

### 2. Sessionsコントローラの作成
```
  $ bin/rails g controller <モデル名（複数形＋頭文字を大文字）>
  $ bin/rails g controller Sessions
```

### 3. Fromオブジェクトの作成
- [Rails_デザインパターン_Formオブジェクト](Rails_デザインパターン_Formオブジェクト.md)
- `app/form/login_form.rb`
```
  class LoginForm
    include ActiveModel::Model

    attr_accessor :email, :password
  end
```

### 4. log_inメソッド、current_userメソッドの定義
- `app/helpers/sessions_helper.rb`
```
  module SessionsHelper
    def log_in(user)
      session[:user_id] = user.id
    end

    def current_user
      if session[:user_id]
        @current_user ||= User.find_by(id: session[:user_id])
      end
    end
  end
```
- `app/controllers/application_controller.rb`
```
  class ApplicationController < ActionController::Base
    include SessionsHelper
  end
```

### 5. Sessionsコントローラの記述
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
          log_in user
          redirect_to :root
        else
          render :new
        end
      else
        render :new
      end
    end

    def destroy
      session.delete(:user_id)
      redirect_to :root
    end

    private

      def login_form_params
        params.require(:login_form).permit(:email, :password)
      end
  end
```

### 6. Viewの作成
- `app/view/sessions/new.html.erb`
```
  <% @title = 'Log in' %>
  <h1><%= @title %></h1>

  <%= form_with model: @form, url: :session do |f| %>
    <%= f.label :email %>
    <%= f.email_field :email %>
    <br>
    <%= f.label :password %>
    <%= f.password_field :password %>
    <br>
    <%= f.submit 'ログイン' %>
  <% end %>
```
- `app/views/shared/_header.html.erb`
```
  <%=
    if current_user
      link_to 'ログアウト', :session, method: :delete      
    else
      link_to 'ログイン', :new_session
    end 
   %>
```
