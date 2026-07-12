# Rails Viewヘルパー 
## form_with
### 1. form_withについて
- URL、スコープ、モデルに基づいてフォームタグを作成する
```
  form_with(model: nil, scope: nil, url: nil, format: nil, **options, &block)
```
- URLを使用
  - erb
  ```
    <%= form_with url: posts_path do |form| %>
      <%= form.text_field :title %>
    <% end %>
  ```
  - html
  ```
    <form action="/posts" method="post">
      <input type="text" name="title">
    </form>
  ```
- 入力フィールド名の前にスコープを追加する
  - erb
  ```
    <%= form_with scope: :post, url: posts_path do |form| %>
      <%= form.text_field :title %>
    <% end %>
  ```
  - html
  ```
    <form action="/posts" method="post">
      <input type="text" name="post[title]">
    </form>
  ```
- モデルを使用すると、URLとスコープの両方が推定される
  - erb
  ```
    <%= form_with model: @post do |form| %>
      <%= form.text_field :title %>
    <% end %>
  ```
  - 以下と同じ
  ```
    <%= form_with scope: :post, url: post_path(@post), method: :patch do |form| %>
      <%= form.text_field :title %>
    <% end %>
  ```
  - html
  ```
    <form action="/posts/1" method="post">
      <input type="hidden" name="_method" value="patch">
      <input type="text" name="post[title]" value="<the title of the post>">
    </form>
  ```

### 2. form_withのオプションについて
- `:url`：フォームの送信先のURL
- `:method`：フォームを送信する際に使用するメソッド。通常は "get "、"post "。"patch"、"put"、"delete"、は、_methodという名前の隠し入力が追加される
- `:scope`：入力フィールド名の前に付けるスコープ
- `:namespace`：フォームの名前空間
- `:model`：モデルオブジェクトは、:urlと:scopeを推論し、入力フィールドの値を記入
- `:authenticity_token`：フォームで使用する認証トークン。JavaScript を使用しないブラウザをサポートしていない限り、埋め込みは不要
- `:local`：デフォルトでは、フォームの送信は通常のHTTPリクエストで行われる。`local: false`を指定することで、リモート、XHR の送信を有効。
  - `config.action_view.form_with_generates_remote_forms = true`でデフォルトでONできる
- `:skip_enforcing_utf8`：`true` 名前がutf8の隠し入力は出力されない
- `:builder`：フォームの構築に使用されるオブジェクトをオーバーライドする
- `:id`：HTMLのid属性
- `:class`：HTMLのclass属性
- `:data`：HTMLのdata属性
- `:html`：その他のHTML属性

### 3. 使用例
- ブロックを渡さない  
単に冒頭のformタグを生成
- namespace `admin_post_url`
```
  <%= form_with(model: [ :admin, @post ]) do |form| %>
    ...
  <% end %>
```

### 4. 他のform helperとの混在  
FormBuilderオブジェクトを使用するが、FormHelper、FormTagHelperの使用も可能
```
  <%= form_with scope: :person do |form| %>
    <%= form.text_field :first_name %>
    <%= form.text_field :last_name %>

    <%= text_area :person, :biography %>
    <%= check_box_tag "person[admin]", "1", @person.company.admin? %>

    <%= form.submit %>
  <% end %>
```

### 5. method設定
```
  method: (:get|:post|:patch|:put|:delete)
```

