# BCryptの使い方
## 1. BCryptとは  
- パスワードをハッシュ化する必要性
  - 平文でパスワードをデータベースに格納しておく => セキュリティ的に危険
  - 格納された値から、元のパスワードを推測されないもの（不可逆的）
- BCrypt(ビークリプト)とは
  - OpenBSDプロジェクトのNiels Provos氏とDavid Mazières氏によって設計されたハッシュアルゴリズム
  - データの塊（例：ユーザーのパスワード）を受け取り、そのデータの「デジタル指紋」または「ハッシュ」を作成
  - このプロセスは可逆的ではないため、ハッシュからパスワードに戻ることはできない
  - ハッシュを保存し、潜在的に有効なパスワードで作られたハッシュと照合することができる
  - saltと呼ばれる短いランダムデータの塊を加えて暗号化する
  - レインボーテーブル(ハッシュ値に対して総当たりで平文を得ようとする攻撃)に対して強い
  - bcrypt-rubyが使用するデフォルトのコストファクターは12、セッションベースの認証には適している

## 2. Railsでの使い方
### 2.1 導入
- `gemfile`に記述し、`bin/bundle`でinstallする
```
  gem 'bcrypt'
```
- DBのpassword用のカラムはstring属性

### 2.2 使い方（従来）
- DBのpassword用のカラムは、`hashed_password`（任意） string属性
- ハッシュ化した値の作成
``` 
 def password=(raw_password)
    if raw_password.kind_of?(String)
      self.hashed_password = BCrypt::Password.create(raw_password)
    elsif raw_password.nil?
      self.hashed_password = nil
    end
  end
```
  - nilもハッシュ化してしまうため、nilは除外
- パスワード認証
```
  def authenticate?(raw_password)
    @hoge && 
      @hoge.hashed_password &&
      BCrypt::Password.new(@hoge.hashed_password) == raw_password
  end
```

### 2.3 使い方（`has_secure_password`：Railsの機能）
- DBのpassword用のカラムは、`password_digest`（固定） string属性
- model内に`has_secure_password`を記載する
  - password,password_confirmation属性が追加（validation）
  - authenticateメソッドが使える
  - 以下のvalidationが自動的に反映される
    - 作成時にパスワードが必要(presense: true)
    - パスワードの長さは72バイト以下であること（bcryptの仕様上、長すぎると無視されるため）
    - パスワードの確認(XXX_confirmation属性を使用)
      - confirminationがnil（空）の場合
        - validationは行われない
        - confirminationのフォームを無くせば、confirminationを無視できる

- ハッシュ化した値の作成（フォームを送信すれば自動的に格納される）
```
  <%= form_with(@hoge) do |f| %>

    <%= f.label :password %>
    <%= f.password_field :password %>

    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation %>

  <% end %>
```
- authenticateメソッド（パスワード認証）
```
  def authenticate?(raw_password)
    @hoge && 
      @hoge.password_digest &&
      @hoge.authenticate("raw_password")  # true
  end
```

## 参考資料
- [bcrypt-ruby](https://github.com/bcrypt-ruby/bcrypt-ruby)
- [has_secure_password](https://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html)
