# 入門JavaScriptプログラミング  
[ソースコード：Github](https://github.com/jisaacks/get-programming-jsnext)

## LESSON 1 ECMAScript仕様とプロポーザルプロセス
### 1.1 ECMAScriptの略史
|西暦|ECMAScriptのバージョン|
|---|---|
|1995年|JavaScriptがNetscapeで開発|
|1997年|ECMAScript First Editionが公開|
|1998年|ECMAScript Second Editionが公開|
|1999年|ECMAScript Third Edition(ES3)が公開|
||ECMAScript Forth Edition(ES4)は放棄|
||ECMAScript 3.1の小変更バージョンは開発継続|
|2009年|ECMAScript Fifth Edition(ES5)が公開(3.1の名前変更)|
|2015年|ECMAScript Sixth Edition(ES6)(ES2015)が公開|※大きな変更
|2016年|ECMAScript Seventh Edition(ES7)(ES2016)が公開|  

※以後は、毎年新しいバージョンをリリース

### 1.2 ES2015で多くの機能が追加されたのか
- 新しい構文
- 演算子
- プロミティブ
- 既存のオブジェクトの機能を拡張したオブジェクト

### 1.3 何が追加されるのか誰が決めるのか
- ECMAScript仕様の開発と管理：ECMA International TC39(Mozilla、Google、Microsoft、Apple)
- 仕様策定ステージ
  - ステージ0：Strawman
    - アイデア提案
  - ステージ1：Proposal
    - 叩き台の作成 => メリット、振る舞い、外観の説明
  - ステージ2：Draft
    - 提案が完全な仕様にまとめられ、実装は「実験的なもの」
  - ステージ3：Candidate
    - 仕様が完成したとみなされ、承認している
  - ステージ4：Finished
    - 正式なECMAScrpt標準仕様の一部となる準備が整っている
  - 関連ページ
    - [ECMA International](https://www.ecma-international.org/)
    - [Github Repository](https://github.com/tc39/ecma262)

## LESSON 2 Babelを使ったトランスパイル
### 2.1 トランスパイルとは何か
- トランスパイル(transpile)：「translate」＋「compile」の混成語
- トランスパイラ：特殊なコンパイラで「あるプログラミング言語のソース」=>「別なプログラミング言語のソース」
- JavaScript言語へのコンパイル
  - CoffeeScript：JavaScriptの「よいパーツ」に主眼を置いている(2009年)
  - Compile-to-JavaScript言語：ClojureScript、PureScript、TypeScript、Elmなど
  - JavaScriptは必ずしも最善のコンパイル先言語でない => 他に選択肢はない
    - WebAssembly(wasm)が発表された => JavaScript以外の受け皿になるかも
- Babelの位置付け
  - JavaScriptの別バージョンを特定のバージョンにコンパイル可能
    - Traceur
    - Babel：ES6toES5からBabelとなった

### 2.2 Babelのセットアップ
- [Babel公式](https://babeljs.io/)
- 提供：NPM(Node Package Manager)パッケージ、Node.jsにバンドル
- プロジェクトの初期化
```
$ mkdir <project_name>
$ cd <project_name>
$ npm init -y           # npmがこのディレクトリ管理を行うため、package.jsonを作成（-y：yesでdefault値を使う）
```
- Babelのセットアップ
```
$ npm install --save-dev @babel/core @babel/cli
$ touch .babelrc    # .babelrcを作成
```
- .babelrcの記載
```
{
  "presets": ["@babel/preset-env"]
}
```
- @babel/preset-envのインストール
```
$ npm install @babel/preset-env --save-dev
```
- node_moduleフォルダはGitでpushする必要がない => .gitignore `node_module/`
- 実行`babel <元フォルダ名> -d <出力フォルダ名>`

### 2.3 本書に必要なBabelの構成
- デコレータ：クラスやメソッドに機能を追加するもの
- ソースマップ：圧縮されたコードを元の形式に戻し、デバッグを容易にする
- 設定`.babelrc`
```
{
  "presets": ["@babel/preset-env"]
  "plugins": [
    ["@babel/plugin-proposal-decorators", { "legacy": true }]
  ],
  "sourceMaps": "inline"
}
```
- インストール
```
$ npm install @babel/core --save-dev
$ npm install @babel/preset-env --save-dev
$ npm install @babel/plugin-proposal-decorators --save-dev
```
- BabelをNPMのスクリプトとしてセットアップする`package.json`
```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "babel": "./node_modules/.bin/babel src -d dist"  # パスを通す必要がある場合
},
```

## LESSON 3 Browserifyによるモジュールのバンドリング
- Bundler：複数のモジュールを一つのファイルにまとめるツール
  - Browserify => 本書で使用
  - Webpack => メジャー
  - Rollup

### 3.1 モジュールとは何か
- モジュール化されたコード
  - Ruby：gem
  - Python：egg
  - Java：パッケージ
  - JavaScript：Node.jsのモジュールが普遍的な存在

### 3.2 Node.jsのモジュールの仕組み
- Node.jsのモジュールシステム
  - NPM：パッケージマネージャー
  - Node.js ≠ CommonJS、Node.js ≒ CommonJS

### 3.3 Browserifyとは何か
- 開発中：Node.jsと同じ方法でモジュール定義
- 開発後：1つのファイルにバンドル

### 3.4 BrowserifyはES6のモジュールをどのように手助けするか
- Babel：ソースの変換のみ、モジュールシステムは提供されない
- BabelがES2015のモジュールを変換 => Browserifyがバンドル

### 3.5 BrowserifyとBabelのセットアップ
- Browserifyのインストール
```
$ npm install browserify --global   # --globalでインストールすると全て
```
- Babelifyを使ってプロジェクトをセットアップ
  - LESSON 2のBabelのプリセット／プラグインとBabelifyをインストール
  ```
  $ mkdir <project_name>
  $ cd <project_name>
  $ npm init -y
  $ touch .babelrc
  $ npm install --save-dev @babel/core @babel/cli
  $ npm install @babel/preset-env --save-dev
  $ npm install @babel/plugin-proposal-decorators --save-dev
  $ npm install babelify --save-dev
  ```
  - `.babelrc`
  ```
  {
    "presets": ["@babel/preset-env"]
    "plugins": [
      ["@babel/plugin-proposal-decorators", { "legacy": true }]
    ],
    "sourceMaps": "inline"
  }
  ```
  - 変換を実行
  ```
  $ ./node_modules/.bin/browserify src/index.js --transform babelify --outfile dist/bundle.js --debug
  ```
  - bundle.jsの実行
  ```
  node dist/bundle.js
  ```
  - HTMLでの実行
  ```
  <script src="dist/bundle.js"></script>
  ```

# UNIT 1 変数と文字列
## LESSON 4 letを使った変数宣言
### 4.1 letのスコープの仕組み
- let：letで宣言された変数はブロックスコープ(block scope)を持つ
```
if (true) {
  let foo = 'bar';
}
console.log(foo); // fooはブロックの外側では存在しないため、エラー
```
  - ブロック：{}の中
  - 変数が予測可能なものになる => 宣言の外側で使用されるバグを無くせる
- ブロックスコープのletが推奨されるのはなぜか
  - var：varで宣言された変数は関数スコープ(function scope)を持つ
  ```
  (finction() {
    if (true) {
      var foo = 'bar';
    }
    console.log(foo); //fooは参照可能
  }());
  ```
  - IIFE (Immediately Invoked Function Expression; 即時実行関数式)
  ```
  (function () {
    文
  })();
  ```
    - ES5までのスコープを作成する唯一の手段
    - 最初の部分は グループ化演算子 () に囲まれた静的スコープ付きの無名関数です。これは IIFE イディオム内で、汚いグローバルスコープと同様に変数へアクセスすることを防ぐ
    - 2つ目の部分は即時実行関数式の () で、これを通じて JavaScript エンジンは直接関数を解釈実行

### 4-2. letによる変数の巻き上げの仕組み
- 巻き上げ(Hoisting)：変数が宣言されたスコープ全体を消費する
  - 変数は宣言される前であってもアクセスできる => アクセスされたら、参照エラー(ReferenceError)
  ```
  {
    console.log(foo); //参照エラー
    let foo = 2;
  }
  ```

### 4-3. 今後は`var`の代わりに`let`を使用すべきか
- 筆者：`let`を使うべき
  - 関数を特別扱いしない
  - 巻き上げの挙動の違い、var：未定義、let：エラー
  - 既存のvarを完全にletに置き換えるのは反対

## LESSON 5 constを使った変数宣言
### 5.1 定数の仕組み
- 定数(constant)：constで宣言された値、再代入は不可
```
const MyConst = 5;
MyConst = 6;        // エラー
```
  - 文字列、数字：プリミティブ => イミュータブル（不変）
  ```
    let a = "Hello";
    let b = a;

    b += ", World!";

    console.log(a);   // "Hello"
    console.log(b);   // "Hello, World!"
  ```
  - オブジェクト => ミュータブル（可変）
  ```
    const mutableConstant = {};
      
    mutableConstant.foo = 'bar';  // 可変
    mutableConstant.foo = 'baz';  // 可変
    mutableConstant = {foo: 'bat'};  // 新しいオブジェクトを代入は不可
  ```

### 5.2 定数はいつ使用するか
- フラグ：一意な識別子
- 変数への再代入が不要な場合
- 定数は再代入されないので、パフォーマンス向上が期待できる
- 再代入されないところは全てconstで書き、必要に応じletを使う
```
  const ADD_ITEM = 'ADD_ITEM';
  const DEL_ITEM = 'DEL_ITEM';
  const CLEAR_ALL_ITEMS = 'CLEAR_ALL_ITEMS';
  const items = [];

  function actionHandler(action) {
    switch (action.type) {
      case ADD_ITEM:
        items.push(action.item);
      break;
      case DEL_ITEM:
        items.splice(items.indexOf(action.item), 1);
      break;
      case CLEAR_ALL_ITEMS:
        items.splice(0, items.length);
      break;
    }
  }
```

## LESSON 6 新しい文字列メソッド
### 6.1 文字列の検索
- 自己文書化(self documenting)：ES6以降で何をするかわかりやすくしたメソッド
- 文字列の検索
  - includes：含まれるかどうか
  - startsWith：始まる
  - endsWith：終わる
  ```
    let str = 'foo-bar'

    str.startsWith('foo');   // true
    str.startsWith('bar');   // false

    str.endsWith('foo');   // false
    str.endsWith('bar');   // true

    str.includes('foo');   // true
    str.includes('bar');   // true
    str.includes('o-b');   // true
  ```
  - 大文字／小文字を区別する
  ```
    let location = 'Atlanta, Ga'

    location.endsWith('GA');                // false
    location.endsWith('ga');                // false
    location.toLowerCase().endsWith('ga');  // true
  ```
  - ２つ目のパラメータは検索開始位置
  ```
    let locationA = 'Atlanta, Ga'
    let locationB = 'Galveston, Tx'

    locationA.includes('Ga');                           // true
    locationB.includes('Ga');                           // true
    locationA.includes('Ga', locationA.index0f(' '));   // true
    locationB.includes('Ga', locationA.index0f(' '));   // false
  ```

### 6.2 文字列のパディング  
- String.prototype.repeat：文字列の長さになるまで埋め文字を設定する
```
  function binaryIP(decimalIPStr) {
    return decimalIPStr.split('.').map(function(octet) {
      let bin = Number(octet).toString(2);
      return '0'.repeat(8 - bin.length) + bin;
    }).join('.');
  }

  binaryIP('192.168.2.1');  // "11000000.10101000.00000010.00000001"
```
```
  'X'.repeat(4);      // 'XXXX'
  'X'.repeat(4.9);    // 'XXXX' 切り捨て
  'foo'.repeat(3.9);  // 'foofoofoo'
```
- String.prototype.padStart、String.prototype.padEnd：2文字以上のパディング
```
  `abc`.padStart(6);      // "   abc"
  `abc`.padEnd(6);        // "abc   "

  `abc`.padStart(6, 'x'); // "xxxabc"
  `abc`.padEnd(6, 'x');   // "abcxxx"

  `abc`.padStart(6, 'xyz'); // "xxxabc"
  `abc`.padEnd(6, 'xyz');   // "abcxxx"

  `abc`.padStart(8, '123'); // "12312abc"
  `abc`.padEnd(5, '123');   // "abc12"
```
```
  function binaryIP(decimalIPStr) {
    return decimalIPStr.split('.').map(function(octet) {
      return Number(octet).toString(2).padStart(8, '0')
    }).join('.');
  }

  binaryIP('192.168.2.1');  // "11000000.10101000.00000010.00000001"
```

## LESSON 7 テンプレートリテラル
- テンプレートリテラル(template literal)：複数行の文字列に対応
### 7.1 テンプレートリテラルとは何か
- テンプレートリテラル：文字列を作成するための新しいリテラル構文
```
  let str = "Hello, World!";
  let str = 'Hello, World!';
  let str = `Hello, World!`;
  console.log(str1 == str3);  // true
  console.log(str2 == str3);  // true
```
  - 文字列補完：文字列内挿（動的な値の配置）、以前は連結のみ
  ```
    let interpolated = 'You have ${cart.length} items in your cart';
    let concatenated = 'You have ' + cart.length + ' items in your cart';
  ```
    - ${}で補完可能、\スケープ
    ```
      let color = 'hazel';
      let str1 = 'My eyes are ${color}';    // "My eyes are hazel"
      let str1 = 'My eyes are \${color}';   // "My eyes are ${color}"
    ```
    - 補完する値が文字列ではない場合、文字列に変換される

  - 複数行の文字列
  ```
    let multiline = 'line one
                     line two'
  ```
    - 終端記号がでるまで続く
    - ホワイトスペース文字が全て維持される

  - タグ付きテンプレートリテラル
  ```
    let html = doming'<div class="first-name">${userInput}</div>';
  ```

### 7.2 テンプレートリテラルは再利用可能なテンプレートではない  
テンプレートという名前に惑わされない
```
  let name = 'Talan';

  let greetB = 'Hello, ${name}';
  greetB;             // "Hello, Talan"
  greetB('Jonathon')  // Type Error

  function greet(name) {
    return 'Hello, ${name}';
  }
  greet;             // "Hello, Talan"
  greet('Jonathon')  // "Hello, Jonathon"
```

### 7.3 タグ付きテンプレートリテラルを使ったカスタム処理
- 組み込みのTag関数はString.rawのみ
- 独自の作成は可能

## LESSON 8 DSLを構築する
- DSL(Domain Specific Language)  
機能や構文をうまく利用することで、最初からサポートされたように特定のタスクを解決する
  - JavaScriptはDSLの作成が容易でない

# UNIT 2 オブジェクトと配列
## LESSON 9 新しい配列メソッド
### 9.1 Array.from を使って配列を作成する
- Array.from：配列のようなオブジェクトを配列に変換する
  - 引数を配列にする
  ```
    function avg() {
      // const args = [].slice.apply(arguments); <= 従来の書き方
      const args = Array.from(arguments);
      const sum = args.reduce(function(a, b) {
        return a + b;
      });
      return sum / arguments.length;
    }
  ```
  - document.querySelectorAllとの組み合わせ NodoList => Array
  - lengthプロパティを持つオブジェクトは変換可能
  ```
    let nodes = document.querySelectorAll('.accordian-panel');
    // let nodesArr = [].slice.call(nodes); <= 従来の書き方
    let nodesArr = Array.from(nodes);
    nodesArr.forEach(activatePanel);
  ```

### 9.2 Array.ofを使って配列を作成する
- Array コンストラクタの振る舞い
```
  let a = new Array(1, 2, 3); // [ 1, 2, 3 ]
  let b = new Array(1, 2);    // [ 1, 2 ]
  let c = new Array(1);       // [ <1 empty item> ] ※振る舞いが異なる
```
- Array.of
```
  let a = Array.of(1, 2, 3); // [ 1, 2, 3 ]
  let b = Array.of(1, 2);    // [ 1, 2 ]
  let c = Array.of(1);       // [ 1 ]
```
- リテラルでも同様のことができるが、Arrayのサブクラスで使用はできない
```
  let a = [ 1, 2, 3 ]; // [ 1, 2, 3 ]
  let b = [ 1, 2 ];    // [ 1, 2 ]
  let c = [ 1 ];       // [ 1 ]
```

### 9.3 Array.prototype.fillを使って配列を作成する
- new Array(9)で作った配列は、map等は使えない
```
  const board = new Array(9).map(function(i) {
    return ' ';
  });

  const board1 = new Array(9).fill(' ');  // fillで値を埋める必要あり
```
```
  [`a`, `b`, `c`,] の構造
  {
    length: 3;
    0: 'a';
    1: 'b';
    2: 'c';
  }

  new Array(9) の構造
  {
    length: 9; のみで値はない
  }
```

### 9.4 Array.prototype.includesを使って配列を検索する
- 従来：indexOf => 真偽値ではない
- includes => 真偽値なので扱いやすい
```
const arr = [grid, list];
arr.includes('list');     // true
```

### 9.5 Array.prototype.findを使って配列を検索する
- 従来：filterメソッド => 検索が全て終わらないと値が返らない
- find：該当したら、該当した要素を返す

## LESSON 10 Object.assign
### 10.1 Object.assignを使ってデフォルト値を設定する
- Object.assaign：引数として任意の数のオブジェクトを受け取り、後続のオブジェクトの値を全て最初のオブジェクトに割り当てる  
同一キーがある場合は上書きされる
```
  let a = { x: 1, y: 2, z: 3 }
  let b = { x: 5, y: 6 }
  let c = { x: 12 }

  Object.assign(a, b, c)

  console.log(a);   // { x: 12, y: 6, z: 3 }
```

### 10.2 Object.assignを使ってオブジェクトを拡張する
```
  let a = { x: 1, y: 2 }
  let b = { z: 3 }

  Object.assign(a, b)

  console.log(a);   // { x: 1, y: 2, z: 3 }
```

### 10.3 Object.assignの使用時にミューテーションを回避する
- コピーを作成して、要素を追加
```
  let a = { x: 1, y: 2 }
  let b = { z: 3 }

  let newObject = Object.assign({}, a, b)

  console.log(a);         // { x: 1, y: 2 }
  console.log(b);         // { z: 3 }
  console.log(newObject); // { x: 1, y: 2, z: 3 }
```

### 10.4 Object.assignが値を割り当てる仕組み  
割り当て = 代入  

## LESSON 11 分割
### 11-1 オブジェクトを分割する
```
  let geolocation = {
    "location": {
      "lat": 51.0,
      "lng": -0.1
    },
    "accuracy": 1200.4
  }

  let { location: {lat, lng}, accuracy } = geolocation; // <= これがES5までは不可

  console.log(lat); // 51.0
  console.log(lng); // -0.1
  console.log(accuracy); // 1200.4
  console.log(location); // undefined
```

### 11.2 配列を分割する
```
  let location = ['Atlanta', [33.7490, 84.3880]];

  let [ place, [lat, lng] ] = location;

  console.log(place); // "Atlanta"
  console.log(lat); // 33.7490
  console.log(lng); // 84.3880
```
- 応用することで、一時的な変数が不要となる
```
  let tmp = start;
  start = stop;
  stop = tmp;

  [start, stop] = [stop, start]; // 1行で書ける
```

### 11.3 配列とオブジェクトの分割を組み合わせる
```
  let product = {
    name: 'Whiskey Glass',
    details: {
      price: 18.99,
      description: 'Enjoy your whiskey in this glass'
    },
    images: {
      primary: '/images/main.jpg',
      others: [
        '/images/1.jpg',
        '/images/2.jpg'
      ]
    }
  }

  let {
    name,
    details: { price, description},
    images: {
      primary,
      others: [secondary, tertiary]
    }
  } = product;

  console.log(name);        // "Whiskey Glass"
  console.log(price);       // 18.99
  console.log(description); // "Enjoy your whiskey in this glass"
  console.log(primary);     // "/images/main.jpg"
  console.log(secondary);   // "/images/1.jpg"
  console.log(tertiary);    // "/images/2.jpg"

  console.log(details);     // undefined
  console.log(images);      // undefined
  console.log(others);      // undefined
```

### 11.4 分割可能なデータ型は何か
- 数字は変数名に使えないので変更が必要
```
  const { 0:a, 1:b, length } = ['foo', 'bar']

  console.log(a)      // "foo"
  console.log(b)      // "bar"
  console.log(length) // 2
```
- Stringも使える
```
  const [first, second, last] = 'abc'

  console.log(first)  // "a"
  console.log(second) // "b"
  console.log(last)   // "c"
```

## LESSON 12 新しいオブジェクトリテラル構文
### 12.1 簡略表記のプロパティ名
```
  const message = { text: text }  // 同じ意味
  const message = { text }        // 同じ意味
```

### 12.2 簡略表記のメソッド名
- () = finction()と同じ
```
  function createStateManager() {
    const state = {};
    return {
      update: function(changes) {
        Object.assign(state, changes);
      },
      getState: function() {
        return Object.assign({}, state);
      }
    }
  }

  reateStateManager() {
    const state = {};
    return {
      update(changes) {
        Object.assign(state, changes);
      },
      getState() {
        return Object.assign({}, state);
      }
    }
  }
```
- 無名関数(anonymous function)になるので、関数を名前で参照するのはできない
```
  const fibonacci = {
    at(n) {
      if (n <= 2) return 1;
      return at(n - 1) + at (n - 2);  // <= 再帰的に呼び出しているのでエラー
    }
  }
  fibonacci.at(7); // "ReferenceError: at is not defined"
  
  const fibonacci = {
    at(n) {
      if (n <= 2) return 1;
      return this.at(n - 1) + this.at (n - 2);  // <= thisでも解決可だが全てではない
    }
  }
  const { at } = fibonacci;
  const fib = { at };
  const nacci = { find: at };

  fibonacci.at(7); // 13
  at(7);           // "ReferenceError"
  fib.at(7);       // 13
  nacci.find(7);   // "ReferenceError"
```
- 自己参照が必要な場合は、簡略表記のメソッド名は使用しない

### 12.3 計算されたプロパティ名
```
  function setValue(name, value) {
    const changes = {};
    changes[name] = value;
    stateManager.update(changes);
  }

  function setValue(name, value) {
    stateManager.update({
      [name]: value
    });
  }
```

## LESSON 13 シンボル - 新しいプリミティブ
- プリミティブ
  - 文字列、数字、boolean、null、undefined
  - シンボル：ES2015から追加
    - 一意なシンボル
    - グローバルシンボル
    - well-knownシンボル

### 13.1 シンボルを定数として使用する
- フラグ：識別を唯一の目的とする特殊な変数
  - 名前が同じ場合に競合する可能性がある
  - シンボルは常に一意
  ```
    'str' === 'str';                  // true
    Symbol('str') === Symbol('str');  // false
  ```

### 13.2 シンボルをオブジェクトのキーとして使用する
- メタプロパティ(meta propaty)：プライベートではなく、アクセスできるが、隠されたAPI
```
  const Store = {
    // プロパティ名は計算されたプロパティ名でなければならない
    [Stmbol('_internals')]: {/* ... */}
  };
```
- getOwnPropatySymbolsメソッド：シンボルであるものが全て含まれた配列を返す

### 13.3 グローバルシンボルを使って振る舞いへのフックを作成
- グローバルシンボル(global symbol)：グローバルにアクセスできるシンボル
```
  const stm = Symbol.for('my symbol');  // 一意でない
```

### 13.4 well-knownシンボルでアブジェクトの振る舞いを変更する
- well-knownシンボル：Symbol.toPrimitiveのようにSymbolに直接割り当てられた組み込みシンボル
- Symbol.toPrimitive
  - string
  - number
  - default
  ```
  const age = {
    number: 32,
    string: 'thirty-two',
    [Symbol.toPrimitive](hint) {
      switch(hint) {
        case 'string':
          return this.string;
        break;
        case 'number':
          return this.number;
        break;
        default:
          return `${this.string}(${this.number})`;
        break;
      }
    }
  };

  console.log(age + '');  // thirty-two(32)
  console.log(`${age}`);  // thirty-two
  console.log(`${+age}`); // 32
  ```

### 13.5 シンボルの注意点
- シンボルはリテラルを持たないプリミティブ
- 作成は、Symbol関数の呼び出しのみ
- newは使えない

## LESSON 14 ロックとキーをシミュレートする
### 14.1 ロックとキーのシステムを作成する
- ロックAPIを設計  
`{ key, unlock } = lock(secret)`
```
  function lock(secret) {
    const key = Symbol('key')

    return {
      key, unlock(keyUsed) {
        if (keyUsed === key) {
          return secret
        } else {
          return '*'.repeat( secret.length || secret.toString().length )
        }
      }
    }
  }

  const { key, unlock } = lock(42);

  unlock();              // **
  unlock(Symbol('key')); // **
  unlock('key');         // **
  unlock(key);           // 42
```

### 14.2 Choose the Doorゲームを作成する

# UNIT 3 関数
## LESSON 15 デフォルトパラメータとレストパラメータ
### 15.1 デフォルトパラメータ
- パラメータのデフォルト値を設定することで可読性があがる
- 式を利用することも可能
- 必須パラメータは、オプションパラメータの前
```
  function display(width = 800, height = width /2) {
    /* ... */
  }

  function range(min = 0, max) {
    /* ... */
  }
  range(5, 10); // min = 5, max = 10
  range(10);    // min = 10, max = undefined
```

### 15.2 デフォルトパラメータを使ってデータの再計算をスキップする
- Aメソッド、Bメソッドの内部で同じCメソッドを使っている場合
```
  A(arg1) {
    const foo = this.C();
  }
  B(arg2) {
    const foo = this.C();
  }

  A(arg1, foo = this.C()) {

  }
  B(arg2, foo = this.C()) {

  }
```

### 15.3 レスト演算子を使ってパラメータをまとめる
- argumentsを配列としてまとめる
- 余りのパラメータをまとめる：Rubyの*と同じ
- レストパラメータは最後のみ使用可
```
  function avg() {
    const args = Array.from(arguments);
  }

  function avg(...args) { // ...で始まる=>レストパラメータ
  }
```

### 15.4 レスト演算子を使って関数の間で引数を受け渡す
- メソッド再定義
```
  const originalProcess = current.process;
  current.process = function(...args) {
    // 新機能
    return originalProcess.apply(current, args);
  }
```

## LESSON 16 パラメータの分割
### 16.1 配列パラメータを分割する
```
  function visualize(inserted, deleted, modified) {
    // 可視化した差分表示
  }
  const [ inserted, deleted, modified ] = diff(fileA, fileB);
  visualize(inserted, deleted, modified);

  function visualize([ inserted, deleted, modified ]){
    // 可視化した差分表示
  }
```

### 16.2 オブジェクトパラメータを分割する
- []配列 => {}オブジェクトへ変更
```
  function visualize({ inserted, deleted, modified }){
    // 可視化した差分表示
  }
```

### 16.3 名前付きパラメータをシミュレートする
```
  function car({ make = 'Ford', model = 'Mustang', year = 2017 } = {}) {
    /* 自動車の組み立て */
  }

  let modern = car(); // 全てデフォルト値 「= {}」があるため
  let classic = car({ year: 1965 });  // make, modelaは、デフォルト値
```

### 16.4 エイリアスパラメータを作成する
```
  function setCoordinates(lat = 33.7490, lon = -84.3880, lng = lon) {
    /* lat lon／lng とも使用可能 */
  }
  function setSize({ width = 50, height = width, w = width, h = height }) {
    /* w h 使用可能 */
  }
```

## LESSON 17 アロー関数
### 17.1 アロー関数を使ってコードを簡潔にする
- {}を省略できる
- パラメータの()を省略できるケースもある
- ()、{}ともつけるのは問題ない
```
  const double = x => x + x;        // パラメータ1つ
  const add = (a, b) => a + b;      // パラメータ2つ
  const rand = () => Math.random(); // パラメータがない
  const rest = (...args) => console.log(args); // ()必要
  const destruct = ([first]) => first; // ()必要

  const doTask = (a, b) => {  // {}が必要
    taskOne();
    taskTwo();
  }
```
- {}が省略されるメリット
  - 2文字入力が減る
  - 単一式の暗黙的returnが存在する（入力値が戻り値を指している）
  ```
    const exponent = exp => base => base ** exp;

    const square   = exponent(2);
    const cube     = exponent(3);
    const powerOf4 = exponent(4);

    square(5);   // 25
    cube(5);     // 125
    powerOf4(5); // 625
  ```

### 17.2 アロー関数を使ってコンテキストを維持する
```
  count Model = {
    ...
    get(propName) {

    },
    pluck(...props) {
      const that = this;
      return props.map(function(prop) {
        return that.get(prop);
      });
    }
    // 以下のように書ける
    plick(...props) {
      return props.map( prop => this.get(prop) );
    }
  }
```

### 17.3 アロー関数の注意点
- アロー関数：関数式に相当、関数定義ではない
```
  // 関数定義
  function double (number) {
    return number * 2;
  }
  // 関数式
  const double = function (number) {
    return number * 2;
  }
  // アロー関数
  const double = number => number * 2;
  }
```
- 巻き上げはできない
```
  const ids = getIds(); // ok
  function getIds() {
    //
  }
  const items = getItems(); //ReferenceError
  const getItems = () => {
    //
  }
```
- オブジェクトリテラルを返す場合
```
  const getSize = () => { width: 50, height: 50 };  // SyntaxError
  const getSize = () => ({ width: 50, height: 50 });  // ok
```
- アロー関数のコンテキストをbind、call、applyを使って変更することはできない

## LESSON 18 ジェネレータ関数
### 18.1 ジェネレータ関数を定義する
- *が付いている
- 呼び出しでnewを使用しない
```
  function *myGeneratorFunction() {
  function * myGeneratorFunction() {
  function* myGeneratorFunction() { // こちらが一般的
    // ...
  }

  const myGenerator = mygeneratorFunction();

  const = myObj {
    *myGen() {
      // ...
    }
  }
```
- ジェネレータ関数の内側のコード
  - イールディング(yielding)：コードを中断し、外部プロセスにアクセス
  ```
    function* myGeneratorFunction() {
      const message = 'Hello';
      yield message;  // 外部プロセスに'Hello'が返される
    }

    function* myGeneratorFunction() {
      const message = 'Hello';
      const response = yield message;  // 外部プロセスからのメッセージを補足
    }
  ```

### 18.2 ジェネレータ関数を使用する
- nextメソッドが呼び出されるまで何もしない
```
  function* myGeneratorFunction() {
    console.log('This code is now runnning');
  }

  const myGenerator = myGeneratorFunction();  // 何もしない
  myGenerator.next(); // This code is now runnning
```
```
  function* myGeneratorFunction() {
    console.log('A');
    yield;
    console.log('B');
  }

  const myGenerator = myGeneratorFunction();  // 何もしない
  myGenerator.next(); // A yieldで中断
  myGenerator.next(); // B
```
- nextメソッド
  - valueプロパティ：yieldに渡された値
  - doneプロパティ：ジェネレータの状態（中断、終了：true） nextの終了判断

### 18.3 ジェネレータ関数を使って無限の長さのリストを作成する
- JavaScript：先行評価 => 無限のリストはメモリが枯渇
- 遅延評価：値が呼ばれた時のみ必要 => メモリは不使用なので無限長も可能
- ジェネレータ関数を使って、無限長のリストも利用可能
```
  function* infinite() {
    let i = 0;
    while (true) {
      yield i++;
    }
  }
  const list = infinite();
  console.log( list.next().value ); // 0
  console.log( list.next().value ); // 1
  console.log( list.next().value ); // 2

  function take(gen, count) {
    return Array(count).fill(1).map(
      () => gen.next().value
    );
  }
  
  const a = take(infinite(), 7);
  console.log(a); // [ 0, 1, 2, 3, 4, 5, 6 ]
```

## LESSON 19 囚人のジレンマ

# UNIT 4 モジュール
## LESSON 20 モジュールの作成
### 20.1 モジュールのルール
- JavaScriptのモジュール：新しいコンテキスト
  - 下位互換性：不要 => 常にStrictモード(use Strict不要)
  - ルートレベルthis：undefined

### 20.2 モジュールを作成する
- 再利用可能な関数
  - 従来：グローバル関数として定義（他の選択肢がなかった）
  ```
    window.logStats = function(stat) {
      /* ... */
    }
  ```
  - モジュールとしてexport
  ```
    export default function logStats(stat) {
      /* ... */
    }
  ```
- exportの種類
  - デフォルトエクスポート(default export)
  ```
    export default function currency(num) {
      /* ... */
    }
  ```
    - 構文：`export default <式>`
    - 戻り値は1つのみ
    - `export default 5`も可能
  - 名前付きエクスポート
  ```
    export function currency(num) {
      /* ... */
    }
    
    function currency(num) {
      /* ... */
    }
    export { currency as othername, 別関数, ... }
  ```
    - 上記のどちらの構文でも可、1つ目が人気
    - 構文：`export { <バインディング1>, <バインディング1>, ... }`
    - `as othername`で別名も可能
    - `export  { 5 }`は不可
    - トップレベルでなければならない

### 20.3 JavaScriptファイルはどのタイミングでモジュールになるか

## LESSON 21 モジュールの使用
### 21.1 モジュールの場所を指定する
- 構文：`import <名前> from <場所>`
```
  import myFormatFunction from './my_format.js';
  import myFormatFunction from './my_format';
```
  - ローダー：Browserify, Webpack

### 21.2 モジュールを値からインポートする
- `import <名前>`の<名前>は自由
```
  import nameA from './format'

  function foo(num) {
    return nameA(num);  // 値は同じ
  }

  import nameB from './format'

  function foo(num) {
    return nameB(num);  // 値は同じ
  }
```
- 構文：`import { currency, number } from './format'`
```
  import { currency, number } from './format'
  import { currency as othername } from './format'
  import * from './format'  // 全てのモジュールをインポート <= 使用しない方がよい
```

### 21.3 インポートされた値はどのような仕組みでバインドされるのか
- インポートは読み取り専用
```
  import ajax from './ajax';
  ajax = 1; // エラー
```
- エクスポート元の変数が変わる場合は、値が変わる
```
  export let title = 'Java'
  export function setTitle(newTitle) {
    title = newTitle;
  }

  console.log(title); // "Java"
  setTitle('Script');
  console.log(title); // "Script"
```

### 21.4 副作用を目的としたインポート
- Google Analytics
```
  import './google_analytics';  // 他のコードより先に実行される
```

### 21.5 モジュールの分割と構成
- format.js
  - formatTime
  - formatDate
  - formatNumber
  - formatCurrency  
  ※上記を2つに分割したい => 他のコードはリファクタリングしたくない
- formatフォルダ
  - index.js
  ```
    import { formatTime, formatDate } from './date';
    import { formatNumber, formatCurrency } from './number';

    export { formatTime, formatDate, formatNumber, formatCurrency };
  ```
  - date.js
    - formatTime
    - formatDate
  - number.js
    - formatNumber
    - formatCurrency
- index.jsの書き方：以下でもOK
```
  export { formatTime, formatDate } from './date';
  export { formatNumber, formatCurrency } from './number';
```
```
  export * from './date';     // 新規追加時にもメンテ不要
  export * from './number';
```

## LESSON 22 ハングマンゲーム

# UNIT 5 イテラブル
## LESSON 23 イテラブル
- ES2015新プロトコル
  - iterabule
  - iterator

### 23.1 イテラブルとは何か
- iterableプロトコル
  - String、Array、Set、Map
  - for..of文、スプレッド演算子

### 23.2 for..of文
```
  for (const item of myArray) { // myArray.lengthの回数まで
    /* itemを使った処理 */
  }
```
- forEach比較
  - 任意のイテラブルで使用でき、パフォーマンスが良い
  - breakで抜け出せる
  - yieldが使える

### 23.3 スプレッド演算子
- (...変数)：イテラブルに含まれるすべてのアイテムが個別に渡されたように処理できる
```
  const letters = ["a", "a", "b", "", "c"];
  findDuplicate(...letters);    // [ 'a', 'c' ]
```
- pushの代用
```
  function addItemCart(item) {
    const newCart = cart.slice(0);
    newCart.push(item);
    return newCart;
  }

  const addItemCart = item => [ ...cart, item ];
```

### 23.4 イテレータ：イテラブルの内部を調べる
- イテラブル：@@iteratorプロパティ＋iteratorプロトコルに従うもの
  - nextメソッドもイテラブル
  - value、doneプロパティを持つ

## LESSON 24 Set
### 24.1 Setを作成する
- Set：データの一意なコレクション
```
  const mySet = new Set();
  const mySet = new Set("abcde"); // Set { 'a', 'b', 'c', 'd', 'e' }
  const mySet = new Set(["abcde"]); // Set { 'abcde' }
  const mySet = new Set(["abcdebcd"]); // Set { 'a', 'b', 'c', 'd', 'e' }
  const mySet = new Set(36); // TypeError
  const mySet = new Set([36]); // Set { 36 }
```

### 24.2 Setを使用する
- 値に基づく追加、存在チェック、削除 => Set
- その他 => 配列
- メソッド
  - `Set.prototype.has`：有無
  - `Set.prototype.add`：追加
  - `Set.prototype.delete`：削除
  - `Set.prototype.size`：長さ
  - `Set.prototype.clear`：空にする

### 24.3 WeakSetはどうなるか
- WeakSet：オブジェクトへの弱参照を保持する特殊なSet

## LESSON 25 Map
### 25.1 Mapを作成する
- Msp：キーは型を限定しない
```
  const myMap = new Map();
  const myMap = new Map(["Key", "Value"], [3, "Number"], [/\S/g, "Regexp"]);
  const myMap = new Map(Object.keys(myObj).map(key => [ key, myObj[key] ])); 
```

### 25.2 Mapを使用する
- `Map.prototype.keys`：`[Map Iterator] { "key", 3, /\S/g }`
- メソッド
  - `[...Map.prototype.keys]`：キー`{ "key", 3, /\S/g }`
  - `[...Map.prototype.Value]`：値
  - `[...Map.prototype.clear]`：全削除
  - `[...Map.prototype.delete]`：指定削除
  - `[...Map.prototype.entries]`：全てのキーと値
  - `[...Map.prototype.forEach]`：反復処理
  - `[...Map.prototype.has]`：指定されたキーがあるか
  - `[...Map.prototype.set]`：キーと値の追加
  - `[...Map.prototype.get]`：キーに対応した値

### 25.3 Mapを使用するのはどのようなときか
- キーと値を反復処理＋キーの順序が重要な場合：Mapを使用

### 25.4 WeakMap
- キーへの弱参照を保持する、Mapの反復不能なサブセット

## LESSON 26 ブラックジャック

# UNIT 6 クラス
## LESSON 27 クラス
### 27.1 クラスを宣言する
- メソッド：簡略表記
- 複数メソッド：`,`で区切らない
```
  class DataStore {
    fetch() {
      /* 取得 */
    }
    update() {
      /* 更新 */
    }
  }
```

### 27.2 クラスをインスタンス化する
```
  const dataStore = new DataStore();
  const userStore = new UserStore('arg'); // 引数も使える
```

### 27.3 クラスをエクスポートする
```
  export default class DataStore {
    /* class本体 */
  }

  import Store from './data_store';
```
```
  export class DataStore {
    /* class本体 */
  }

  import { DataStore } from './data_store';
```

### 27.4 クラスのメソッドはバインドされない
- クラスのメソッドは自動的にバインドされない
```
  class DataStore {
    fetch() {
      /* ajaxを呼び出し、コールバックとしてthis.handleNewRecordsを指定 */
      ajax(this.url, this.handleNewRecords);
    }
    handleNewRecords(records) { // このメソッドはバインドされない
      this.records = records;
    }
  }
```
```
  class DataStore {
    fetch() {
      /* ajaxを呼び出し、コールバックとしてthis.handleNewRecordsを指定 */
      ajax(this.url, records => this.handleNewRecords(records) ); // アロー関数で指定
    }
    handleNewRecords(records) {
      this.records = records;
    }
  }
```

### 27.5 クラス定義でインスタンスプロパティを設定する
```
class DataStore {
  url = '/data/resources';
  records = [];
  
  fetch() {
    ajax(this.url, records => this.records = records);
  }
}

console.log(DataStore.url);     // undefined

const store = new DataStore();  
console.log(store.url);         // /data/resources
console.log(store.records.length);
```

### 27.6 静的プロパティ
- インスタンス間で変化しないプロパティ
```
  class DataStore {
    static domain = 'https://example.com'
    static url(path) {
      return '${this.domain}${path}';
    }

    constructor(resource) {
      this.url = DataStore.url(resource);
    }
  }

  const userStore = new DataStore('/users');
  console.log(userStore.url); // "https://example.com/user"
```

## LESSON 28 クラスの拡張
### 28.1 extendsキーワード
- extends
```
  class TeamStore extends DataStore {
    addUserToTeam(teamId, userId) {
      /* ユーザーをチームに追加 */
    }
    removeUserToTeam(teamId, userId) {
      /* ユーザーをチームから削除 */
    }
  }
```
- 継承
```
  TeamStoreインスタンス => TeamStoreプロトタイプ => DataStoreプロトタイプ => Objectプロトタイプ => Null
```

### 28.2 superキーワード
- スーパークラスのメソッドを呼び出す
```
  class DateStore {
    constructor(resource) {
      /* */
    }
  }

  class TeamStore extends DataStore {
    constructor() {
      super('/teams');
    }
  }
```

### 28.3 クラスを拡張するときの一般的な注意点
- コンストラクタの中でインスタンスにバインドする
```
  class DataStore {
    constructor(resource) {
      /* */
      this.handleNewRecords = this.handleNewRecords.bind(this);
    }
    handleNewrecords(records) {
      /* */
    }
  }

  class ProductStore extends DataStore {
    handleNewRecords(records) {
      super.handleNewRecords(records);
      /* */
    }
  }
```

## LESSON 29 Comets

# UNIT 6 非同期処理
## LESSON 30 プロミス
### 30.1 プロミスを使用する
- プロミス：then => 最終的な値を表すオブジェクト
```
  function getOrgs(username) {
    return axios.get('https://api.github.com/users/${username}/orgs');
  }

  getOrgs('jisaacks').then(resp => {
    const orgs = resp.data;
    /* orgsを使った処理 */
  })
```
- コールバック
```
  function getOrgs(username, callback) {
    return axios.get('https://api.github.com/users/${username}/orgs', callback);
  }

  getOrgs('jisaacks').then(resp => {
    const orgs = resp.data;
    /* orgsを使った処理 */
  })
```

### 30.2 エラー処理
```
  function handleResp(resp) {
    /* 成功のレスポンス */
  }
  function handleError(err) {
    /* エラー処理 */
  }

  const url = 'http://api.github.com/users/jisaacks/orgs';
  axios.get(url).then(handleResp, handleError);
```

### 30.3 プロミスヘルパー
- Promise.resolve() ：既に解決されているプロミスを返す
- Promise.reject()  ：結果として得られるプロミスが拒否状態になる
- Promise.all()     ：全てのプロミスが解決されるのを待ち、配列を渡す
- Promise.race()    ：全てのプロミスのうち、1つでも解決されたら値を提供する

## LESSON 31 高度なプロミス
### 31.1 プロミスを作成する
- `new Promise(fn)`
  - プロミスを解決する関数
  - プロミスを拒否する関数
```
  function fetchImage(src) {
    return new Promise((re, rej) => {
      const img = new Image();
      img.onload = res;
      img.onerror = rej;
      img.src = src;
    });
  }

  fetchImage("https://www.fillmurray.com/200/300").then(
    () => console.log('image loaded'),
    () => console.log('image failed')
  );
```

### 31.2 入れ子のプロミス
```
  Promise.resolve(1)
    .then(number => number + 1) // 1 + 1 = 2
    .then(number => number + 1) // 2 + 1 = 3
    .then(number => number + 1) // 3 + 1 = 4
    .then(number => {
      console.log(number)       // 4
    });
```

### 31.3 エラーをキャッチする
- catch：エラー、拒否をキャッチする
- catch(errCatcher)メソッド＝then(null, errCatcher)メソッドの簡略表記
```
  Promise.resolve()
  .then(() => console.log('A'))
  .then(() => Promise.reject('X'))
  .then(() => console.log('B'))
  .catch(err => console.log('caught:', err))
  .then(() => console.log('C'));
```

## LESSON 32 非同期関数
### 32.1 ジェネレータを使って非同期コードを記述する
- runner関数
```
  const runner = gen => (...args) => {
    const generator = gen(...args);
    return new Promise((resolve, reject) => {
      const run = prev => {
        const { value, done } = generator.next(prev);
        if (done) {
          resolve(value);
        } else if (value instanceof Promise) {
          value.then(run, reject);
        } else {
          run(value);
        }
      }
      run();
    });
  }

  const fetchImageAsync = runner(function* fetchImage(url) {
    const resp = yield fetch(url);
    const blob = yield resp.blob();
    const image = yield createImageBitmap(blob);

    return image;
  });

  fetchImageAsync('my-image.png').then(image => {
    // do something with image
  });
```

### 32.2 async関数
- async関数：ジェネレータ＋プロミスのsyntax sugar（糖衣構文）
```
  async function fetchImage(url) {
    const resp = await fetch(url);
    const blob = await resp.blob();
    return createImageBitmap(blob);
  };

  fetchImage('my-image.png').then(image => {
    // do something with image
  });
```

### 32.3 async関数でのエラー処理
- awaitしているプロミスが拒否された場合
```
  async function fail() {
    const msg = await Promise.reject('I failed.');
    return 'I Succeeded.';
  };

  fail().then(msg => console.log(msg), msg => console.log(msg))
```

## LESSON 33 オブザーバブル
### 33.1 オブザーバブルを作成する
```
  const currentTime$ = new Observable(observer => {
    const interval = setInterval(() => {
      const currentTime = new Date();
      observer.next(currentTime);
    }, 1000);

    return () => clearInterval(interval);
  });

  const currentTimeSubscription = currentTime$.subscribe({
    next(currentTime) {
      // show current time
    }
  });
```
- start：1回限りの通知
- next：ディスパッチするデータを送信するメソッド
- error：エラー送信
- complete：完了、1回限りの通知

### 33.2 オブザーバブルを合成する
```
  currentTime$.map(data => `${date.getHours()}:${date.getMinutes()}`).distinct();
```

### 33.3 オブザーバブルのコンビネータを作成する
```
  function filter (obs$, fn) {
    return new Observable(observer => obs$.subscribe({
      next(val) {
        if (fn(val)) observer.next(val);
      }
    }));
  }

  filter(Observable.of(1, 2, 3, 4, 5), n => n % 2).subscribe({
    next(val) {
      console.log('filtered: ', val);
    }
  });
```

## LESSON 34 キャンバスイメージギャラリー