## FormBuilderオブジェクト
- [入力欄 (フォーム入力) 要素](https://developer.mozilla.org/ja/docs/Web/HTML/Element/input)

### 1. ラベル
- label：ユーザーインターフェイスの項目のキャプションを表す
```
  label(method, text = nil, options = {}, &block)
  <label></label>
```

### 2. 入力欄
- hidden_field：表示されないコントロール、その値はサーバーに送信される
```
  hidden_field(method, options = {})
  <input type="hidden" />
```
- text_field：単一行のテキスト入力欄
```
  text_field(method, options = {})
  <input type="text" />
```
- text_area：複数行のプレーンテキスト編集コントロール
```
  text_area(method, options = {})
  <textarea></textarea>
```
- email_field：電子メールアドレスを編集するための欄
```
  email_field(method, options = {})
  <input type="email" />
```
- password_field：入力値を隠す単一行テキストフィールド
```
  password_field(method, options = {})
  <input type="password" />
```
- number_field：数値を入力するためのコントロール
```
  number_field(method, options = {})
  <input type="number" />
```
- telephone_field：電話番号を入力するためのコントロール
```
  telephone_field(method, options = {})
  <input type="tel" />
```
- search_field：検索文字列を入力するための単一行のテキスト欄
```
  search_field(method, options = {})
  <input type="search" />
```
- url_field：URL を入力するための入力欄
```
  hidden_field(method, options = {})
  <input type="hidden" />
```

### 3. ボタン類
- submit：フォームを送信するボタン
```
  submit(value = nil, options = {})
  <input type="submit" />
```
- button：基本的にsubmitと同じ（送信ボタンを作成する）
```
  button(value = nil, options = {}, &block)
  <input type="submit" />
```
- check_box：チェックボックスで、選択または未選択のうちひとつの値をとる
```
  check_box(method, options = {}, checked_value = "1", unchecked_value = "0")
  <input type="checkbox" />
```
- radio_button：ラジオボタンで、同じ name の値を持つ複数の選択肢から一つの値を選択することができる
```
  radio_button(method, tag_value, options = {})
  <input type="radio" />
```
- file_field：ユーザーがファイルを選択するコントロール
```
  file_field(method, options = {})
  <input type="file" />
```
- color_field：色を指定するためのコントロール
```
  color_field(method, options = {})
  <input type="color" />
```
- range_field：厳密な値ではない数値を入力するためのコントロール。範囲のウィジェットを表示。
```
  range_field(method, options = {})
  <input type="range" />
```
- select：選択肢を表示
```
  select(method, choices = nil, options = {}, html_options = {}, &block)
  <select name="" id="">
    <option value="" label=" "></option>
    <option value="1" selected="selected">foo</option>
    <option value="2">bar</option>
    <option value="3">baz</option>
  </select>
```
- collection_check_boxes
```
  collection_check_boxes(method, collection, value_method, text_method, options = {}, html_options = {}, &block)
  <input type="checkbox" />
  <label></label>
  <input type="checkbox" />
  <label></label>
```
- collection_radio_buttons：radioボタンをまとめて表示
```
  collection_radio_buttons(method, collection, value_method, text_method, options = {}, html_options = {}, &block)
  <input type="radio" />
  <label></label>
  <input type="radio" />
  <label></label>
```
- collection_select：選択肢を表示
```
  collection_select(method, collection, value_method, text_method, options = {}, html_options = {})
  <select name="" id="">
    <option value="" label=" "></option>
    <option value="1" selected="selected">foo</option>
    <option value="2">bar</option>
    <option value="3">baz</option>
  </select>
```

### 4. 日時
- datetime_field：タイムゾーン情報がない日付と時刻を入力するためのコントロール
```
  datetime_field(method, options = {})
  <input type="datetime-local" />
```
- datetime_local_field：datetime_fieldと同じ
```
  datetime_local_field(method, options = {})
  <input type="datetime-local" />
```
- date_field：日付 (時刻を除く年、月、日) を入力するためのコントロール
```
  date_field(method, options = {})
  <input type="date" />
```
- time_field：時刻を入力するためのコントロール
```
  time_field(method, options = {})
  <input type="time" />
```
- month_field：タイムゾーン情報がない年と月を入力するためのコントロール
```
  month_field(method, options = {})
  <input type="month" />
```
- week_field：年と週番号で構成される日付を入力するためのコントロール
```
  week_field(method, options = {})
  <input type="week" />
```
- datetime_select：日時にアクセスするたのセレクト・タグを返す
```
  datetime_select(method, options = {}, html_options = {})
  type="datetime-local"
```
- date_select：日付にアクセスするたのセレクト・タグを返す
```
  date_select(method, options = {}, html_options = {})
  type="date"
```
- time_select：時刻にアクセスするたのセレクト・タグを返す
```
  time_select(method, options = {}, html_options = {})
  type="time"
```
