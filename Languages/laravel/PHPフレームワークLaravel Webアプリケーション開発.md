# PHPフレームワークLaravel Webアプリケーション開発 バージョン8.x対応
## Chapter1 Laravelの概要
### Laravelのディレクトリ構成
- app/  
Console、EXceptions、Http、Providersの各ディレクトリがある。コントローラー、ミドルウェア、例外クラス、コンソール、サービスプロバイダなど、アプリケーションの主要な処理クラスが置かれる。
- bootstrap/  
アプリケーションで最初に実行される処理やオートローディング設定が入る。
- config/  
アプリケーションの設定値
- database/  
データベース関連のファイルが入る。マイグレーションファイル、初期投入データなど
- public/  
Webアプリケーションの場合、このフォルダがドキュメントルート。エントリポイントとなるindex.php、JavaScript、CSSなどそのまま公開できるファイルを配置するディレクトリ。
- resouces/  
Viewのテンプレートファイル、LESS、SASSなどメタ言語ファイル、言語ファイルを配置。
- routes/  
アプリケーションのルート定義ファイルを配置
- storage/  
プログラム実行時にLaravelが作成するファイルの出力先。ログファイル、ファイルキャッシュ、コンパイルされたテンプレートファイルなどを保存。
- tests/  
テストコードを記載したファイルを配置
- vendor/  
Composerによりダウンロードされる各種パッケージのディレクトリ。Laravel、Symfonyもココ。

### Laravel テストフレームワーク
- PHPUnit：PHPテストフレームワーク
- Laravel Dusk：ブラウザを使った自動テスト

--- 
## Chapter2 Laravelのアーキテクチャ
### 2-1 ライフサイクル
#### 2-1-1 Laravelアプリケーション実行の流れ
- 1.エントリポイント  ：public/index.php
- 2.HTTPカーネル      ：app/Http/Kernel.php
- 3.ルータ            ：Illuminate\Routing\Router routers/web.php
- 4.ミドルウェア
- 5.コントローラ、クロージャ

#### 2-1-2 エントリポイント
- Laravelアプリケーションの起点
- public/index.php
- Webサーバ（Apache、nginx）などで設定

#### 2-1-3 HTTPカーネル
- アプリケーションのセットアップ
- ミドルウェアの設定
- ルータの実行
- App\Http\Kernel                   ：ミドルウェア設定のみ
- Illuminate\Foundation\Http\Kernel ：基底クラス、Handleメソッドの記載
  - ルータ実行、例外処理
  - イベント発火

#### 2-1-4 ルータ
- Requestにマッチするルートを探す
- 定義された以下を実行
  - コントローラ（メソッド）
  - アクションクラス
  - クロージャ（viewを表示など単純処理、あまり使われない）
- 実行結果
  - Illuminate\Http\Response
  - Illuminate\Http\JsonResponse
- routes/web.php

#### 2-1-5 ミドルウェア
- ルートで指定された処理を実行する前後に任意の処理を実行可能
  - Request、Responseに含まれる値の変更、暗号化（複合）、セッション実行、認証処理
  - 複数のミドルウェアを数珠つなぎに連結可能
- 容易に有効・無効が切り替えられる

#### 2-1-6 コントローラ
- HTTPリクエストに対応する処理を実行
- 狭義の意味では、アプリケーション実行の起点
  - ビジネスロジックの実行
  - データベースアクセス等
- 処理が完了すればResponseを返す

---
### 2-2 サービスコンテナ
#### 2-2-1 サービスコンテナとは
- フレームワークの導入の利点の１つ
  - 必要な共通機能を簡単に利用できる
  - 開発者がビジネスロジックに集中できる
- サービスコンテナ
  - Laravelフレームワーク内のインスタンス管理の役割を担う
  - ビジネスロジック：インスタンスを要求
  - 各クラスのインスタンス、インスタンスの生成方法保持、インスタンスを返却
  - アプリケーションの初期化処理でサービスコンテナへの登録処理を行う

#### 2-2-2 バインドと解決
- バインド（bind/binding）
  - サービスコンテナに対してインスタンスの生成方法を登録する処理
- 解決（resolve/resolving）
  - 指定されたインスタンスをサービスコンテナが生成して返すこと
- Illuminate/Foundation/Applicationクラス
  - Illuminate/Container/Containerクラスを継承
  - サービスコンテナの機能の多くが実装されている

