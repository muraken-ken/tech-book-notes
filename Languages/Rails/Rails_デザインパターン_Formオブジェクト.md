# Fromオブジェクト
- [Railsのデザインパターン: Formオブジェクト](https://applis.io/posts/rails-design-pattern-form-objects)

## 1. Formオブジェクトとは  
- Formオブジェクトはモデル層に属するクラス群
- コントローラ層からユーザの入力を受けとり整形・検証し永続化する責務を持つ
- ビュー層に表示するためのデータを提供する

### 補足
- ActiveRecordモデルと1対1、そうでない場合でも可
- 複数のActiveRecordモデルの場合、対応するActiveRecordモデルがない場合でも可

---
## 2. Formオブジェクトの必要性 
- フォームの責務をカプセル化
- コントローラやビューを疎結合に保つ
  
### 2.1 MVCでの問題
- コントローラが肥大化：モデル層の知識をもちすぎるため
- ビューも肥大化：フォームを表示するための知識をもつ
- モデル層の変更がコントローラやビューに影響を及ぼす => 単一責任の原則 違反
- ActiveRecordモデルに持たせる：フォームの知識で肥大化

=> フォームという独立した責務をひとつのクラスにカプセル化
=> Formオブジェクトの役割
  
### 2.2 メリット
- コントローラはFormオブジェクトの#saveのようなひとつのインタフェースのみを使うため、クラス間を疎結合に保てる
- ビューもRailsのフレームワークに則った方法でフォームを表示できる
- フォームのふるまいに関するユニットテストが書きやすい

### 2.3 応用
- 複数のActiveRecordモデルを操作
- 複数の子レコードをつくる

---
## 3. Formオブジェクトのユースケース  
- サインアップ処理  
ユーザの作成と他ユーザのフォローを同時に行うなど、複数のActiveRecordモデルを作成
- 複数のタグを作成  
複数の子レコードを作成するとき
- ブログの検索フォーム  
Elasticsearchへのリクエストなど、ActiveRecordモデルを使用しない場合

---
## 4. Formオブジェクトの例
### 4.1 テーブル設計  
|Table|Column|Type|Not Null|
|---|---|---|---|
|posts|id|integer|◯|
||name|string|◯|
||content|text|-|
|tags|id|integer|◯|
||name|string|◯|
|taggings|post_id|integer|◯|
||tag_id|integer|◯|

### 4.2 コントローラ  
記事の作成・編集を行うコントローラ
- 一般的にはActiveRecordモデルのインスタンスをビューに渡す => Formオブジェクトのインスタンス
```
  class PostsController < ApplicationController
    def new
      @form = PostForm.new
    end

    def create
      @form = PostForm.new(post_params)

      if @form.save
        redirect_to posts_path, notice: 'The post has been created.'
      else
        render :new
      end
    end

    def edit
      load_post

      @form = PostForm.new(post: @post)
    end

    def update
      load_post

      @form = PostForm.new(post_params, post: @post)

      if @form.save
        redirect_to @post, notice: 'The post has been updated.'
      else
        render :edit
      end
    end

    private

    def post_params
      params.require(:post).permit(:title, :content, :tag_names)
    end

    def load_post
      @post = current_user.posts.find(params[:id])
    end
  end
```

### 4.3 Formオブジェクト  
Formオブジェクトは、ユーザの入力とpostオブジェクトを受け取り、作成・更新処理を行う
```
  class PostForm
    # 値の代入やバリデーション、コールバック当、モデルのふるまいをするために必要となるモジュール
    include ActiveModel::Model

    # Formオブジェクトで用いる値は書き込みメソッドを定義する必要がある
    # 読み取りメソッドも定義しているのは、ビューのフォームに必要なため
    attr_accessor :title, :content, :tag_names

    validates :title, presence: true
    validates :split_tag_names, presence: true

    # ビューの表示（#form_with）に必要なメソッド（作成・更新に応じてフォームのアクションをPOST・PATCHに切り替える）
    delegate :persisted?, to: :post

    # 更新にも対応する場合は#default_attributesのように保存済みのレコードをもとに値を設定する必要あり
    # 更新に対応しない場合は#initializeを定義する必要はない（ActiveModel::Modelの#initializeにより自動で値の初期化）
    def initialize(attributes = nil, post: Post.new)
      @post = post
      attributes ||= default_attributes
      super(attributes)
    end

    def save
      return if invalid?

      ActiveRecord::Base.transaction do
        tags = split_tag_names.map { |name| Tag.find_or_create_by!(name: name) }
        post.update!(title: title, content: content, tags: tags)
      end
    rescue ActiveRecord::RecordInvalid
      false
    end

    # ビューの表示（#form_with）に必要なメソッド（アクションのURLを適切な場所（ここではposts_pathやpost_path(id)）に切り替える）
    def to_model
      post
    end

    private

    attr_reader :post

    def default_attributes
      {
        title: post.title,
        content: post.content,
        tag_names: post.tags.pluck(:name).join(',')
      }
    end

    def split_tag_names
      tag_names.split(',')
    end
  end
```

### 4.4 ビュー  
- `posts/_form.html.erb`  
Formオブジェクトに#persisted?と#to_modelを定義したことで、作成・更新画面に応じてフォームの内容を切り替える
```
  <%= form_with model: form, local: true do |form| %>
    <%= form.text_field :title %>
    <%= form.text_area :content %>
    <%= form.text_field :tag_names %>
    <%= form.submit %>
  <% end %>
```

---
## 5. Formオブジェクトのルール
- ActiveModel::Modelをincludeする
- クラス名は接尾辞をFormにする（例：PostForm）
- #saveや#searchなど、クラス名から推測可能な単一の処理用メソッドを定義する。失敗時にfalseを返す
- バリデーションを実装する。ただしActiveRecordモデルとの整合性に気をつける
- Formオブジェクトがもつべき責務を明確にし、肥大化しないようにする
  - 例：レコード作成時にメールで通知するのはモデル層でなくコントローラ層で行う
