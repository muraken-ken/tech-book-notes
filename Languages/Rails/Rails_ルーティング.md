# Railsのルーティング
## 1 Railsルーターの目的
1. URLを実際のコードに割り振る
HTTPリクエスト
```
  GET /users/17
```  
マッチしたルーティング  
```
  get '/users/:id', to: 'users#show' => usersコントローラ => showアクション
```  
2. コードからパスやURLを生成する
ルーティング  
```
  get '/users/:id', to: 'users#show', as: 'users'
```  
アプリケーションコントローラー  
```
  @user = User.find(params[:id])
```  
view  
```
  <%= link_to 'User Record', user_path(@user) %> => /users/17というパスが生成
```

## 2. Railsルーターを設定する
### 2.1 設定ファイルの場所
```
  config/routes.rb
```

### 2.2 ルーティングの記述方法
1. resources（複数）
2. resource（単数）
3. 個別設定

### 2.2 設定ファイルは分割も可能
- `config/application.rb`
```
  config.paths["config/routes"] << "config/routes/routes_1.rb" 
  config.paths["config/routes"] << "config/routes/routes_2.rb" 
```

## 3. ルーティングの確認方法
1. `$ rails routes`  
Rails標準：遅い、見にくいというのが欠点  
spring gemがインストールされているなら`spring rails routes`とすれば2度目以降から高速になる