#### 2-2-3 バインド
- バインドの主要な方法
  - bindメソッド：もっとも利用されるバインドメソッド
  ```
    app()->bind(Number::class, function () {
      return new Number();
    });
    $numcls = app()->make(Number::class);
    $number = $numcls->getNumber();
  ```
  - bindIfメソッド：引数で指定された文字列に対するバインドが存在しない場合のみ処理を行う
  ```
    app()->bindIf('Number100', function () {
      return new Number(100);
    });
    app()->bindIf('Number100', function () {
      return new Number(200);
    });
    $numcls = app()->make('Number100');
    $number = $numcls->getNumber();
    // $number = 100
  ```
  - singletonメソッド：インスタンスを1つのみにする（インスタンスはキャッシュされる）
  ```
    app()->singleton('random', function () {
      return new RandomNumber();
    });
    $number1 = app('random);
    $number2 = app('random);
    // $number1->getNumber() === $number2->getNumber()
  ```
  - instanceメソッド：既に生成したインスタンスをサービスコンテナにバインド
  ```
    $numcls = new Number(1001);
    app()->instance('SharedNumber', $numcls);

    $number1 = app('SharedNumber')
    $number2 = app('SharedNumber')
    // $number1->getNumber() === $number2->getNumber()
  ```
  - whenメソッド
  - 別の文字列による解決処理のバインド：第2引数を文字列 => 同時に解決
  ```
    $app()->singleton(Illuminate\Contracts\Http\Kernel::class, App\Http\Kernel::class);
    //App\Http\Kernelというクラス名により解決される
    $app = app(Illuminate\Contracts\Http\Kernel::class);
  ```
  - 複雑なインスタンス構築処理のバインド
  - バインドの定義場所：app\ProvidersフォルダにServiceProviderクラスを作成して定義
  ```
    <?php
    namespace App\Providers;
    use Illuminate\Support\ServiceProvider;
    class AppServiceProvider extends ServiceProvider
    {
        /**
        * Register any application services.
        *
        * @return void
        */
        public function register()
        {
            //インスタンス生成時に他のクラスを利用する必要があるときは、ここでバインド処理
        }
        /**
        * Bootstrap any application services.
        *
        * @return void
        */
        public function boot()
        {
            //通常はここにバインド処理
        }
    }
  ```

#### 2-2-4 解決
- makeメソッド
```
  app()->bind(Number::class, function () {
    return new Number();
  });

  $number1 = app()->make(Number::class);
```
- appヘルパ関数
```
  app()->bind(Number::class, function () {
    return new Number();
  });

  $number2 = app(Number::class);
```
- バインドしていない文字列の解決

#### 2-2-5 DI（DependencyInjection）とサービスコンテナ
- 拡張可能なプログラム設計
  - 密結合 => 疎結合
- クラスが別クラスと依存関係にあるコード
```
  class UserService
  {
    public function sendotification(string $to, string $message) : void
    {
      $mailsender = new MailSender();
      $mailsender->send($to, $message);
    }
  }

  class MailSender
  {
    public function send(string $to, string $message) : void
    {
      // メール送信
    }
  }
```
- 引数でインスタンスを与えるコード
```
  class UserService
  {
    public function sendNotification(MailSender $mailsender, string $to, string $message) : void
    {
      $mailsender->send($to, $message)
    }
  }
```
- 抽象クラス（インターフェース）に依存したコード
```
  class UserService
  {
    public function sendNotification(NotifierInterface $notifier, string $to, string $message) : void
    {
      $notifier->send($to, $message);
    }
  }
```
- コンストラクタインジェクション  
コンストラクタの引数でインスタンスを注入する方法
```
  class UserService
  {
    protected $notifier;
    public function __construct(NotifierInterface $notifier)
    {
      $this->notifier = $notifier;
    }
    public function sendNotification(string $to, string $message) : void
    {
      $this->notifier->send($to, $message);
    }
  }

  $user = app()->make(UserService::class);
  $user->sendNotification('to', 'message');

  //インターフェイスのバインド例
  app()->bind(NotifierInterface::class, function () {
    return new MailSender();
  });
```
- メソッドインジェクション  
メソッドの引数で必要とするインスタンスを渡す方法
```
  class UserService
  {
    public function sendNotification(NotifierInterface $notifier, string $to, string $message) : void
    {
      $notifier->send($to, $message);
    }
  }

  $service = app(UserService::class);
  $to = 'address';
  $message = 'message';
  app()->call([$sercice, 'sendNotification'], ['to' => $to, 'message' => $message]);
```
- コンテキストに応じた解決
```
  class UserService
  {
    protected $notifier;
    public function __construct(NotifierInterface $notifier)
    {
      $this->notifier = $notifier;
    }
  }

  class AdminService
  {
    protected $notifier;
    public function __construct(NotifierInterface $notifier)
    {
      $this->notifier = $notifier;
    }
  }
  
  app()->when(UserService)
       ->needs(NotifierInterface::class)
       ->give(PushSender::class);

  app()->when(AdminService)
       ->needs(NotifierInterface::class)
       ->give(MailSender::class);
```

#### 2-2-6 ファサード
- クラスメソッド形式でフレームワークの機能を簡単に利用できる
- ex）Configファサードから値を取得する
```
  $debug = \Config::get('app.debug');
```
  - config/app.phpのAliasesキーの定義
  ```
    'aliases' => [

          'App' => Illuminate\Support\Facades\App::class,
          'Arr' => Illuminate\Support\Arr::class,
          'Artisan' => Illuminate\Support\Facades\Artisan::class,
          'Auth' => Illuminate\Support\Facades\Auth::class,
          'Blade' => Illuminate\Support\Facades\Blade::class,
          // 略
      ],
  ```
  - Illuminate/Support/Facades/Config.phpにもgetは未定義
  - Illuminate/Support/Facades/Facade.phpにもgetは未定義
  - Facade.phpの__callStaticが呼ばれる
  ```
      /**
      * Handle dynamic, static calls to the object.
      *
      * @param  string  $method
      * @param  array  $args
      * @return mixed
      *
      * @throws \RuntimeException
      */
      public static function __callStatic($method, $args)
      {
          $instance = static::getFacadeRoot();

          if (! $instance) {
              throw new RuntimeException('A facade root has not been set.');
          }

          return $instance->$method(...$args);
      }
    ```
- 注意点
  - コードの密結合化：ロジックのどこからでも利用可能
  - config/app.phpの定義が変わると使えなくなる可能性あり
  - 短期間、小規模アプリはOK
  - ファサードを使わず、サービスコンテナから直接インスタンス利用も検討すること

---
### 2-3 サービスプロバイダ
- サービスプロバイダ：フレームワーク、アプリケーションに含まれるサービス（機能）の初期処理を行う
  - 1.サービスコンテナへのバインド
  - 2.イベントリスナーやミドルウェア、ルーティングの登録
  - 3.外部コンポーネントを組み込む
- 読み込むサービスプロバイダ：config/app.phpのprovidersプロパティ
```
  'providers' => [

      /*
        * Laravel Framework Service Providers...
        */
      Illuminate\Auth\AuthServiceProvider::class,
      Illuminate\Broadcasting\BroadcastServiceProvider::class,
      Illuminate\Bus\BusServiceProvider::class,
      Illuminate\Cache\CacheServiceProvider::class,
      // 略
      /*
        * Package Service Providers...
        */

      /*
        * Application Service Providers...
        */
      App\Providers\AppServiceProvider::class,
      App\Providers\AuthServiceProvider::class,
      // App\Providers\BroadcastServiceProvider::class,
      App\Providers\EventServiceProvider::class,
      App\Providers\RouteServiceProvider::class,

  ],
```

#### 2-3-1 サービスプロバイダの基本的な動作
- Laravelの初期処理
  - 1.各サービスプロバイダのregisterメソッド（必須）
  - 2.各サービスプロバイダのbootメソッド（任意）

#### 2-3-2 DeferrableProviderインターフェイスの遅延実行
- 通常は起動時に処理
- Illuminate\Contracts\Support\DeferrableProviderインターフェイス実装 => 遅延実行

---
### 2-4 コントラクト
#### 2-4-1 コントラクトの基本
- コントラクト：Laravelのコアコンポーネントで利用されている関数をインターフェイスとして定義したもの
  - 疎結合なコード
    - コアコンポーネントの機能を利用するクラス：コアコンポーネントの抽象クラスに依存
    - インターフェイスを指定
    - 機能を差し替えることが可能

#### 2-4-2 コントラクトを利用した機能の差し替え
---