2. [http://localhost:3000/rails/info/routes](http://localhost:3000/rails/info/routes)を参照  
dev環境＋Rails起動中で使用可能   
Helper：path/url 切り替え可能という便利機能もある  

3. `$ rails console`を起動しておいて`> show-routes`を実行  
pryとpry-rails gemがインストール済み環境で使用可能  

4. Rails: ルーティングを動的にビジュアル表示する方法（参考）    
[Rails: ルーティングを動的にビジュアル表示する方法](https://techracho.bpsinc.jp/hachi8833/2020_11_20/100614)

## 4. ルーティングの書き方
### 4.1 resources（複数）  
- 記述内容
```
  resources :photos
```
- 作成されるルーティング  
アプリケーション内に以下の7つのルーティングが作成され、Photosコントローラに対応付けられる
|HTTP動詞|パス|コントローラ#アクション|目的|
|---|---|---|---|
|GET|/photos|photos#index|一覧を表示|
|GET|/photos/new|photos#new|1つ作成するためのHTMLフォームを返す|
|POST|/photos|photos#create|1つ作成する|
|GET|/photos/:id|photos#show|表示する|
|GET|/photos/:id/edit|photos#edit|編集用のHTMLフォームを1つ返す|
|PATCH/PUT|/photos/:id|photos#update|更新する|
|DELETE|/photos/:id|photos#destroy|削除する|

### 4.2 resource（単数）
- 記述内容
```
  resource :geocoder
```
- 作成されるルーティング  
アプリケーション内に以下の6つのルーティングが作成され、geocoderコントローラに対応付けられる
|HTTP動詞|パス|コントローラ#アクション|目的|
|---|---|---|---|
|GET|/geocoder/new|geocoders#new|作成するためのHTMLフォームを返す|
|POST|/geocoder|geocoders#create|作成する|
|GET|/geocoder|geocoders#show|表示する|
|GET|/geocoder/edit|geocoders#edit|編集用のHTMLフォームを1つ返す|
|PATCH/PUT|/geocoder|geocoders#update|更新する|
|DELETE|/geocoder|geocoders#destroy|削除する|

### 4.3 個別設定
- 記述内容
```
  get 'hello1', to: 'pages#hello'
  get 'hello', :controller => 'pages', :action => 'hello'
  get 'hello', controller: 'pages', action: :hello
```
- 作成されるルーティング  
記載した内容のGET・POST・PUT・UPDATE・DELETEのHTTPメソッドに対応するルーティング
- 注意  
GET・POST・PUT・UPDATE・DELETEのHTTPメソッドを複数指定したいとき  
`match`は、基本使わない（ワイルドカードはセキュリティ上の隙になる可能性がある）
```
  match :hello, to: 'pages#hello', via: [:get, :post] #許される
  match :hello, to: 'pages#hello' #禁止 (エラーが表示される)
```

### 4.4 root設定
- 記述内容
```
  root to: 'page#top'
  root 'page#top'
```

### 4.5 ルーティングのオプション
- `only:`と`except:`  
主要アクションから限定できる
```
  # indexとshowアクションだけ使う場合
  resources :prefectures, only: [:index, :show]
  # destory アクション以外を使う場合
  resources :prefectures, except: :destroy 
```
- リソース名の変更  
`as`オプションを使用して、リソース名を変更する
```
  get 'home', controller: :users, as: 'user_root'
```
- httpsの指定
```
  scope protocol: 'https://', constrains: {protocol: 'https'} do
    root to: 'page#top'
  end
```
- idを拡張  
idの制約を変更してアルファベットのidを使用する
```
  resources :prefectures, id: '/^[a-z]+$/'
```

## 5. ルーティングの書き方（応用）
### 5.1 ネストしたリソース（複数）
- モデル例
```
  class Magazine < ApplicationRecord
    has_many :ads
  end

  class Ad < ApplicationRecord
    belongs_to :magazine
  end
```
- ルーティング記述例
```
  resources :magazines do
    resources :ads
  end
```
- 作成されるルーティング  
`:magazine_id`がパスに付加される
|HTTP動詞|パス|コントローラ#アクション|
|---|---|---|
|GET|/magazines/:magazine_id/ads|ads#index|
|GET|/magazines/:magazine_id/ads/new|ads#new|
|POST|/magazines/:magazine_id/ads|ads#create|
|GET|/magazines/:magazine_id/ads/:id|ads#show|
|GET|/magazines/:magazine_id/ads/:id/edit|ads#edit|
|PATCH/PUT|/magazines/:magazine_id/ads/:id|ads#update|
|DELETE|/magazines/:magazine_id/ads/:id|ads#destroy|

### 5.2 ネストしたリソース（単数）
- ルーティング記述例
```
  resources :users do
    resource :password
  end
```
- 作成されるルーティング  
`:user_id`がパスに付加される
|HTTP動詞|パス|コントローラ#アクション|
|---|---|---|
|GET|/users/:user_id/password/new|password#new|
|POST|/users/:user_id/password|password#create|
|GET|/users/:user_id/password/:id|password#show|
|GET|/users/:user_id/password/:id/edit|password#edit|
|PATCH/PUT|/users/:user_id/password/:id|password#update|
|DELETE|/users/:user_id/password/:id|password#destroy|

### 5.3 ネストの注意点
1. ネストは1回にする  
ネスティングが深くなるとルーティングが扱いにくくなる
2. 「浅い」ネストの活用  
コレクション (index/new/createのような、idを持たないアクション) だけを親のスコープの下で生成するという手法
```
  resources :articles do
    resources :comments, only: [:index, :new, :create]
  end
  resources :comments, only: [:show, :edit, :update, :destroy]
```
|HTTP動詞|パス|コントローラ#アクション|名前付きルーティングヘルパー|
|---|---|---|---|
|GET|/articles/:article_id/comments(.:format)|comments#index|article_comments_path|
|POST|/articles/:article_id/comments(.:format)|comments#create|article_comments_path|
|GET|/articles/:article_id/comments/new(.:format)|comments#new|new_article_comment_path|
|GET|/sekret/comments/:id/edit(.:format)|comments#edit|edit_comment_path|
|GET|/sekret/comments/:id(.:format)|comments#show|comment_path|
|PATCH/PUT|/sekret/comments/:id(.:format)|comments#update|comment_path|
|DELETE|/sekret/comments/:id(.:format)|comments#destroy|comment_path|

### 5.4 namespace（名前空間）
- 記述例
```
  namespace :admin do
    resources :users
  end
```
- 作成されるルーティング  
「名前付きルート」「パス」「コントローラ#アクション」に`admin`が追加される

### 5.5 :moduleによる名前空間
- 記述例
```
  resources :users, module: :admin

  #以下も同等
  scope module: :admin do
    resources :users
  end
```
- 作成されるルーティング  
「コントローラ#アクション」にのみ`admin`が追加される  
パスには表したくないが別のディレクトリにまとめたいコントローラがある場合に利用

### 5.6 collectionとmember  
主要な7つのルーティング以外を追加したい場合  
  - collection(集合)はidなし
  - member(個別)はidあり
- 記述例
```
resources :books do
  collection do
    post :search
  end
  member do
    get :thumbnail
  end
end
```
- 作成されるルーティング
|HTTP動詞|パス|コントローラ#アクション|名前付きルーティングヘルパー|
|---|---|---|---|
|POST|/books/search(.:format)|books#search|search_books_path|
|GET|/books/id/thumnail(.:format)|books#thumnail|thumnail_book_path|

## 6. ルーティングを綺麗に書くコツ
1. 原則は「RESTに従う」：基本はresourceで書く
2. 以下の用語を理解する
  - HTTPメソッド：GET/POST/PATCH/PUT/DELETEで操作対象となるURL
  - 名前付きルート（パスヘルパー、URLヘルパー）
    - 「パス_path」(ドメイン名より下のパスのみ)
    - 「パス_url」(httpなどから始まるフルパス)
    - Contactページが/contactというパスにある場合 => `contact_path`, `contact_url`
  - ネストしたリソース：ネストしたルーティングを記述 => あるリソースを他のリソースの子にする
    - ネストは1回まで
3. アルファベット順にソートする：ルーティングはファイルの上から順に有効になる
4. 規模が大きくなったらルーティングファイルの分割を検討する

## 7. 名前付きルート（pathヘルパー、urlヘルパー）について
- ヘルパーの違い
  - pathヘルパー：相対パス（ドメイン名以下）
  - urlヘルパー：絶対パス（httpなどから始まるフルパス）
  ```
    root_path => '/'
    root_url  => 'http://www.example.com/'
  ```
- 基本的な使い分け
  - pathヘルパー：通常はこちらを使用する。（viewなど）
  - urlヘルパー：redirect_toする場合のみ使用する。（主にController）  
  HTTPの標準としては、リダイレクトのときに完全なURLが要求されるため

## 参考資料
- [Railsガイド：ルーティング](https://railsguides.jp/routing.html)
- [Railsのルーティングを極める(前編)](https://techracho.bpsinc.jp/baba/2020_11_18/15665)
- [Railsのルーティングを極める (後編)](https://techracho.bpsinc.jp/baba/2020_11_20/15619)