## Chapter3 アプリケーションアーキテクチャ
---
## Chapter4 HTTPリクエストとレスポンス
---
### 4-4 ミドルウェア
#### 4-4-1 ミドルウェアの基本
- Laravelのミドルウェア  
コントローラクラスの前後に位置し、HTTPリクエストのフィルタリング、HTTPレスポンスの変更を行う
- ミドルウェアの種類
  - 1.グローバルミドルウェア：システム全体で使用する
  - 2.ルートミドルウェア：特定のルートに対し使用される
  - 3.コンストラクタ内ミドルウェア：コントロールクラスのコンストラクタで指定する
- Illuminate\Pipeline\Pipelineクラスで実行

#### デフォルトで容易されているミドルウェア
- app/Http/Kernel.phpで定義
```
    /**
     * The application's global HTTP middleware stack.
     *
     * These middleware are run during every request to your application.
     *
     * @var array<int, class-string|string>
     */
    protected $middleware = [
        // \App\Http\Middleware\TrustHosts::class,  //信頼できるhostを記述（デフォルトoff）
        \App\Http\Middleware\TrustProxies::class,   //リバースプロキシ、ロードバランサ、CDN利用時など
        \Fruitcake\Cors\HandleCors::class,  //ブラウザのセキュリティ設定、サーバーでCORS設定時は不要
        \App\Http\Middleware\PreventRequestsDuringMaintenance::class,   //メンテナンスの振る舞いを変更
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class, //リクエストボディのサイズチェック
        \App\Http\Middleware\TrimStrings::class,  //リクエストにtrim処理を行い空文字を削除
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,  //リクエストの空文字をNull
    ];
```
- ルートミドルウェア
```
    /**
     * The application's route middleware groups.
     *
     * @var array<string, array<int, class-string|string>>
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class, //クッキーの暗号化、複合化
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,  //Cookie::queueで登録した値をレスポンスにクッキーとして追加
            \Illuminate\Session\Middleware\StartSession::class, //セッションを有効、レスポンス返却時にセッションに書き込み
            // \Illuminate\Session\Middleware\AuthenticateSession::class, //パスワード変更時に他のデバイスでログインしているユーザーをログアウトさせる
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,  //Bladeテンプレートのerror変数にエラー内容を埋め込み
            \App\Http\Middleware\VerifyCsrfToken::class,  //CSRF対策のtoken発行、チェック
            \Illuminate\Routing\Middleware\SubstituteBindings::class, //Route Model Binding
        ],

        'api' => [
            // \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
            'throttle:api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];
```
- 名前付きミドルウェア
```
    /**
     * The application's route middleware.
     *
     * These middleware may be assigned to groups or used individually.
     *
     * @var array<string, class-string|string>
     */
    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class, //認証済みユーザの判定
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class, //Basic認証を行う
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,  //ETagを利用しキャッシュを制御
        'can' => \Illuminate\Auth\Middleware\Authorize::class,  //特定のモデルやリソースへのアクションに対して認可
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class, //認証済みかどうか判定し、済みは/homeへレダイレクト
        'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,  //署名付きアクセスで有効かどうか
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class, //同一ユーザー規定回数アクセス判定
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class, //メールアドレス認証済み判定
    ];
```

#### 4-4-3 独自ミドルウェアの実装

## Chapter5 データベース
### 5-1 マイグレーション
- マイグレーション：データベースのテーブル作成、定義変更の管理機能

#### 5-1-1 マイグレーション処理の流れ
- マイグレーション処理コマンド
```
  php artisan make:migration (ファイル名) (オプション)
```
- 作成されたマイグレーションファイルに定義と削除を記述
- 実行コマンド。ロールバックコマンド
```
  php artisan migrate
  php artisan migrate:rollback
```

#### 5-1-2 マイグレーションファイルの作成
- コマンド
```
  php artisan make:migration (ファイル名) (オプション)
```
|オプション|機能|
|---|---|
|--create=(テーブル名)|新規テーブル作成|
|--table=(テーブル名)|テーブル操作|
|--path=(テーブル名)|指定パスにマイグレーションファイルを配置|
- ファイル名について
  - 命名規則はない
  - 作業内容をわかるファイル名にする

#### 5-1-3 定義の記述
- スキーマビルダ：schemaファサード
|カラムタイプ|スキーマビルダ|備考|
|---|---|---|
||||

## Chapter9 テスト
### 9-1 ユニットテスト
#### 9-1-1 テスト対象クラス
- ユニットテスト
  - クラス
  - メソッド
  - 関数など
