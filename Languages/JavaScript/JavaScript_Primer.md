# JavaScript Primer
## はじめに
### 著者紹介
### 読み始める前の事前準備
### 文章の間違いに気づいたら
### 補足
- リファレンス：[JavaScript リファレンス](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference)
- [JavaScript Promiseの本](https://azu.github.io/promises-book/)
- [json.orgの日本語ドキュメント](https://www.json.org/json-ja.html)
---
## 第一部：基本文法
### JavaScriptとは
- JavaScriptとECMAScript
- strict mode：厳格モード => 古い文法などはエラー

### コメント
- 一行コメント
```
  // 1行コメント
```
- 複数行コメント
```
  /*
    コメント
    コメント
  */
```
- HTML-likeコメント：後方互換性のためだけに存在する
```
  <!-- この行はコメントと認識される
  console.log("この行はJavaScriptのコードとして実行される");
  --> この行はコメントと認識される
```

### 変数と宣言
- const：再代入できない変数を宣言できる
```
  const 変数名 = 初期値;
```
  - 再代入：TypeError
  - 定数ではない
  - オブジェクトも宣言可能
  ```
    // `const`でオブジェクトを定義している
    const object = {
        key: "値"
    };
    // オブジェクトそのものは変更できてしまう
    object.key = "新しい値";
  ```
- let：再代入ができる変数を宣言できる
```
  let 変数名 = 初期値;
```
  - 初期値、代入
  ```
    let count;  // undefined
    count = 0;  // 0
    count = 1;  // 1
  ```
- var：再代入ができる変数を宣言できるが、いくつかの問題が知られている
```
  let 変数名 = 初期値;
```
  - const、letで置き換えられるため、基本的に使わない
  - 問題点
    - varは同じ名前の変数を再定義できてしまう（const、letはエラー）
    - 巻き上げ
- 変数の名前（識別子）には利用できる名前のルールがある
  - 半角のアルファベット、_（アンダースコア）、$（ダラー）、数字を組み合わせた名前にする
  - 変数名は数字から開始できない
  - 予約語と被る名前は利用できない

### 値の評価と表示
### データ型とリテラル
- 動的型付け言語：変数の型はない
- データ型
  - プリミティブ型（基本型）：一度作成したらその値自体を変更できないというイミュータブル（immutable）の特性を持つ
    - 真偽値（Boolean）: trueまたはfalseのデータ型
    - 数値（Number）: 42 や 3.14159 などの数値のデータ型
    - 巨大な整数（BigInt）: ES2020から追加された9007199254740992nなどの任意精度の整数のデータ型
    - 文字列（String）: "JavaScript" などの文字列のデータ型
    - undefined: 値が未定義であることを意味するデータ型
    - null: 値が存在しないことを意味するデータ型
    - シンボル（Symbol）: ES2015から追加された一意で不変な値のデータ型
  - オブジェクト（複合型）：一度作成した後もその値自体を変更できるためミュータブル（mutable）の特性を持つ
    - プリミティブ型以外のデータ
    - オブジェクト、配列、関数、正規表現、Dateなど
```
  console.log(typeof 値);// => "データ型"
  console.log(typeof true);// => "boolean"
  console.log(typeof null); // => "object"  // 歴史的バグ
  console.log(typeof ["配列"]); // => "object"   // プリミティブかオブジェクトを表示
  console.log(typeof { "key": "value" }); // => "object"
```
- リテラル  
プリミティブ型の値や一部のオブジェクトは、リテラルを使うことで簡単に定義できる  
リテラルとはプログラム上で数値や文字列など、データ型の値を直接記述できるように構文として定義されたもの
  - プリミティブ型
    - 真偽値
    ```
      true; // => true
      false; // => false
    ```
    - 数値
      - 整数リテラル
        - 10進数: 数字の組み合わせ
        - 2進数: 0b（または0B）の後ろに、0または1の数字の組み合わせ
        - 8進数: 0o（または0O）の後ろに、0から7までの数字の組み合わせ
        - 16進数: 0x（または0X）の後ろに、0から9までの数字とaからfまたはAからFのアルファベットの組み合わせ
      - 浮動小数点数リテラル
        - 3.14159 のような .（ドット）を含んだ数値
        - 2e8 のような e または E を含んだ数値
      - [ES2020] BigInt
        - 数値リテラルはIEEE 754の倍精度浮動小数である
        - 正確に扱える数値の最大値は2^53-1（2の53乗から1引いた値） => 9007199254740991
        - BigIntリテラルは、数値の後ろにnをつける => `9007199254740992n`
      - [ES2021] Numeric Separators
      ```
        1000000000000;
        1_000_000_000_000;
      ```
    - 文字列
    ```
      console.log("文字列"); // => "文字列"
      console.log('文字列'); // => "文字列"
      console.log(`文字列`); // => "文字列"
    ```
      - ダブルクォートとシングルクォートは同じ
      - 文字列の中に同じ記号が出現した場合、`\`（バックスラッシュ）を使ってエスケープ
      - 文字列内部に出現しない別のクォート記号を使うことで、エスケープをせずに書くことも可
      - 改行：`"複数行の\n文字列を\n入れたい";`
      - [ES2015] テンプレートリテラル
        - 複数行  
        ```
          `複数行の
          文字列を
          入れたい`; // => "複数行の\n文字列を\n入れたい"
        ```
        - 変数
        ```
          const str = "文字列";
          console.log(`これは${str}です`); // => "これは文字列です"
        ```
    - null  
    nullリテラルはnull値を返すリテラル => nullは「値がない」ということを表現する値
    ```
      const foo = null;
      console.log(foo); // => null
    ```
  - undefinedはリテラルではない：グローバル変数で値がundefined
  - オブジェクト
    - オブジェクト
    ```
      const obj = {}; // 中身が空のオブジェクトを作成
    ```
    ```
      const obj = {
        "key": "value"
      };
      // ドット記法
      console.log(obj.key); // => "value"
      // ブラケット記法
      console.log(obj["key"]); // => "value"
    ```
    - 配列
    ```
      const emptyArray = []; // 空の配列を作成
      const array = [1, 2, 3]; // 値を持った配列を作成
    ```
    - 正規表現
    ```
      const numberRegExp = /\d+/; // 1文字以上の数字にマッチする正規表現
      // `numberRegExp`の正規表現が文字列"123"にマッチするかをテストする
      console.log(numberRegExp.test("123")); // => true
    ```

### 演算子
- 二項演算子
```
  // 二項演算子とオペランドの関係
  左オペランド 演算子 右オペランド
```
  - `+`：プラス演算子
  - `+`：文字列結合演算子
  - `-`：マイナス演算子
  - `*`：乗算演算子
  - `/`：除算演算子
  - `%`：剰余演算子
  - `**`：[ES2016] べき乗演算子
- 単項演算子
```
  let num = 1;
  num++;
  // または
  ++num;
```
  - `+`：単項プラス演算子 => オペランドを数値に変換する  
  単項プラス演算子は文字列から数値への変換に使うべきではない
  - `-`：単項マイナス演算子 => マイナスの数値を記述する場合に利用
  - `++`：インクリメント演算子 => オペランドの数値を+1する演算子
  ```
    let x = 1;
    console.log(x++); // => 1
    console.log(x);   // => 2
  ```
  ```
    let x = 1;
    console.log(++x); // => 2
    console.log(x);   // => 2
  ```
  - `--`：デクリメント演算子 => オペランドの数値を-1する演算子
- 比較演算子
  - `===`：厳密等価演算子 => 同じ型で同じ値である場合に、trueを返す
  - `!==`：厳密不等価演算子 => 異なる型または異なる値である場合に、trueを返す
  - `==`：等価演算子 => 同じ型となるように暗黙的な型変換をしてから比較（※利用しない）
  - `!=`：不等価演算子 => 同じ型となるように暗黙的な型変換をしてから比較（※利用しない）
  - `>`：大なり演算子/より大きい
  - `>=`：大なりイコール演算子/以上
  - `<`：小なり演算子/より小さい
  - `<=`：小なりイコール演算子/以下
- ビット演算子  
オペランドである数値を符号付き32ビット整数（0と1からなる32個のビットの集合）として扱う
  - `&`：ビット論理積 => ビットごとのAND演算した結果を返す
  - `|`：ビット論理和 => ビットごとのOR演算した結果を返す
  - `^`：ビット排他的論理和 => ビットごとのXOR演算した結果を返す
  - `~`：ビット否定 => 各ビットを反転した値を返す
  - `<<`：左シフト演算子 => 数値であるnumをbitの数だけ左へシフトする
  - `>>`：右シフト演算子 => 数値であるnumをbitの数だけ右へシフトする（符号は維持）
  - `>>>`：ゼロ埋め右シフト演算子 => 数値であるnumをbitの数だけ右へシフトする（左端のビットは0：常に正）
- `=`：代入演算子
  - [ES2015] 分割代入（Destructuring assignment）：配列やオブジェクトの値を複数の変数へ同時に代入できる
- 論理演算子
  - `&&`：AND演算子
  - `OR`：OR演算子
  - `!`：NOT演算子
  - `??`：[ES2020] Nullish coalescing演算子 => 左辺の値がnullishであるならば、右辺の評価結果を返す  
  nullish => null, undefined  
  デフォルト値設定に使われる
- `?`と`:`：条件（三項）演算子
```
  条件式 ? Trueのとき処理する式 : Falseのとき処理する式;
```
- `(`と`)`：グループ化演算子
- `,`：カンマ演算子 => カンマ（,）で区切った式を左から順に評価し、 最後の式の評価結果を返す
```
  式1, 式2, 式3;
```

### 暗黙的な型変換
- `===`：厳密等価演算子を使え
- `==`：等価演算子は使わない
- 他の演算子でも暗黙的な型変換が起るので、書き方に注意する
- 明示的な型変換を使う
```
  // `===`では、異なるデータ型の比較結果はfalse
  console.log(1 === "1"); // => false

  // `==`では、異なるデータ型は暗黙的な型変換をしてから比較される
  // 暗黙的な型変換によって 1 == 1 のように変換されてから比較される
  console.log(1 == "1"); // => true
```
```
  // 暗黙的な型変換が行われ、数値の加算として計算される
  1 + true; // => 2
  // 次のように暗黙的に変換されてから計算される
  1 + 1; // => 2
```

### 明示的な型変換
- 任意の値 → 真偽値
  - falsyな値はfalseになる
    - false, undefined, null, 0, 0n, NaN, ""
  - falsyでない値はtrueになる
```
  Boolean("string"); // => true
  Boolean(1); // => true
  Boolean({}); // => true
  Boolean(0); // => false
  Boolean(""); // => false
  Boolean(null); // => false
```
- 数値 → 文字列
```
  String("str"); // => "str"
  String(true); // => "true"
  String(null); // => "null"
  String(undefined); // => "undefined"
  String(Symbol("シンボルの説明文")); // => "Symbol(シンボルの説明文)"
  // プリミティブ型ではない値の場合
  String([1, 2, 3]); // => "1,2,3"
  String({ key: "value" }); // => "[object Object]"
  String(function() {}); // "function() {}"
```
- シンボル → 文字列
```
  "文字列と" + Symbol("シンボルの説明"); // => TypeError: can't convert symbol to string
  "文字列と" + String(Symbol("シンボルの説明")); // => "文字列とSymbol(シンボルの説明)"
```
- 文字列 → 数値
```
  console.log(Number(input));
```
```
  // "1"をパースして10進数として取り出す
  console.log(Number.parseInt("1", 10)); // => 1
  // 余計な文字は無視してパースした結果を返す
  console.log(Number.parseInt("42px", 10)); // => 42
  console.log(Number.parseInt("10.5", 10)); // => 10
  // 文字列をパースして浮動小数点数として取り出す
  console.log(Number.parseFloat("1")); // => 1
  console.log(Number.parseFloat("42.5px")); // => 42.5
  console.log(Number.parseFloat("10.5")); // => 10.5
```
```
  // 数字ではないため、数値へは変換できない
  Number("文字列"); // => NaN
  // 未定義の値はNaNになる
  Number(undefined); // => NaN
```
- NaNはNot a NumberだけどNumber型  
NaNはNot a Numberの略称で、特殊な性質を持つNumber型のデータ
```
  Number.isNaN(NaN); // => true
```
```
  function sum(...values) {
      return values.reduce((total, value) => {
          // `value`をNumberで明示的に数値へ変換してから加算する
          return total + Number(value);
      }, 0);
  }
  const x = 1, z = 10;
  let y; // `y`はundefined
  console.log(sum(x, y, z)); // => NaN
```
  - sum関数側（呼ばれる側）で、Number型の値以外を受けつけなくする
  - sum関数を呼び出す側で、Number型の値のみを渡すようにする
- 明示的な変換でも解決しないこと
  - 空文字列かどうかを判定する

### 関数と宣言
- 関数宣言：functionからはじまる文は関数宣言
```
  // 関数宣言
  function 関数名(仮引数1, 仮引数2) {
      // 関数が呼び出されたときの処理
      // ...
      return 関数の返り値;
  }
  // 関数呼び出し
  const 関数の結果 = 関数名(引数1, 引数2);
  console.log(関数の結果); // => 関数の返り値
```
  - 関数名 - 利用できる名前は変数名と同じ（「変数名に使える名前のルール」を参照）
  - 仮引数 - 関数の呼び出し時に渡された値が入る変数。複数ある場合は,（カンマ）で区切る
  - 関数の中身 - {と}で囲んだ関数の処理を書く場所
  - 関数の返り値 - 関数を呼び出したときに、呼び出し元へ返される値
- 関数の引数
  - 呼び出し時の引数が少ないとき  
  呼び出し時の引数が少ない場合、余った仮引数にはundefinedという値が代入
  ```
    function echo(x) {
        return x;
    }

    console.log(echo(1)); // => 1
    console.log(echo()); // => undefined
  ```
  - [ES2015] デフォルト引数
  ```
    function 関数名(仮引数1 = デフォルト値1, 仮引数2 = デフォルト値2) {

    }
  ```
  - 呼び出し時の引数が多いとき  
  引数の個数が多い場合、あふれた引数は単純に無視され
  ```
    function add(x, y) {
        return x + y;
    }
    add(1, 3); // => 4
    add(1, 3, 5); // => 4
  ```
- 可変長引数  
  引数の数が固定ではなく、任意の個数の引数を受け取りたい場合
  `Math.max(...args)`
  - [ES2015] Rest parameters  
  仮引数名の前に...をつけた仮引数のことで、残余引数とも呼ばれる => 関数に渡された値が配列として代入
  ```
    function fn(...args) {
        // argsは引数の値が順番に入った配列
        console.log(args); // => ["a", "b", "c"]
    }
    fn("a", "b", "c");
  ```
- [ES2015] 関数の引数と分割代入  
  分割代入はオブジェクトや配列からプロパティを取り出し、変数として定義し直す構文
  ```
    function printUserId(user) {
        console.log(user.id); // => 42
    }
    const user = {
        id: 42
    };
    printUserId(user);
  ```
- 関数はオブジェクト
  ```
    function fn() {
        console.log("fnが呼び出されました");
    }
    // 関数`fn`を`myFunc`変数に代入している
    const myFunc = fn;
    myFunc();
  ```
  - 関数式  
  関数を値として変数へ代入している式
  ```
    // 関数式
    const 関数名 = function() {
        // 関数を呼び出したときの処理
        // ...
        return 関数の返り値;
    };
  ```
  - 匿名関数（または無名関数）  
  関数式では、名前を持たない関数を変数に代入できる
  ```
    // 関数式は変数名で参照できるため、"関数名"を省略できる
    const 変数名 = function() {
    };
    // 関数宣言では"関数名"は省略できない
    function 関数名() {
    }
  ```
  - [ES2015] Arrow Function  
  ```
    // Arrow Functionを使った関数定義
    const 関数名 = () => {
        // 関数を呼び出したときの処理
        // ...
        return 関数の返す値;
    };
  ```
    - 関数の仮引数が1つのときは()を省略できる
    - 関数の処理が1つの式である場合に、ブロックとreturn文を省略できる
    ```
      // 仮引数の数と定義
      const fnA = () => { /* 仮引数がないとき */ };
      const fnB = (x) => { /* 仮引数が1つのみのとき */ };
      const fnC = x => { /* 仮引数が1つのみのときは()を省略可能 */ };
      const fnD = (x, y) => { /* 仮引数が複数のとき */ };
      // 値の返し方
      // 次の２つの定義は同じ意味となる
      const mulA = x => { return x * x; }; // ブロックの中でreturn
      const mulB = x => x * x;            // 1行のみの場合はreturnとブロックを省略できる
    ```
      - 名前をつけることができない（常に匿名関数）
      - thisが静的に決定できる（詳細は「関数とスコープ」の章で解説します）
      - functionキーワードに比べて短く書くことができる
      - newできない（コンストラクタ関数ではない）
      - arguments変数を参照できない
- 同じ名前の関数宣言は上書きされる
```
  function fn(x) {
      return `最初の関数 x: ${x}`;
  }
  function fn(x, y) {
      return `最後の関数 x: ${x}, y: ${y}`;
  }
  console.log(fn(2, 10)); // => "最後の関数 x: 2, y: 10"
```
  - コールバック関数
  ```
    function 高階関数(コールバック関数) {
        コールバック関数();
    }
  ```
- メソッド  
このobj.method1プロパティとobj.method2プロパティがメソッド
```
  const obj = {
      method1: function() {
          // `function`キーワードでのメソッド
      },
      method2: () => {
          // Arrow Functionでのメソッド
      }
  };
```
  - [ES2015] メソッドの短縮記法  
  ```
    const obj = {
        method() {
            return "this is method";
        }
    };
    console.log(obj.method()); // => "this is method"
  ```

### 文と式  
JavaScriptは、文（Statement）と式（Expression）から構成されている
- 式  
値を生成し、変数に代入できるもの
```
  // 1という式の評価値を表示
  console.log(1); // => 1
  // 1 + 1という式の評価値を表示
  console.log(1 + 1); // => 2
  // 式の評価値を変数に代入
  const total = 1 + 1;
  // 関数式の評価値(関数オブジェクト)を変数に代入
  const fn = function() {
      return 1;
  };
  // fn() という式の評価値を表示
  console.log(fn()); // => 1
```
- 文  
処理する1ステップが1つの文
```
  処理する文;
  処理する文;
  処理する文;
```
- 式文  
文となった式のことを式文と呼ぶ
```
  // 式文であるためセミコロンをつけている
  式;
```
- ブロック文  
文を{と}で囲んだ部分をブロックと言う
```
  {
      文;
      文;
  }
```
- function宣言（文）とfunction式
```
  // learn関数を宣言する関数宣言文
  function learn() {
  }
  // 関数式をread変数へ代入
  const read = function() {
  };
```

### 条件分岐
- if文
```
  if (条件式) {
      実行する文;
  }
```
  - 1行のみの場合は`{}`の省略も可能だが、省略しない（可読性）
  - falsy：false, undefined, null, 0, 0n, NaN, ""（空文字列）
- else if文
```
  const version = "ES6";
  if (version === "ES5") {
      console.log("ECMAScript 5");
  } else if (version === "ES6") {
      console.log("ECMAScript 2015");
  } else if (version === "ES7") {
      console.log("ECMAScript 2016");
  }
```
- else文
```
  const num = 1;
  if (num > 10) {
      console.log(`numは10より大きいです: ${num}`);
  } else {
      console.log(`numは10以下です: ${num}`);
  }
```
- ネストしたif文
```
  if (条件式A) {
      if (条件式B) {
          // 条件式Aと条件式Bがtrueならば実行される文
      }
  }
```
  - ネストは浅い方が可読性が良い
- switch文  
式の評価結果が指定した値である場合に行う処理を並べて書く
```
  switch (式) {
      case ラベル1:
          // `式`の評価結果が`ラベル1`と一致する場合に実行する文
          break;
      case ラベル2:
          // `式`の評価結果が`ラベル2`と一致する場合に実行する文
          break;
      default:
          // どのcaseにも該当しない場合の処理
          break;
  }
  // break; 後はここから実行される
```
  - break;を書き忘れると、他のcase文が実行されてしまう
  - switch文は関数と組み合わせて条件に対する値を返すパターンとして使うことが多い
  ```
    function getECMAScriptName(version) {
        switch (version) {
            case "ES5":
                return "ECMAScript 5";
            case "ES6":
                return "ECMAScript 2015";
            case "ES7":
                return "ECMAScript 2016";
            default:
                return "しらないバージョンです";
        }
    }
    // 関数を実行して`return`された値を得る
    getECMAScriptName("ES6"); // => "ECMAScript 2015"
  ```

### ループと反復処理
- while文  
条件式がtrueであるならば、反復処理を行う
```
  while (条件式) {
      実行する文;
  }
```
  - 1.条件式 の評価結果がtrueなら次のステップへ、falseなら終了
  - 2.実行する文を実行
  - 3.ステップ1へ戻る
- do-while文  
while文とほとんど同じですが実行順序が異なる
```
  do {
      実行する文;
  } while (条件式);
```
  - 1.実行する文を実行
  - 2.条件式 の評価結果がtrueなら次のステップへ、falseなら終了
  - 3.ステップ1へ戻る
- for文  
繰り返す範囲を指定した反復処理を書く
```
  for (初期化式; 条件式; 増分式) {
      実行する文;
  }
```
  - 1.初期化式 で変数の宣言
  - 2.条件式 の評価結果がtrueなら次のステップへ、falseなら終了
  - 3.実行する文 を実行
  - 4.増分式 で変数を更新
  - 5.ステップ2へ戻る
- 配列のforEachメソッド  
for文と同じように反復処理を行うメソッド  
for文の条件式に相当するものはなく、必ず配列のすべての要素を反復処理する
```
  const array = [1, 2, 3];
  array.forEach(currentValue => {
      console.log(currentValue);
  });
```
  - コールバック関数：引数として渡されるその場で作った匿名関数（名前のない関数）  
  currentValueがコールバック関数
  - 高階関数：コールバック関数を引数として受け取る関数やメソッドのこと  
- break文  
処理中の文から抜けて次の文へ移行する制御文：while、do-while、for
```
  while (true) {
      break; // *1 へ
  }
  // *1 次の文
```
  - return文は現在の関数を終了させることができるので上手く使えば、break;を無くせる
- 配列のsomeメソッド  
配列の各要素をテストする処理をコールバック関数として受け取る  
コールバック関数が、一度でもtrueを返した時点で反復処理を終了し、someメソッドはtrueを返す
```
  const array = [1, 2, 3, 4, 5];
  const isPassed = array.some(currentValue => {
      // テストをパスするとtrue、そうでないならfalseを返す
  });
```
- continue文  
continue文は現在の反復処理を終了して、次の反復処理を行う：while、do-while、for
```
  while (条件式) {
      // 実行される処理
      continue; // `条件式` へ
      // これ以降の行は実行されません
  }
```
- 配列のfilterメソッド  
配列から特定の値だけを集めた新しい配列を作るにはfilterメソッドを利用できる
```
  const array = [1, 2, 3, 4, 5];
  // テストをパスしたものを集めた配列
  const filteredArray = array.filter((currentValue, index, array) => {
      // テストをパスするならtrue、そうでないならfalseを返す
  });
```
- for...in文（利用は避ける）  
for...in文はオブジェクトのプロパティに対して、反復処理を行う
```
  for (プロパティ in オブジェクト) {
      実行する文;
  }
```
  - 親オブジェクトまで列挙可能なものがあるかを探索して列挙
  - オブジェクト自身が持っていないプロパティも列挙
  - 意図しない動作に注意
  - Object.keysメソッド、Object.valuesメソッド、Object.entriesメソッドなどが利用可能
  ```
    const obj = {
        "a": 1,
        "b": 2,
        "c": 3
    };
    Object.keys(obj).forEach(key => {
        const value = obj[key];
        console.log(`key:${key}, value:${value}`);
    });
  ```
  - 配列もプロパティ名が列挙 => 意図しない動作
- [ES2015] for...of文  
iterableオブジェクトは、for...of文で反復処理できる
```
  for (variable of iterable) {
      実行する文;
  }
```
  - ArrayもStringもiterableオブジェクト
  ```
    const array = [1, 2, 3];
    for (const value of array) {
        console.log(value);
    }
  ```
- letではなくconstで反復処理をする  
配列には、反復処理をして新しい値を作るreduceメソッドがある  
```
  const result = array.reduce((前回の値, 現在の値) => {
      return 次の値;
  }, 初期値);
```

### オブジェクト  
オブジェクト：プロパティの集合  
プロパティ：名前（キー）と値（バリュー）が対になったもの  
あらゆるオブジェクトの元となるObjectというビルトインオブジェクトがある
- オブジェクトを作成する  
```
  // プロパティを持たない空のオブジェクトを作成
  const obj = {};
```
```
  // プロパティを持つオブジェクトを定義する
  const obj = {
      // キー: 値
      "key": "value"
  };
```
  - 省略記法
  ```
    const name = "名前";
    // `name`というプロパティ名で`name`の変数を値に設定したオブジェクト
    const obj = {
        name        // name: nameを省略記法で書いた場合[ES2015]
    };
    console.log(obj); // => { name: "名前" }
  ```
- {}はObjectのインスタンスオブジェクト  
new Object()でオブジェクトを作成するのと同じ意味
- プロパティへのアクセス
  - ドット記法（.）
  - ブラケット記法（[]）
  ```
    const obj = {
        key: "value"
    };
    // ドット記法で参照
    console.log(obj.key); // => "value"
    // ブラケット記法で参照
    console.log(obj["key"]); // => "value"
  ```
    - 基本的には簡潔なドット記法（.）を使い、ドット記法で書けない場合はブラケット記法（[]）を使うとよい
- [ES2015] オブジェクトと分割代入  
オブジェクト.プロパティ名を変数として定義して利用も可能
```
    const languages = {
        ja: "日本語",
        en: "英語"
    };
    const ja = languages.ja;          // 代入
    const en = languages.en;          // 代入
    const { ja, en } = languages;     // 分割代入：上2つの代入と同じ意味
    console.log(ja); // => "日本語"
    console.log(en); // => "英語"
```
- プロパティの追加  
一度作成した後もその値自体を変更できるというミュータブル（mutable）の特性を持つ  
作成したいプロパティ名へ値を代入するだけ
```
  // 空のオブジェクト
  const obj = {};
  // `key`プロパティを追加して値を代入
  obj.key = "value";
  obj[key] = "value of key";    // ブラケット記法
  console.log(obj.key); // => "value"
```
  - プロパティを初期化時以外に追加：オブジェクトがどのようなプロパティを持っているかがわかりにくくなる
  - できる限り作成後に新しいプロパティは追加しない
  - オブジェクトの作成時のオブジェクトリテラルの中でプロパティを定義することを推奨
- プロパティの削除  
オブジェクトのプロパティを削除するにはdelete演算子を利用
```
  const obj = {
      key1: "value1",
      key2: "value2"
  };
  // key1プロパティを削除
  delete obj.key1;
  // key1プロパティが削除されている
  console.log(obj); // => { "key2": "value2" }
```
- constで定義したオブジェクトは変更可能
```
  const obj = { key: "value" };
  obj.key = "Hi!"; // constで定義したオブジェクト(`obj`)が変更できる
  console.log(obj.key); // => "Hi!"
```
  - 作成したオブジェクトのプロパティの変更を防止するにはObject.freezeメソッド
  - Object.freezeメソッドを利用する場合は必ずstrict modeと合わせて使う
- プロパティの存在を確認する
```
  const obj = {};
  console.log(obj.notFound); // => undefined
```
  - 存在しないプロパティに対してアクセスした場合に例外ではなくundefinedを返す
  - プロパティ名を間違えた場合に間違いに気づきにくいという問題がある
  - 確認方法
    - undefinedとの比較
    ```
      if (obj.key !== undefined) {}
    ```
    - in演算子
    ```
      "プロパティ名" in オブジェクト; // 存在 = true or false
    ```
    - hasOwnPropertyメソッド
    ```
      const obj = {};
      obj.hasOwnProperty("プロパティ名"); // true or false
    ```
- [ES2020] Optional chaining演算子（?.）  
左辺のオペランドがnullish（nullまたはundefined）の場合、それ以上評価せずにundefinedを返す  
プロパティが存在する場合は、そのプロパティの評価結果を返す
```
  function printWidgetTitle(widget) {
      // 例外を避けるために`widget`のプロパティの存在を順番に確認してから、値を表示している
      if (widget.window !== undefined && widget.window.title !== undefined) {
          console.log(`ウィジェットのタイトルは${widget.window.title}です`);
      } else {
          console.log("ウィジェットのタイトルは未定義です");
      }
  }
```
```
  function printWidgetTitle(widget) {
      const title = widget?.window?.title ?? "未定義";
      console.log(`ウィジェットのタイトルは${title}です`);
  }
```  
※ブラケット記法（[]）と組み合わせることも可能
- toStringメソッド  
オブジェクト自身を文字列化するメソッド  
Stringコンストラクタ関数とtoStringメソッドの結果はどちらも同じ
```
  const obj = { key: "value" };
  console.log(obj.toString()); // => "[object Object]"
  // `String`コンストラクタ関数は`toString`メソッドを呼んでいる
  console.log(String(obj)); // => "[object Object]"
```
- オブジェクトのプロパティ名は文字列化される  
指定したプロパティ名は暗黙的に文字列に変換される
```
  const obj = {};
  const keyObject1 = { a: 1 };
  const keyObject2 = { b: 2 };
  // どちらも同じプロパティ名（"[object Object]"）に代入している
  obj[keyObject1] = "1";
  obj[keyObject2] = "2";
  console.log(obj); //  { "[object Object]": "2" }
```  
唯一の例外として、Symbolだけは文字列化されない
```
  const obj = {};
  // Symbolは例外的に文字列化されず扱える
  const symbolKey1 = Symbol("シンボル1");
  const symbolKey2 = Symbol("シンボル2");
  obj[symbolKey1] = "1";
  obj[symbolKey2] = "2";
  console.log(obj[symbolKey1]); // => "1"
  console.log(obj[symbolKey2]); // => "2"
```
- オブジェクトの静的メソッド（スタティックメソッド）  
静的メソッドはObjectそのものから呼び出せるメソッド
  - オブジェクトの列挙
    - Object.keysメソッド: オブジェクトのプロパティ名の配列にして返す
    - Object.valuesメソッド[ES2017]: オブジェクトの値の配列にして返す
    - Object.entriesメソッド[ES2017]: オブジェクトのプロパティ名と値の配列の配列を返す
    ```
      const obj = {
          "one": 1,
          "two": 2,
          "three": 3
      };
      // `Object.keys`はキーの列挙した配列を返す
      console.log(Object.keys(obj)); // => ["one", "two", "three"]
      // `Object.values`は値を列挙した配列を返す
      console.log(Object.values(obj)); // => [1, 2, 3]
      // `Object.entries`は[キー, 値]の配列を返す
      console.log(Object.entries(obj)); // => [["one", 1], ["two", 2], ["three", 3]]
    ```  
    列挙する静的メソッドと配列のforEachメソッドなどを組み合わせれば、プロパティに対して反復処理が可能  
- オブジェクトのマージと複製  
Object.assignメソッド[ES2015]は、あるオブジェクトを別のオブジェクトに代入（assign）できる
```
  const obj = Object.assign(target, ...sources);
```
  - オブジェクトのマージ
  ```
    const objectA = { a: "a" };
    const objectB = { b: "b" };
    const merged = Object.assign({}, objectA, objectB);
    console.log(merged); // => { a: "a", b: "b" }
  ```
  ```
    const objectA = { a: "a" };
    const objectB = { b: "b" };
    const merged = Object.assign(objectA, objectB);
    console.log(merged); // => { a: "a", b: "b" }
    // `objectA`が変更されている
    console.log(objectA); // => { a: "a", b: "b" }
    console.log(merged === objectA); // => true
  ```
  - [ES2018] オブジェクトのspread構文でのマージ
  ```
    const objectA = { a: "a" };
    const objectB = { b: "b" };
    const merged = {
        ...objectA,
        ...objectB
    };
    console.log(merged); // => { a: "a", b: "b" }
  ```
  - オブジェクトの複製  
  JavaScriptには、オブジェクトを複製する関数は用意されていません
  ```
    // 引数の`obj`を浅く複製したオブジェクトを返す
    const shallowClone = (obj) => {
        return Object.assign({}, obj);
    };
    const obj = { a: "a" };
    const cloneObj = shallowClone(obj);
    console.log(cloneObj); // => { a: "a" }
    // オブジェクトを複製しているので、異なるオブジェクトとなる
    console.log(obj === cloneObj); // => false
  ```  
  ※オブジェクトのプロパティを浅くコピー（shallow copy）する点に注意

### プロトタイプオブジェクト
- Objectはすべての元  
他のオブジェクトはすべてObjectを継承している  
正確には、ほとんどすべてのオブジェクトはObject.prototypeプロパティに定義されたprototypeオブジェクトを継承している  
prototypeオブジェクトとは、すべてのオブジェクトの作成時に自動的に追加される特殊なオブジェクト
- プロトタイプメソッド  
prototypeオブジェクトに組み込まれているメソッド  
Object.prototype.toString = Object#toString
- プロトタイプチェーン  
インスタンスからprototypeオブジェクト上に定義されたメソッドを参照できる仕組み
- プロトタイプメソッドとインスタンスメソッドの優先順位  
プロトタイプメソッドと同じ名前のメソッドがインスタンスオブジェクトに定義されている場合もあります。 その場合には、インスタンスに定義したメソッドが優先して呼び出される
```
  // オブジェクトのインスタンスにtoStringメソッドを定義
  const customObject = {
      toString() {
          return "custom value";
      }
  };
  console.log(customObject.toString()); // => "custom value"
```
- in演算子とObject#hasOwnPropertyメソッドの違い  
```
  const obj = {};
  // `obj`というオブジェクト自体に`toString`メソッドが定義されているわけではない
  console.log(obj.hasOwnProperty("toString")); // => false
  // `in`演算子は指定されたプロパティ名が見つかるまで親をたどるため、`Object.prototype`まで見にいく
  console.log("toString" in obj); // => true
```
  - hasOwnPropertyメソッドは、そのオブジェクト自身が指定したプロパティを持っているかを判定
  - in演算子はオブジェクト自身が持っていなければ、継承元であるprototypeオブジェクトまで探索
- オブジェクトの継承元を明示するObject.createメソッド  
第一引数に指定したprototypeオブジェクトを継承した新しいオブジェクトを作成できる
```
  // const obj = {} と同じ意味
  const obj = Object.create(Object.prototype);
  // `obj`は`Object.prototype`を継承している
  console.log(obj.hasOwnProperty === Object.prototype.hasOwnProperty); // => true
```
- ArrayもObjectを継承している  
Arrayのインスタンス → Array.prototype → Object.prototype
- Object.prototypeを継承しないオブジェクト
```
  // 親がnull、つまり親がいないオブジェクトを作る
  const obj = Object.create(null);
  // Object.prototypeを継承しないため、hasOwnPropertyが存在しない
  console.log(obj.hasOwnProperty); // => undefined
```  
Mapオブジェクトの代わりとして利用されていた
ES2015からは、本物のMapが利用できるため、Object.create(null)をMapの代わりに利用する必要はありません

### 配列  
配列とは値に順序をつけて格納できるオブジェクト
  - 要素：配列に格納したそれぞれの値
  - インデックス：それぞれの要素の位置
  - 配列は可変長：配列を作成後に配列へ要素を追加したり、配列から要素を削除できる
- 配列の作成とアクセス
  - 配列の作成
  ```
    const emptyArray = [];
    const numbers = [1, 2, 3];
    // 2次元配列（配列の配列）
    const matrix = [
        ["a", "b"],
        ["c", "d"]
    ];
  ```
  - 配列の読み取り
  ```
    const array = ["one", "two", "three"];
    console.log(array[0]); // => "one"
  ```
  ```
    // 2次元配列（配列の配列）
    const matrix = [
        ["a", "b"],
        ["c", "d"]
    ];
    console.log(matrix[0][0]); // => "a"
  ```
  - 配列の最後
  ```
    const array = ["one", "two", "three"];
    console.log(array.length); // => 3
    // 配列の要素数 - 1 が 最後の要素のインデックスとなる
    console.log(array[array.length - 1]); // => "three"
  ```
  - 存在しないプロパティ
  ```
    const array = ["one", "two", "three"];
    // `array`にはインデックスが100の要素は定義されていない
    console.log(array[100]); // => undefined
  ```
  - 疎な配列：未定義の要素を含める
  - 密な配列：隙間がなくすべてのインデックスに要素がある配列
- オブジェクトが配列かどうかを判定する  
Array.isArrayメソッドは引数が配列ならばtrueを返す
```
const obj = {};
const array = [];
console.log(Array.isArray(obj)); // => false
console.log(Array.isArray(array)); // => true
console.log(typeof array); // => "object"
```
- [ES2015] TypedArray  
固定長でかつ型つきの配列を扱う別のオブジェクトが存在  
バイナリデータのバッファを示すために使われるデータ型で、WebGLやバイナリを扱う場面で利用  
基本的に別物と考える
```
  // TypedArrayを作成
  const typedArray = new Int8Array(8);
  console.log(Array.isArray(typedArray)); // => false
```
- [ES2015] 配列と分割代入
```
  const array = ["one", "two", "three"];
  const [first, second, third] = array;
  console.log(first);  // => "one"
  console.log(second); // => "two"
  console.log(third);  // => "three"
```
- undefinedの要素と未定義の要素の違い  
アクセスしても違いはわからない
```
  // 要素として`undefined`を持つ密な配列
  const denseArray = [1, undefined, 3];
  // 要素そのものがない疎な配列
  const sparseArray = [1, , 3];
  console.log(denseArray[1]); // => undefined
  console.log(sparseArray[1]); // => undefined
```  
Object#hasOwnPropertyメソッド
```
  const denseArray = [1, undefined, 3];
  const sparseArray = [1, , 3];
  // 要素自体は`undefined`値が存在する
  console.log(denseArray.hasOwnProperty(1)); // => true
  // 要素自体がない
  console.log(sparseArray.hasOwnProperty(1)); // => false
```
- 配列から要素を検索
  - その要素のインデックスが欲しい場合
  - その要素自体が欲しい場合
  - その要素が含まれているかという真偽値が欲しい場合
- インデックスを取得  
`indexOf`メソッド
```
  const array = ["Java", "JavaScript", "Ruby"];
  const indexOfJS = array.indexOf("JavaScript");
  console.log(indexOfJS); // => 1
  console.log(array[indexOfJS]); // => "JavaScript"
  // "JS" という要素はないため `-1` が返される
  console.log(array.indexOf("JS")); // => -1
```  
`findIndex`メソッド：異なるオブジェクトだが値は同じものを見つけたい場合
```
  // colorプロパティを持つオブジェクトの配列
  const colors = [
      { "color": "red" },
      { "color": "green" },
      { "color": "blue" }
  ];
  // `color`プロパティが"blue"のオブジェクトのインデックスを取得
  const indexOfBlue = colors.findIndex((obj) => {
      return obj.color === "blue";
  });
  console.log(indexOfBlue); // => 2
  console.log(colors[indexOfBlue]); // => { "color": "blue" }
```
- 条件に一致する要素を取得  
`find`メソッド：より明確に要素自体が欲しいということを表現
```
  // colorプロパティを持つオブジェクトの配列
  const colors = [
      { "color": "red" },
      { "color": "green" },
      { "color": "blue" }
  ];
  // `color`プロパティが"blue"のオブジェクトを取得
  const blueColor = colors.find((obj) => {
      return obj.color === "blue";
  });
  console.log(blueColor); // => { "color": "blue" }
  // 該当する要素がない場合は`undefined`を返す
  const whiteColor = colors.find((obj) => {
      return obj.color === "white";
  });
  console.log(whiteColor); // => undefined
```
- 指定範囲の要素を取得  
`slice`メソッド：第一引数に開始位置、第二引数に終了位置で、その範囲を取り出した新しい配列を返す
```
  const array = ["A", "B", "C", "D", "E"];
  // インデックス1から4の範囲を取り出す
  console.log(array.slice(1, 4)); // => ["B", "C", "D"]
  // 第二引数を省略した場合は、第一引数から末尾の要素までを取り出す
  console.log(array.slice(1)); // => ["B", "C", "D", "E"]
  // マイナスを指定すると後ろからの数えた位置となる
  console.log(array.slice(-1)); // => ["E"]
  // 第一引数と第二引数が同じ場合は、空の配列を返す
  console.log(array.slice(1, 1)); // => []
  // 第一引数 > 第二引数の場合、常に空配列を返す
  console.log(array.slice(4, 1)); // => []
```
- 真偽値を取得  
`includes`メソッド：配列に指定要素が含まれているかを判定
```
  const array = ["Java", "JavaScript", "Ruby"];
  // `includes`は含まれているなら`true`を返す
  if (array.includes("JavaScript")) {
      console.log("配列にJavaScriptが含まれている");
  }
```  
`some`メソッド：テストするコールバック関数にマッチする要素があるかを判定
```
  // colorプロパティを持つオブジェクトの配列
  const colors = [
      { "color": "red" },
      { "color": "green" },
      { "color": "blue" }
  ];
  // `color`プロパティが"blue"のオブジェクトがあるかどうか
  const isIncludedBlueColor = colors.some((obj) => {
      return obj.color === "blue";
  });
  console.log(isIncludedBlueColor); // => true
```
- 追加と削除  
`push`メソッド：要素を配列の末尾に追加  
`pop`メソッド：配列の末尾から要素を削除
```
  const array = ["A", "B", "C"];
  array.push("D"); // "D"を末尾に追加
  console.log(array); // => ["A", "B", "C", "D"]
  const poppedItem = array.pop(); // 最末尾の要素を削除し、その要素を返す
  console.log(poppedItem); // => "D"
  console.log(array); // => ["A", "B", "C"]
```  
`unshift`メソッド：要素を配列の先頭に追加  
`shift`メソッド：配列の先頭から要素を削除
```
  const array = ["A", "B", "C"];
  array.unshift("S"); // "S"を先頭に追加
  console.log(array); // => ["S", "A", "B", "C"]
  const shiftedItem = array.shift(); // 先頭の要素を削除
  console.log(shiftedItem); // => "S"
  console.log(array); // => ["A", "B", "C"]
```
- 配列同士を結合  
`concat`メソッド：配列と配列を結合した新しい配列を作成
```
  const array = ["A", "B", "C"];
  const newArray = array.concat(["D", "E"]);
  console.log(newArray); // => ["A", "B", "C", "D", "E"]
```
- [ES2015] 配列の展開  
`...`（Spread構文）を使うことで、配列リテラル中に既存の配列を展開できる
```
  const array = ["A", "B", "C"];
  const newArray = ["X", ...array, "Z"];
  console.log(newArray); // => ["X", "A", "B", "C", "Z"]
```
- [ES2019] 配列をフラット化  
`flat`メソッド：多次元配列をフラットな配列に変換
```
  const array = [[["A"], "B"], "C"];
  // 引数なしは 1 を指定した場合と同じ
  console.log(array.flat()); // => [["A"], "B", "C"]
  console.log(array.flat(1)); // => [["A"], "B", "C"]
  console.log(array.flat(2)); // => ["A", "B", "C"]
  // すべてをフラット化するには Infinity を渡す
  console.log(array.flat(Infinity)); // => ["A", "B", "C"]
```
- 配列から要素を削除  
`splice`メソッド：配列の任意のインデックスの要素を削除、必要ならば要素を同時に追加  
削除した要素を自動で詰める
```
  const array = ["a", "b", "c"];
  // 1番目から1つの要素("b")を削除
  array.splice(1, 1);
  console.log(array); // => ["a", "c"]
  console.log(array.length); // => 2
  console.log(array[1]); // => "c"
  // すべて削除
  array.splice(0, array.length);
  console.log(array.length); // => 0
```
- lengthプロパティへの代入  
`length`プロパティへ要素数を代入する、要素数に配列が切り詰めらる。0を代入=全削除
```
  const array = [1, 2, 3];
  array.length = 0; // 配列を空にする
  console.log(array); // => []
```
- 空の配列を代入
```
  let array = [1, 2, 3];
  console.log(array.length); // => 3
  // 新しい配列で変数を上書き
  array = [];
  console.log(array.length); // => 0
```
- 破壊的なメソッドと非破壊的なメソッド
  - 破壊的なメソッド
|メソッド名|返り値|
|---|---|
|Array.prototype.pop|配列の末尾の値|
|Array.prototype.push|変更後の配列のlength|
|Array.prototype.splice|取り除かれた要素を含む配列|
|Array.prototype.reverse|反転した配列|
|Array.prototype.shift|配列の先頭の値|
|Array.prototype.sort|ソートした配列|
|Array.prototype.unshift|変更後の配列のlength|
|Array.prototype.copyWithin[ES2015]|変更後の配列|
|Array.prototype.fill[ES2015]|変更後の配列|
- 配列を反復処理するメソッド
  - `Array#forEach`
  - `Array#map`
  - `Array#filter`
  - `Array#reduce`
- [コラム] Array-likeオブジェクト  
配列のように扱えるが配列ではないオブジェクトのことを、Array-likeオブジェクトと呼ぶ  
|機能|Array-likeオブジェクト|配列|
|---|---|---|
|インデックスアクセス（array[0]）|できる|できる|
|長さ（array.length）|持っている|持っている|
|Arrayメソッド(Array#forEachなど)|持っていない場合もある|持っている|
  - 例：argumentsオブジェクト
  - 判定：Array.isArrayメソッド => false
  - 配列に変換：Array.fromメソッド
- メソッドチェーンと高階関数
  - メソッドチェーン
  ```
    const array = ["a"].concat("b").concat("c");
    console.log(array); // => ["a", "b", "c"]
  ```

### 文字列
- 文字列を作成する
  - ""
  - ''
  - ``：改行も可能
  - 同じ文字は\でエスケープ
- エスケープシーケンス
|エスケープシーケンス|意味|
|\'|シングルクォート|
|\"|ダブルクォート|
|\` |バッククォート|
|\\|バックスラッシュ(\そのものを表示する)|
|\n|改行|
|\t|タブ|
|\uXXXX|Code Unit(\uと4桁のHexDigit)|
|\u{X} ... \u{XXXXXX}|Code Point（\u{}のカッコ中にHexDigit）|
```
  // 改行を\nのエスケープシーケンスとして入力している
  const multiline = "1行目\n2行目\n3行目";
  console.log(multiline); 
  /* 改行した結果が出力される
  1行目
  2行目
  3行目
  */
```
- 文字列を結合する
```
  const name = "JavaScript";
  console.log("Hello " + name + "!");// => "Hello JavaScript!"
  console.log(`Hello ${name}!`);// => "Hello JavaScript!"
```
- 文字へのアクセス
```
  const str = "文字列";
  // 配列と同じようにインデックスでアクセスできる
  console.log(str[0]); // => "文"
  console.log(str[1]); // => "字"
  console.log(str[2]); // => "列"
  console.log(str[42]); // => undefined
```
- 文字列とは
  - 文字コード：Unicode
  - エンコード：UTF-16
- 文字列の分解と結合  
  - `split`メソッド：文字列を配列へ分解する、第一引数=区切り文字
```
  const strings = "赤・青・緑".split("・");
  console.log(strings); // => ["赤", "青", "緑"]
```
  - `join`メソッド：区切り文字で結合した文字列、第一引数=区切り文字
```
  const str = "赤・青・緑".split("・").join("、");
  console.log(str); // => "赤、青、緑"
```
- 文字列の長さ
```
  console.log("文字列".length); // => 3
  console.log("".length); // => 0
```
- 文字列の比較  
文字列の比較には`===`（厳密比較演算子）を利用
  - 文字列の要素であるCode Unitが同じ順番で並んでいるか
  - 文字列の長さ（length）は同じか

`>`、`<`、`>=`、`<=`など大小の関係演算子で文字列同士の比較
  - 関係演算子での文字列比較はCode Unit同士を比較
  - この結果を予測することは難しく、また直感的ではない結果が生まれることも多い
- 文字列の一部を取得
  - `slice`メソッド
  - `substring`メソッド：第一引数に開始位置、第二引数に終了位置を指定  
  引数をマイナスにすると挙動がかわるため、引数は0以上で使う
- 文字列による検索
  - 文字列によるインデックスの取得
    - `文字列.indexOf("検索文字列")`  
    先頭から検索し、指定された文字列が最初に現れたインデックスを返す
    - `文字列.lastIndexOf("検索文字列")`  
    末尾から検索し、指定された文字列が最初に現れたインデックスを返す  
    ※どちらも指定した検索文字列を最初に見つけた時点で検索は終了
- 文字列にマッチした文字列の取得  
文字列を検索してマッチした文字列は、検索文字列そのものになるので自明です
- 真偽値の取得
  - String#startsWith(検索文字列)： 検索文字列が先頭にあるかの真偽値を返す
  - String#endsWith(検索文字列)： 検索文字列が末尾にあるかの真偽値を返す
  - String#includes(検索文字列)： 検索文字列を含むかの真偽値を返す
  ```
    // 検索対象となる文字列
    const str = "にわにはにわにわとりがいる";
    // startsWith - 検索文字列が先頭ならtrue
    console.log(str.startsWith("にわ")); // => true
    console.log(str.startsWith("いる")); // => false
    // endsWith - 検索文字列が末尾ならtrue
    console.log(str.endsWith("にわ")); // => false
    console.log(str.endsWith("いる")); // => true
    // includes - 検索文字列が含まれるならtrue
    console.log(str.includes("にわ")); // => true
    console.log(str.includes("いる")); // => true
  ```
- 正規表現オブジェクト  
正規表現による検索では、あるパターン（規則性）にマッチするという柔軟な検索が可能
  - 特殊文字
  ```
    \ ^ $ . * + ? ( ) [ ] { } |
  ```
  - 正規表現オブジェクトを作成
  ```
    // 正規表現リテラルで正規表現オブジェクトを作成
    const patternA = /パターン/フラグ;
    // `RegExp`コンストラクタで正規表現オブジェクトを作成
    const patternB = new RegExp("パターン文字列", "フラグ");
  ```
  - 正規表現リテラルとRegExpコンストラクタの違い  
  正規表現のパターンが評価されるタイミングの違い
    - 正規表現リテラル：ソースコードをロード（パース）した段階で正規表現のパターンが評価
    - RegExpコンストラクタ：egExpコンストラクタを呼び出すまで正規表現のパターンは評価されない
  - 正規表現リテラルで表現できる場合は、リテラルを利用したほうが簡潔でパフォーマンスもよい
  - 正規表現のパターンに変数を利用する場合などは、RegExpコンストラクタを利用
- 正規表現による検索
  - 正規表現によるインデックスの取得
    - String#indexOf(検索文字列)：指定された文字列にマッチした箇所のインデックスを返す
    - String#search(/パターン/)：指定された正規表現のパターンにマッチした箇所のインデックスを返す
    ```
      const str = "ABC123EFG";
      const searchPattern = /\d{3}/;
      console.log(str.search(searchPattern)); // => 3
    ```
  - 正規表現によるマッチした文字列の取得
    - String#matchメソッド
    ```
      "文字列".match(/パターン/);
      console.log("文字列".match(/マッチしないパターン/)); // => null
    ```
    ```
      const str = "ABC あいう DE えお";
      const alphabetsPattern = /[a-zA-Z]+/;
      // gフラグなしでは、最初の結果のみを含んだ特殊な配列を返す
      const alphabetsPattern = /[a-zA-Z]+/g;
      // gフラグありでは、すべての検索結果を含む配列を返す
    ```
    - String#matchAllメソッド  
    マッチした結果をIteratorで返す  
    matchAllメソッドはgフラグなしの正規表現はサポートしていない
    - マッチした文字列の一部を取得  
    キャプチャリング：正規表現中で/パターン1(パターン2)/のようにカッコで囲んだ部分を取り出すこと
    - [コラム] RegExp#execでのString#matchAll  
    RegExp#execメソッドを利用して、String#matchAllメソッド相当の表現を実装  
    while文などで手動で反復処理を書く必要があるため直感的ではない  
    String#matchAllメソッドが利用できる場合に、RegExp#execメソッドを利用する必要はない
  - 真偽値を取得  
  RegExp#testメソッド：パターンにマッチするかをテストする
  ```
    // 検索対象となる文字列
    const str = "にわにはにわにわとりがいる";
    // ^ - 検索文字列が先頭ならtrue
    console.log(/^にわ/.test(str)); // => true
    console.log(/^いる/.test(str)); // => false
    // $ - 検索文字列が末尾ならtrue
    console.log(/にわ$/.test(str)); // => false
    console.log(/いる$/.test(str)); // => true
    // 検索文字列が含まれるならtrue
    console.log(/にわ/.test(str)); // => true
    console.log(/いる/.test(str)); // => true
  ```
- 文字列と正規表現どちらを使うべきか  
Stringメソッドで表現できることはStringメソッドで表現  
柔軟性や曖昧な検索が必要な場合はコメントとともに正規表現を利用する
  - 正規表現
    - 曖昧な検索に強く、特殊文字を使うことで柔軟な検索結果を得られる
    - 曖昧であるため、コードを見ても何を検索しているかが正規表現のパターン自体からわかりづらい
    - コメントや変数名で具体的な意図を補足したほうがよい
- 文字列の置換/削除  
  - String#replaceメソッド：文字列の一部を置換したり削除する
  ```
    文字列.replace("検索文字列", "置換文字列");
    文字列.replace(/パターン/, "置換文字列");
  ```
  - String#replaceAllメソッド：文字列から検索文字列にマッチするものをすべて置換する場合
  ``
    文字列.replaceAll("検索文字列", "置換文字列");
    文字列.replaceAll(/パターン/, "置換文字列");
  ```
  - キャプチャを使った複雑な置換処理にも対応
  ```
    function toDateJa(dateString) {
        // パターンにマッチしたときのみ、コールバック関数で置換処理が行われる
        return dateString.replace(/(\d{4})-(\d{2})-(\d{2})/g, (all, year, month, day) => {
            // `all`には、マッチした文字列全体が入っているが今回は利用しない
            // `all`が次の返す値で置換されるイメージ
            return `${year}年${month}月${day}日`;
        });
    }
    // マッチしない文字列の場合は、そのままの文字列が返る
    console.log(toDateJa("本日ハ晴天ナリ")); // => "本日ハ晴天ナリ"
    // マッチした場合は置換した結果を返す
    console.log(toDateJa("今日は2017-03-01です")); // => "今日は2017年03月01日です"
  ```
- 文字列の組み立て  
文字列結合演算子（+）で単純に結合するよりも専用の関数を用意するほうが安全
- [ES2015] タグつきテンプレート関数  
テンプレートとなる文字列に対して一部分だけを変更する処理を行う方法

### 文字列とUnicode  
文字列におけるUnicodeを意識しないといけない場面  
JavaScriptにおけるUnicodeとUTF-16に話を限定
- Code Point（符号位置）  
Unicodeはすべての文字（制御文字などの画面に表示されない文字も含む）に対してIDを定義する目的で策定されている仕様  
この「文字」に対する「一意のID」のこと
  - String#codePointAtメソッド：文字列の指定インデックスにある文字のCode Pointの値を返す
  ```
    // 文字列"あ"のCode Pointを取得
    console.log("あ".codePointAt(0)); // => 12354
  ```
  - String.fromCodePointメソッド：指定したCode Pointに対応する文字を返す
  ```
    // Code Pointが`12354`の文字を取得する
    console.log(String.fromCodePoint(12354)); // => "あ"
    // `12354`を16進数リテラルで表記しても同じ結果
    console.log(String.fromCodePoint(0x3042)); // => "あ"
  ```
- Code PointとCode Unitの違い  
Code Unit（符号単位）：UTF-16で変換されたJavaScriptの文字列の構成要素
  - Code PointとCode Unitが同じ値
  - 絵文字を含む文字列においてはCode PointとCode Unitが異なる値
  |インデックス|0|1|2|3|4|
  |---|---|---|---|---|---|
  |文字列|リ|ン|ゴ|🍎|
  |UnicodeのCode Point（16進数）|0x30ea|0x30f3|0x30b4|0x1f34e|
  |UTF-16のCode Point（16進数）|0x30ea|0x30f3|0x30b4|0xd83c|0xdf4e|
- サロゲートペア  
2つのCode Unitの組み合わせ（合計4バイト）で1つの文字（1つのCode Point）を表現
  - \uD800～\uDBFF：上位サロゲートの範囲
  - \uDC00～\uDFFF：下位サロゲートの範囲
  ```
    // 上位サロゲート + 下位サロゲートの組み合わせ
    console.log("\uD867\uDE3D"); // => "𩸽"
    // Code Pointでの表現
    console.log("\u{29e3d}"); // => "𩸽"
  ```
- Code Pointを扱う  
Code Pointに対応したメソッドなどを利用する必要がある
  - CodePointを名前に含むメソッド
  - u（Unicode）フラグが有効化されている正規表現
  - 文字列のIteratorを扱うもの（Destructuring、for...of、Array.fromメソッドなど）
- 正規表現の.とUnicode  
正規表現にu（Unicode）フラグが追加
```
  const [all, fish] = "𩸽のひらき".match(/(.)のひらき/);
  console.log(all); // => "\ude3dのひらき"
  console.log(fish); // => "\ude3d"

  const [all, fish] = "𩸽のひらき".match(/(.)のひらき/u);
  console.log(all); // => "𩸽のひらき"
  console.log(fish); // => "𩸽"
```
- Code Pointの数を数える  
サロゲートペアを含む文字列では、lengthの結果が見た目より大きな値となる場合がある
```
  // Code Unitの個数を返す
  console.log("🍎".length); // => 2
  console.log("\uD83C\uDF4E"); // => "🍎"
  console.log("\uD83C\uDF4E".length); // => 2
```
  - 文字列におけるCode Pointの個数を数えるメソッドはない
  - 文字列をCode Pointごとに区切った配列へ変換して、配列の長さを数えるのが簡潔
  ```
    // Code Pointごとの配列にする
    // Array.fromメソッドはIteratorを配列にする
    const codePoints = Array.from("リンゴ🍎");
    console.log(codePoints); // => ["リ", "ン", "ゴ", "🍎"]
    // Code Pointの個数を数える
    console.log(codePoints.length); // => 4
  ```
- Code Pointごとに反復処理をする  
Array.fromメソッドを使えば、文字列をCode Pointで区切った文字の配列へと変換  
配列にすれば、Code Pointごとに反復処理が可能

### ラッパーオブジェクト  
- プリミティブ型とラッパーオブジェクト  
真偽値（Boolean）、数値（Number） 、文字列（String）、シンボル（Symbol）にはそれぞれ対応するオブジェクトが存在する
  - ラッパーオブジェクト：プリミティブ型の値を包んだ（ラップした）オブジェクト
|ラッパーオブジェクト|プリミティブ型|例|
|---|---|---|
|Boolean|真偽値|trueやfalse|
|Number|数値|1や2|
|String|文字列|"文字列"|
|Symbol|シンボル|Symbol("説明")|
- プリミティブ型の値からラッパーオブジェクトへの自動変換  
プリミティブ型の値からラッパーオブジェクトへの変換は自動的に行われる  
  - 必要に応じて、プリミティブ型の文字列は自動的にラッパーオブジェクトに変換されるため
  - new String("string")のようにラッパーオブジェクトのインスタンスを扱う利点がないため
  - ラッパーオブジェクトをtypeof演算子で評価した結果が、プリミティブ型ではなく"object"となり混乱を生むため  
  => 2つを明示的に使い分ける利点はないため、常にリテラルを使うことを推奨

### 関数とスコープ  
定義された関数はそれぞれのスコープを持つ  
スコープ：変数や関数の引数などを参照できる範囲を決めるもの
- スコープとは：変数の名前や関数などの参照できる範囲を決めるもの  
スコープの中で定義された変数はスコープの内側でのみ参照でき、スコープの外側からは参照できない
```
  function fn() {
      const x = 1;
      // fn関数のスコープ内から`x`は参照できる
      console.log(x); // => 1
  }
  fn();
  // fn関数のスコープ外から`x`は参照できないためエラー
  console.log(x); // => ReferenceError: x is not defined
```
  - 関数スコープ：関数によるスコープのこと
- ブロックスコープ  
{と}で囲んだ範囲をブロック  
ブロック内で宣言された変数は、スコープ内でのみ参照でき、スコープの外側からは参照できない
```
  // ブロック内で定義した変数はスコープ内でのみ参照できる
  {
      const x = 1;
      console.log(x); // => 1
  }
  // スコープの外から`x`を参照できないためエラー
  console.log(x); // => ReferenceError: x is not defined
```
- スコープチェーン  
スコープもネスト可能
```
  {
      // OUTERブロックスコープ
      {
          // INNERブロックスコープ
      }
  }
```
- グローバルスコープ  
暗黙的なグローバルスコープ（大域スコープ）と呼ばれるスコープが存在する  
ビルトインオブジェクト：プログラム実行時に自動的に定義
  - むやみにグローバルスコープへ変数を定義しない
- [コラム] 変数を参照できる範囲を小さくする  
特定の変数を参照できる範囲を小さくするのはよいこと
- 関数スコープとvarの巻き上げ  
  - let 変数を宣言する前にその変数を参照：ReferenceErrorの例外が発生
  - var 変数を宣言する前にその変数を参照：undefined
    - 巻き上げ（hoisting）  
    宣言部分が暗黙的にもっとも近い関数またはグローバルスコープの先頭に巻き上げられ、代入部分はそのままの位置に残る
- 関数宣言と巻き上げ  
functionキーワードを使った関数宣言もvarと同様に、もっとも近い関数またはグローバルスコープの先頭に巻き上げられる
```
  // `hello`関数の宣言より前に呼び出せる
  hello(); // => "Hello"

  function hello(){
      return "Hello";
  }
```
```
  // 解釈されたコード
  // `hello`関数の宣言が巻き上げられる
  function hello(){
      return "Hello";
  }

  hello(); // => "Hello"
```  
varによる変数宣言の巻き上げとは異なり、問題となることはほとんどない  
実際に巻き上げられた関数を呼び出せるため
- [コラム] 即時実行関数  
即時実行関数（IIFE, Immediately-Invoked Function Expression）： グローバルスコープの汚染を避けるために生まれたイディオム
```
  // 匿名関数を宣言 + 実行を同時に行っている
  (function() {
      // 関数のスコープ内でfoo変数を宣言している
      var foo = "foo";
      console.log(foo); // => "foo"
  })();
  // foo変数のスコープ外
  console.log(typeof foo === "undefined"); // => true
```  
グローバルスコープの汚染を防ぐための即時実行関数は不要  
即時実行関数は次のようにletやconstとブロックスコープで置き換えられる
```
  {
      // ブロックスコープ内でfoo変数を宣言している
      const foo = "foo";
      console.log(foo); // => "foo"
  }
  // foo変数のスコープ外
  console.log(typeof foo === "undefined"); // => true
```
- クロージャー  
「外側のスコープにある変数への参照を保持できる」という関数が持つ性質のこと
- 静的スコープ  
コードを実行する前にどの識別子がどの変数を参照しているかがわかる
```
  const x = 10; // ＊1

  function printX() {
      // この識別子`x`は常に ＊1 の変数`x`を参照する
      console.log(x); // => 10
  }

  function run() {
      const x = 20; // ＊2
      printX(); // 常に10が出力される
  }

  run();
```
- [コラム] 動的スコープ  
動的スコープという呼び出し元により識別子がどの変数を参照するかが変わる仕組みを持つ言語もある  
JavaScriptは変数や関数の参照先は静的スコープで決まる

- メモリ管理の仕組み  
ガベージコレクション：使わなくなった変数やデータを解放する仕組み  
データがメモリ上から解放されるかどうかはあくまで、そのデータが参照されているかによって決定
- クロージャーがなぜ動くのか
```
  const createCounter = () => {
      let count = 0;
      return function increment() {
          // `increment`関数は`createCounter`関数のスコープに定義された`変数`count`を参照している
          count = count + 1;
          return count;
      };
  };
  // createCounter()の実行結果は、内側で定義されていた`increment`関数
  const myCounter = createCounter();
  // myCounter関数の実行結果は`count`の評価結果
  console.log(myCounter()); // => 1
  console.log(myCounter()); // => 2
```
  - myCounter変数はcreateCounter関数の返り値であるincrement関数を参照している
  - myCounter変数はincrement関数を経由してcount変数を参照している
  - myCounter変数を実行した後もcount変数への参照は保たれている  
  クロージャー（関数閉包）：上記のような性質
- クロージャーの用途
  - 関数に状態を持たせる手段として
  - 外から参照できない変数を定義する手段として
  - グローバル変数を減らす手段として
  - 高階関数の一部分として
- [コラム] 状態を持つ関数オブジェクト  
関数はオブジェクトの一種 => 直接プロパティに値を代入
```
  function countUp() {
      // countプロパティを参照して変更する
      countUp.count = countUp.count + 1;
      return countUp.count;
  }
  // 関数オブジェクトにプロパティとして値を代入する
  countUp.count = 0;
  // 呼び出すごとにcountが更新される
  console.log(countUp()); // => 1
  console.log(countUp()); // => 2
```  
この方法は推奨されない => 関数の外からcountプロパティを変更できるため

### 関数とthis
- 実行コンテキストとthis
  - スクリプトにおけるthis  
  トップレベルのスコープに書かれたthisはグローバルオブジェクトを参照
  ```
    <script>
    // 実行コンテキストは"Script"
    console.log(this); // => window
    </script>
  ```
  - モジュールにおけるthis  
  トップレベルのスコープに書かれたthisは常にundefined
  ```
    <script type="module">
    // 実行コンテキストは"Module"
    console.log(this); // => undefined
    </script>
  ```
  - `globalThis`  
  単純にグローバルオブジェクトを参照したい場合は、thisではなくglobalThisを使う
- 関数とメソッドにおけるthis
  - 関数の種類とメソッドの種類
|名前|関数|メソッド|
|---|---|---|
|関数宣言(function fn(){})|✔|x|
|関数式(const fn = function(){})|✔|✔|
|Arrow Function(const fn = () => {})|✔|✔|
|メソッドの短縮記法(const obj = { method(){} })|x|✔|
- Arrow Function以外の関数におけるthis  
実行時に決まる値：thisは関数に渡される暗黙的な引数のようなもの
```
  // 疑似的な`this`の値の仕組み
  // 関数は引数として暗黙的に`this`の値を受け取るイメージ
  function fn(暗黙的に渡されるthisの値, 仮引数) {
      console.log(this); // => 暗黙的に渡されるthisの値
  }
  // 暗黙的に`this`の値を引数として渡しているイメージ
  fn(暗黙的に渡すthisの値, 引数);
```
```
  // `fn`関数はメソッドではないのでベースオブジェクトはない
  fn();
  // `obj.method`メソッドのベースオブジェクトは`obj`
  obj.method();
  // `obj1.obj2.method`メソッドのベースオブジェクトは`obj2`
  // ドット演算子、ブラケット演算子どちらも結果は同じ
  obj1.obj2.method();
  obj1["obj2"]["method"]();
```
- 関数宣言や関数式におけるthis  
関数宣言や関数式：ベースオブジェクトはないため、thisはundefined
- メソッド呼び出しにおけるthis  
メソッドが何かしらのオブジェクトに所属している：thisは、そのオブジェクト
- thisが問題となるパターン
  - 問題: thisを含むメソッドを変数に代入した場合  
  thisはundefined
    - メソッドをわざわざただの関数として呼ばなければそもそもこの問題は発生しない
    - thisの値を指定して関数を呼べるメソッドで関数を実行する
  - 対処法: call、apply、bindメソッド
  ```
    関数.call(thisの値, ...関数の引数);
    関数.apply(thisの値, [関数の引数1, 関数の引数2]);
    関数.bind(thisの値, ...関数の引数); // => thisや引数がbindされた関数
  ```
  - 問題: コールバック関数とthis  
  callback関数のthisはundefined
  - 対処法: thisを一時変数へ代入する
  ```
    "use strict";
    const Prefixer = {
        prefix: "pre",
        prefixArray(strings) {
            // `that`は`prefixArray`メソッド呼び出しにおける`this`となる
            // つまり`that`は`Prefixer`オブジェクトを参照する
            const that = this;
            return strings.map(function(str) {
                // `this`ではなく`that`を参照する
                return that.prefix + "-" + str;
            });
        }
    };
    // `prefixArray`メソッドにおける`this`は`Prefixer`
    const prefixedStrings = Prefixer.prefixArray(["a", "b", "c"]);
    console.log(prefixedStrings); // => ["pre-a", "pre-b", "pre-c"]
  ```
  - 対処法: Arrow Functionでコールバック関数を扱う  
  Arrow Functionはこの暗黙的なthisの値を受け取りません  
  Arrow Functionで定義したコールバック関数は呼び出し方には関係なく、常に外側の関数のthisをそのまま利用
  ```
    "use strict";
    const Prefixer = {
        prefix: "pre",
        prefixArray(strings) {
            return strings.map((str) => {
                // Arrow Function自体は`this`を持たない
                // `this`は外側の`prefixArray`関数が持つ`this`を参照する
                // そのため`this.prefix`は"pre"となる
                return this.prefix + "-" + str;
            });
        }
    };
    // このとき、`prefixArray`のベースオブジェクトは`Prefixer`となる
    // つまり、`prefixArray`メソッド内の`this`は`Prefixer`を参照する
    const prefixedStrings = Prefixer.prefixArray(["a", "b", "c"]);
    console.log(prefixedStrings); // => ["pre-a", "pre-b", "pre-c"]
  ```
- Arrow Functionとthis  
Arrow Functionで定義された関数やメソッドにおけるthisがどの値を参照するかは関数の定義時（静的）に決まる  
Arrow Function自身の外側のスコープに定義されたもっとも近い関数のthisの値
- メソッドとコールバック関数とArrow Function
- Arrow Functionはthisをbindできない

### クラス
- クラスの定義  
  - クラス宣言文
  ```
    class MyClass {
        constructor() {
            // コンストラクタ関数の処理
            // インスタンス化されるときに自動的に呼び出される
        }
    }
  ```
  - クラス式
  ```
    const MyClass = class MyClass {
        constructor() {}
    };

    const AnonymousClass = class {
        constructor() {}
    };
  ```  
    - constructorは省略できる
- クラスのインスタンス化  
インスタンス化：class構文で定義したクラスからインスタンスを作成すること
```
    class MyClass {
    }
    // `MyClass`をインスタンス化する
    const myClass = new MyClass();
    // 毎回新しいインスタンス(オブジェクト)を作成する
    const myClassAnother = new MyClass();
    // それぞれのインスタンスは異なるオブジェクト
    console.log(myClass === myClassAnother); // => false
    // クラスのインスタンスかどうかは`instanceof`演算子で判定できる
    console.log(myClass instanceof MyClass); // => true
    console.log(myClassAnother instanceof MyClass); // => true
```
- [Note] クラス名は大文字ではじめる
```
  class Thing {}
  const thing = new Thing();
```
- [コラム] class構文と関数でのクラスの違い
  - class構文：new演算子でインスタンス化して使う、関数呼び出し不可
  - 関数：関数として呼び出せる
- クラスのプロトタイプメソッドの定義  
クラスの動作はメソッドによって定義できる  
constructorメソッドは初期化時に呼ばれる特殊なメソッド  
```
  class クラス {
      メソッド() {
          // ここでの`this`はベースオブジェクトを参照
      }
  }

  const インスタンス = new クラス();
  // メソッド呼び出しのベースオブジェクト(`this`)は`インスタンス`となる
  インスタンス.メソッド();
```
 - プロトタイプメソッド：クラスの各インスタンスから共有されるメソッド、インスタンスメソッド
- クラスのインスタンスに対してメソッドを定義する  
コンストラクタ関数内でインスタンスオブジェクトであるthisに対してメソッドを定義する
```
class Counter {
    constructor() {
        this.count = 0;
        this.increment = () => {
            this.count++;
        };
    }
}
const counterA = new Counter();
const counterB = new Counter();
// 各インスタンスオブジェクトのメソッドの参照先は異なる
console.log(counterA.increment !== counterB.increment); // => true
```
  - 各インスタンスからのメソッドの参照先も異なる
  - Arrow Functionが利用できる
- クラスのアクセッサプロパティの定義  
アクセッサプロパティ：プロパティの参照（getter）、プロパティへの代入（setter）時に呼び出される特殊なメソッド
```
  class クラス {
      // getter
      get プロパティ名() {
          return 値;
      }
      // setter
      set プロパティ名(仮引数) {
          // setterの処理
      }
  }
  const インスタンス = new クラス();
  インスタンス.プロパティ名; // getterが呼び出される
  インスタンス.プロパティ名 = 値; // setterが呼び出される
```
- [コラム] プライベートプロパティ  
外から直接読み書きしてほしくないプロパティを_（アンダーバー）で開始するのはただの習慣であるため、構文としての意味はありません。  
外から原理的に参照できないプライベートプロパティ（hard private）を定義する構文はありません。
- 静的メソッド  
クラスをインスタンス化せずに利用できる静的メソッド（クラスメソッド）もある
```
  class クラス {
      static メソッド() {
          // 静的メソッドの処理
      }
  }
  // 静的メソッドの呼び出し
  クラス.メソッド();
```  
静的メソッドは、クラスのインスタンスを作成する処理やクラスに関係する処理を書くために利用される  
- 2種類のインスタンスメソッドの定義  
```
  class ConflictClass {
      constructor() {
          this.method = () => {
              console.log("インスタンスオブジェクトのメソッド");
          };
      }

      method() {
          console.log("プロトタイプメソッド");
      }
  }

  const conflict = new ConflictClass();
  conflict.method(); // "インスタンスオブジェクトのメソッド"
  // インスタンスの`method`プロパティを削除
  delete conflict.method;
  conflict.method(); // "プロトタイプメソッド"
```
  - プロトタイプメソッドとインスタンスオブジェクトのメソッドは上書きされずにどちらも定義されている
  - インスタンスオブジェクトのメソッドがプロトタイプオブジェクトのメソッドよりも優先して呼ばれている
- プロトタイプオブジェクト  
JavaScriptの関数オブジェクトのprototypeプロパティに自動的に作成される特殊なオブジェクト
```
  function fn() {
  }
  // `prototype`プロパティにプロトタイプオブジェクトが存在する
  console.log(typeof fn.prototype === "object"); // => true

  class MyClass {
  }
  // `prototype`プロパティにプロトタイプオブジェクトが存在する
  console.log(typeof MyClass.prototype === "object"); // => true
```
- プロトタイプチェーン  
インスタンスからプロトタイプメソッドを呼び出せるのはプロトタイプチェーンと呼ばれる仕組みによるもの
  - インスタンス作成時に、インスタンスの[[Prototype]]内部プロパティへプロトタイプオブジェクトの参照を保存する処理
  - インスタンスからプロパティ（またはメソッド）を参照するときに、[[Prototype]]内部プロパティまで探索する処理
- インスタンス作成とプロトタイプチェーン
```
  class MyClass {
      method() {
          console.log("プロトタイプのメソッド");
      }
  }
  const instance = new MyClass();
  // `instance`の`[[Prototype]]`内部プロパティは`MyClass.prototype`と一致する
  const MyClassPrototype = Object.getPrototypeOf(instance);
  console.log(MyClassPrototype === MyClass.prototype); // => true
```
- プロパティの参照とプロトタイプチェーン  
  - instanceオブジェクト自身
  - instanceオブジェクトの[[Prototype]]の参照先（プロトタイプオブジェクト）
  - どこにもなかった場合はundefined
- 継承  
extendsキーワードを使うことで既存のクラスを継承できます。 継承とは、クラスの構造や機能を引き継いだ新しいクラスを定義すること
- 継承したクラスの定義
```
  class 子クラス extends 親クラス {

  }
```
- super  
extendsを使って定義した子クラスから親クラスを参照するにはsuperというキーワードを利用します
```
  // 親クラス
  class Parent {
      constructor(...args) {
          console.log("Parentコンストラクタの処理", ...args);
      }
  }
  // Parentを継承したChildクラスの定義
  class Child extends Parent {
      constructor(...args) {
          // Parentのコンストラクタ処理を呼び出す
          super(...args);
          console.log("Childコンストラクタの処理", ...args);
      }
  }
  const child = new Child("引数1", "引数2");
  // "Parentコンストラクタの処理", "引数1", "引数2"
  // "Childコンストラクタの処理", "引数1", "引数2"
```
- コンストラクタの処理順は親クラスから子クラスへ  
コンストラクタの処理順は、親クラスから子クラスへと順番が決まっています
```
  class Parent {
      constructor() {
          this.name = "Parent";
      }
  }
  class Child extends Parent {
      constructor() {
          // 子クラスでは`super()`を`this`に触る前に呼び出さなければならない
          super();
          // 子クラスのコンストラクタ処理
          // 親クラスで書き込まれた`name`は上書きされる
          this.name = "Child";
      }
  }
  const parent = new Parent();
  console.log(parent.name); // => "Parent"
  const child = new Child();
  console.log(child.name); // => "Child"
```
- プロトタイプ継承  
```
  class Parent {
      method() {
          console.log("Parent#method");
      }
  }
  // `Parent`を継承した`Child`を定義
  class Child extends Parent {
      // methodの定義はない
  }
  // `Child`のインスタンスは`Parent`のプロトタイプメソッドを継承している
  const instance = new Child();
  instance.method(); // "Parent#method"
```
  - instanceオブジェクト自身
  - Child.prototype（instanceオブジェクトの[[Prototype]]の参照先）
  - Parent.prototype（Child.prototypeオブジェクトの[[Prototype]]の参照先）
- 静的メソッドの継承  
```
  class Parent {
      static hello() {
          return "Hello";
      }
  }
  class Child extends Parent {}
  console.log(Child.hello()); // => "Hello"
```
  - Childコンストラクタ
  - Parentコンストラクタ（Childコンストラクタの[[Prototype]]の参照先）
- superプロパティ
```
  class Parent {
      method() {
          console.log("Parent#method");
      }
  }
  class Child extends Parent {
      method() {
          console.log("Child#method");
          // `this.method()`だと自分(`this`)のmethodを呼び出して無限ループする
          // そのため明示的に`super.method()`とParent#methodを呼び出す
          super.method();
      }
  }
  const child = new Child();
  child.method();
  // コンソールには次のように出力される
  // "Child#method"
  // "Parent#method"
```
- 継承の判定
```
  class Parent {}
  class Child extends Parent {}

  const parent = new Parent();
  const child = new Child();
  // `Parent`のインスタンスは`Parent`のみを継承したインスタンス
  console.log(parent instanceof Parent); // => true
  console.log(parent instanceof Child); // => false
  // `Child`のインスタンスは`Child`と`Parent`を継承したインスタンス
  console.log(child instanceof Parent); // => true
  console.log(child instanceof Child); // => true
```
- ビルトインオブジェクトの継承
```
  class MyArray extends Array {
      get first() {
          if (this.length === 0) {
              return undefined;
          } else {
              return this[0];
          }
      }

      get last() {
          if (this.length === 0) {
              return undefined;
          } else {
              return this[this.length - 1];
          }
      }
  }

  // Arrayを継承しているのでArray.fromも継承している
  // Array.fromはIterableなオブジェクトから配列インスタンスを作成する
  const array = MyArray.from([1, 2, 3, 4, 5]);
  console.log(array.length); // => 5
  console.log(array.first); // => 1
  console.log(array.last); // => 5
```

### 例外処理
- try...catch構文  
例外が発生しうるブロックをマークし、例外が発生したときの処理を記述するための構文
```
  try {
      console.log("try節:この行は実行されます");
      // 未定義の関数を呼び出してReferenceError例外が発生する
      undefinedFunction();
      // 例外が発生したため、この行は実行されません
  } catch (error) {
      // 例外が発生したあとはこのブロックが実行される
      console.log("catch節:この行は実行されます");
      console.log(error instanceof ReferenceError); // => true
      console.log(error.message); // => "undefinedFunction is not defined"
  } finally {
      // このブロックは例外の発生に関係なく必ず実行される
      console.log("finally節:この行は実行されます");
  }
```
- throw文  
ユーザーが例外を投げる
```
  try {
      // 例外を投げる
      throw new Error("例外が投げられました");
  } catch (error) {
      // catch節のスコープでerrorにアクセスできる
      console.log(error.message); // => "例外が投げられました"
  }
```
- エラーオブジェクト
- Error
```
  // 渡された数値が0以上ではない場合に例外を投げる関数
  function assertPositiveNumber(num) {
      if (num < 0) {
          throw new Error(`${num} is not positive.`);
      }
  }

  try {
      // 0未満の値を渡しているので、関数が例外を投げる
      assertPositiveNumber(-1);
  } catch (error) {
      console.log(error instanceof Error); // => true
      console.log(error.message); // => "-1 is not positive."
}
```
- ビルトインエラー  
状況に合わせたいくつかの種類があり、これらはビルトインエラーとして定義されている
  - ReferenceError：存在しない変数や関数などの識別子が参照された場合のエラー
  - SyntaxError：構文的に不正なコードを解釈しようとした場合のエラー
  - TypeError：値が期待される型ではない場合のエラー
- ビルトインエラーを投げる
```
  // 文字列を反転する関数
  function reverseString(str) {
      if (typeof str !== "string") {
          throw new TypeError(`${str} is not a string`);
      }
      return Array.from(str).reverse().join("");
  }

  try {
      // 数値を渡す
      reverseString(100);
  } catch (error) {
      console.log(error instanceof TypeError); // => true
      console.log(error.name); // => "TypeError"
      console.log(error.message); // "100 is not a string"
  }
```
- エラーとデバッグ  
エラーが持つ情報を活用することで、ソースコードのどこでどのような例外が投げられたのかを知ることができる
- console.errorとスタックトレース
```
  function fn() {
      console.log("メッセージ");
      console.error("エラーメッセージ");
  }

  fn();
```

### 非同期処理:コールバック/Promise/Async Function
- コードの評価
  - 同期処理(sync)  
    - コードを順番に処理していき、ひとつの処理が終わるまで次の処理は行ない
    - 時間がかかる処理 → ブラウザでは、処理中は他の動作ができない
  - 非同期処理(async)
    - コードを順番に処理していきますが、ひとつの非同期処理が終わるのを待たずに次の処理を評価
    - コードの見た目上の並びとは異なる順番で実行されることがある
- 非同期処理はメインスレッドで実行される  
JavaScriptでは一部の例外を除き非同期処理が並行処理（concurrent）として扱われる
- 非同期処理と例外処理  
非同期処理の外からは非同期処理の中で例外が発生したかがわかりません  
非同期処理の中で例外が発生したことを非同期処理の外へ伝える方法が必要
- エラーファーストコールバック
  - 処理が失敗した場合は、コールバック関数の1番目の引数にエラーオブジェクトを渡して呼び出す
  - 処理が成功した場合は、コールバック関数の1番目の引数にはnullを渡し、2番目以降の引数に成功時の結果を渡して呼び出す
  ```
    fs.readFile("./example.txt", (error, data) => {
        if (error) {
            // 読み込み中にエラーが発生しました
        } else {
            // データを読み込むことができました
        }
    });
  ```
#### [ES2015] Promise  
非同期処理の結果を表現するビルトインオブジェクト
```
  // asyncPromiseTask関数はPromiseインスタンスを返す
  asyncPromiseTask().then(()=> {
      // 非同期処理が成功したときの処理
  }).catch(() => {
      // 非同期処理が失敗したときの処理
  });
```
- Promiseインスタンスの作成
```
  // `Promise`インスタンスを作成
  const promise = new Promise((resolve, reject) => {
      // 非同期の処理が成功したときはresolve()を呼ぶ
      // 非同期の処理が失敗したときにはreject()を呼ぶ
  });
  const onFulfilled = () => {
      console.log("resolveされたときに呼ばれる");
  };
  const onRejected = () => {
      console.log("rejectされたときに呼ばれる");
  };
  // `then`メソッドで成功時と失敗時に呼ばれるコールバック関数を登録
  promise.then(onFulfilled, onRejected);
```
- Promise#thenとPromise#catch  
```
  /**
  * 1000ミリ秒未満のランダムなタイミングでレスポンスを疑似的にデータ取得する関数
  * 指定した`path`にデータがある場合、成功として**Resolved**状態のPromiseオブジェクトを返す
  * 指定した`path`にデータがない場合、失敗として**Rejected**状態のPromiseオブジェクトを返す
  */
  function dummyFetch(path) {
      return new Promise((resolve, reject) => {
          setTimeout(() => {
              if (path.startsWith("/success")) {
                  resolve({ body: `Response body of ${path}` });
              } else {
                  reject(new Error("NOT FOUND"));
              }
          }, 1000 * Math.random());
      });
  }
  // `then`メソッドで成功時と失敗時に呼ばれるコールバック関数を登録
  // /success/data のリソースは存在するので成功しonFulfilledが呼ばれる
  dummyFetch("/success/data").then(function onFulfilled(response) {
      console.log(response); // => { body: "Response body of /success/data" }
  }, function onRejected(error) {
      // この行は実行されません
  });
  // /failure/data のリソースは存在しないのでonRejectedが呼ばれる
  dummyFetch("/failure/data").then(function onFulfilled(response) {
      // この行は実行されません
  }, function onRejected(error) {
      console.log(error); // Error: "NOT FOUND"
  });
```
- Promiseと例外
```
  function throwPromise() {
      return new Promise((resolve, reject) => {
          // Promiseコンストラクタの中で例外は自動的にキャッチされrejectを呼ぶ
          throw new Error("例外が発生");
          // 例外が発生すると、これ以降のコンストラクタの処理は実行されません
      });
  }

  throwPromise().catch(error => {
      console.log(error.message); // => "例外が発生"
  });
```
- Promiseの状態
  - Fulfilled
    - resolve（成功）したときの状態。このときonFulfilledが呼ばれる
  - Rejected
    - reject（失敗）または例外が発生したときの状態。このときonRejectedが呼ばれる
  - Pending
    - FulfilledまたはRejectedではない状態
    - new Promiseでインスタンスを作成したときの初期状態
- Promise.resolve
```
  const fulfilledPromise = Promise.resolve();
```
- Promise.reject
```
  const rejectedPromise = Promise.reject(new Error("エラー"));
```
- Promiseチェーン
```
  // Promiseインスタンスでメソッドチェーン
  Promise.resolve()
      // thenメソッドは新しい`Promise`インスタンスを返す
      .then(() => {
          console.log(1);
      })
      .then(() => {
          console.log(2);
      });
```
- Promiseチェーンで値を返す
```
  Promise.resolve(1).then((value) => {
      console.log(value); // => 1
      return value * 2;
  }).then(value => {
      console.log(value); // => 2
      return value * 2;
  }).then(value => {
      console.log(value); // => 4
      // 値を返さない場合は undefined を返すのと同じ
  }).then(value => {
      console.log(value); // => undefined
  });
```
- コールバック関数でPromiseインスタンスを返す
```
  romise.resolve().then(function onFulfilledA() {
      return Promise.reject(new Error("失敗"));
  }).then(function onFulfilledB() {
      console.log("onFulfilledBは呼び出されません");
  }).catch(function onRejected(error) {
      console.log(error.message); // => "失敗"
  }).then(function onFulfilledC() {
      console.log("onFulfilledCは呼び出されます");
  });
```
- [ES2018] Promiseチェーンの最後に処理を書く  
Promise#finallyメソッドは成功時、失敗時どちらの場合でも呼び出されるコールバック関数を登録できます
```
  // `promise`にはResolvedまたはRejectedなPromiseインスタンスがランダムで入る
  const promise = Math.random() < 0.5 ? Promise.resolve() : Promise.reject();
  promise.then(() => {
      console.log("Promise#then");
  }).catch((error) => {
      console.log("Promise#catch");
  }).finally(() => {
      // 成功、失敗どちらの場合でも呼び出される
      console.log("Promise#finally");
  });
```
- Promiseチェーンで逐次処理
```
  function dummyFetch(path) {
      return new Promise((resolve, reject) => {
          setTimeout(() => {
              if (path.startsWith("/resource")) {
                  resolve({ body: `Response body of ${path}` });
              } else {
                  reject(new Error("NOT FOUND"));
              }
          }, 1000 * Math.random());
      });
  }

  const results = [];
  // Resource Aを取得する
  dummyFetch("/resource/A").then(response => {
      results.push(response.body);
      // Resource Bを取得する
      return dummyFetch("/resource/B");
  }).then(response => {
      results.push(response.body);
  }).then(() => {
      console.log(results); // => ["Response body of /resource/A", "Response body of /resource/B"]
  });
```
- Promise.allで複数のPromiseをまとめる  
Promise.allメソッドは複数のPromiseがすべて完了するまで待つ処理
```
  // `timeoutMs`ミリ秒後にresolveする
  function delay(timeoutMs) {
      return new Promise((resolve) => {
          setTimeout(() => {
              resolve(timeoutMs);
          }, timeoutMs);
      });
  }
  const promise1 = delay(1);
  const promise2 = delay(2);
  const promise3 = delay(3);

  Promise.all([promise1, promise2, promise3]).then(function(values) {
      console.log(values); // => [1, 2, 3]
  });
```
- Promise.race  
複数のPromiseを受け取りますが、Promiseが1つでも完了した（Settled状態となった）時点で次の処理を実行する
  - 配列の中で一番最初にSettledとなったPromiseがFulfilledの場合は、新しいPromiseインスタンスもFulfilledになる
  - 配列の中で一番最初にSettledとなったPromiseがRejectedの場合は、新しいPromiseインスタンスも Rejectedになる
  ```
    // `timeoutMs`ミリ秒後にresolveする
    function delay(timeoutMs) {
        return new Promise((resolve) => {
            setTimeout(() => {
                resolve(timeoutMs);
            }, timeoutMs);
        });
    }
    // 1つでもresolveまたはrejectした時点で次の処理を呼び出す
    const racePromise = Promise.race([
        delay(1),
        delay(32),
        delay(64),
        delay(128)
    ]);
    racePromise.then(value => {
        // もっとも早く完了するのは1ミリ秒後
        console.log(value); // => 1
    });
  ```
- [JavaScript Promiseの本](https://azu.github.io/promises-book/)
#### [ES2017] Async Function  
非同期処理を行う関数を定義する構文：必ずPromiseインスタンスを返す関数を定義する構文
```
  async function doAsync() {
      return "値";
  }
  // doAsync関数はPromiseを返す
  doAsync().then(value => {
      console.log(value); // => "値"
  });
```
```
  // 通常の関数でPromiseインスタンスを返している
  function doAsync() {
      return Promise.resolve("値");
  }
  doAsync().then(value => {
      console.log(value); // => "値"
  });
```
- Async Functionの定義
```
  // 関数宣言のAsync Function版
  async function fn1() {}
  // 関数式のAsync Function版
  const fn2 = async function() {};
  // Arrow FunctionのAsync Function版
  const fn3 = async() => {};
  // メソッドの短縮記法のAsync Function版
  const obj = { async method() {} };
```
  - Async Functionは必ずPromiseインスタンスを返す
  - Async Function内ではawait式が利用できる
- Async FunctionはPromiseを返す
  - Async Functionが値をreturnした場合、その返り値を持つFulfilledなPromiseを返す
  - Async FunctionがPromiseをreturnした場合、その返り値のPromiseをそのまま返す
  - Async Function内で例外が発生した場合は、そのエラーを持つRejectedなPromiseを返す
  ```
    // 1. resolveFnは値を返している
    // 何もreturnしていない場合はundefinedを返したのと同じ扱いとなる
    async function resolveFn() {
        return "返り値";
    }
    resolveFn().then(value => {
        console.log(value); // => "返り値"
    });

    // 2. rejectFnはPromiseインスタンスを返している
    async function rejectFn() {
        return Promise.reject(new Error("エラーメッセージ"));
    }

    // rejectFnはRejectedなPromiseを返すのでcatchできる
    rejectFn().catch(error => {
        console.log(error.message); // => "エラーメッセージ"
    });

    // 3. exceptionFnは例外を投げている
    async function exceptionFn() {
        throw new Error("例外が発生しました");
        // 例外が発生したため、この行は実行されません
    }

    // Async Functionで例外が発生するとRejectedなPromiseが返される
    exceptionFn().catch(error => {
        console.log(error.message); // => "例外が発生しました"
    });
  ```
- await式  
await式は右辺のPromiseインスタンスがFulfilledまたはRejectedになるまでその場で非同期処理の完了を待つ
```
  async function asyncMain() {
      // PromiseがFulfilledまたはRejectedとなるまで待つ
      await Promiseインスタンス;
      // Promiseインスタンスの状態が変わったら処理を再開する
  }
```
  - 右辺のPromiseがFulfilledとなった場合：resolveされた値がawait式の返り値
  - 右辺のPromiseがRejectedとなった場合：その場でエラーをthrowする  
await式を使うことで、try...catch構文のように非同期処理を同期処理と同じ構文を使って扱える
```
  async function asyncMain() {
      // await式のエラーはtry...catchできる
      try {
          // `await`式で評価した右辺のPromiseがRejectedとなったため、例外がthrowされる
          const value = await Promise.reject(new Error("エラーメッセージ"));
          // await式で例外が発生したため、この行は実行されません
      } catch (error) {
          console.log(error.message); // => "エラーメッセージ"
      }
  }
  // asyncMainはResolvedなPromiseを返す
  asyncMain().catch(error => {
      // すでにtry...catchされているため、この行は実行されません
  });
```
- Promiseチェーンをawait式で表現する
```
  function dummyFetch(path) {
      return new Promise((resolve, reject) => {
          setTimeout(() => {
              if (path.startsWith("/resource")) {
                  resolve({ body: `Response body of ${path}` });
              } else {
                  reject(new Error("NOT FOUND"));
              }
          }, 1000 * Math.random());
      });
  }
  // リソースAとリソースBを順番に取得する
  function fetchAB() {
      const results = [];
      return dummyFetch("/resource/A").then(response => {
          results.push(response.body);
          return dummyFetch("/resource/B");
      }).then(response => {
          results.push(response.body);
          return results;
      });
  }
  // リソースを取得して出力する
  fetchAB().then((results) => {
      console.log(results); // => ["Response body of /resource/A", "Response body of /resource/B"]
  });
```
```
  function dummyFetch(path) {
      return new Promise((resolve, reject) => {
          setTimeout(() => {
              if (path.startsWith("/resource")) {
                  resolve({ body: `Response body of ${path}` });
              } else {
                  reject(new Error("NOT FOUND"));
              }
          }, 1000 * Math.random());
      });
  }
  // リソースAとリソースBを順番に取得する
  async function fetchAB() {
      const results = [];
      const responseA = await dummyFetch("/resource/A");
      results.push(responseA.body);
      const responseB = await dummyFetch("/resource/B");
      results.push(responseB.body);
      return results;
  }
  // リソースを取得して出力する
  fetchAB().then((results) => {
      console.log(results); // => ["Response body of /resource/A", "Response body of /resource/B"]
  });
```
- Async Functionと組み合わせ
  - Async Functionと反復処理
  ```
    function dummyFetch(path) {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                if (path.startsWith("/resource")) {
                    resolve({ body: `Response body of ${path}` });
                } else {
                    reject(new Error("NOT FOUND"));
                }
            }, 1000 * Math.random());
        });
    }
    // 複数のリソースを順番に取得する
    async function fetchResources(resources) {
        const results = [];
        for (let i = 0; i < resources.length; i++) {
            const resource = resources[i];
            // ループ内で非同期処理の完了を待っている
            const response = await dummyFetch(resource);
            results.push(response.body);
        }
        // 反復処理がすべて終わったら結果を返す(返り値となるPromiseを`results`でresolveする)
        return results;
    }
    // 取得したいリソースのパス配列
    const resources = [
        "/resource/A",
        "/resource/B"
    ];
    // リソースを取得して出力する
    fetchResources(resources).then((results) => {
        console.log(results); // => ["Response body of /resource/A", "Response body of /resource/B"]
    });
  ```
  - Promise APIとAsync Functionを組み合わせる  
  ```
    function dummyFetch(path) {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                if (path.startsWith("/resource")) {
                    resolve({ body: `Response body of ${path}` });
                } else {
                    reject(new Error("NOT FOUND"));
                }
            }, 1000 * Math.random());
        });
    }
    // 複数のリソースをまとめて取得する
    async function fetchAllResources(resources) {
        // リソースを同時に取得する
        const promises = resources.map(function(resource) {
            return dummyFetch(resource);
        });
        // すべてのリソースが取得できるまで待つ
        // Promise.allは [ResponseA, ResponseB] のように結果が配列となる
        const responses = await Promise.all(promises);
        // 取得した結果からレスポンスのボディだけを取り出す
        return responses.map((response) => {
            return response.body;
        });
    }
    const resources = [
        "/resource/A",
        "/resource/B"
    ];
    // リソースを取得して出力する
    fetchAllResources(resources).then((results) => {
        console.log(results); // => ["Response body of /resource/A", "Response body of /resource/B"]
    });
  ```
  - await式はAsync Functionの中でのみ利用可能

### [ES2015] Map/Set
#### Map  
マップ型のコレクションを扱うためのビルトインオブジェクト（他言語のハッシュ）
- マップの作成と初期化
```
  const map = new Map();
  console.log(map.size); // => 0
```
```
  const map = new Map([["key1", "value1"], ["key2", "value2"]]);
  // 2つのエントリーで初期化されている
  console.log(map.size); // => 2
```
- 要素の追加と取り出し  
  - setメソッド：新しい要素を追加できる
  - getメソッド：要素を取り出せる
  - hasメソッド：特定のキーにひもづいた値を持っているかを確認する
  ```
    const map = new Map();
    // 新しい要素の追加
    map.set("key", "value1");
    console.log(map.size); // => 1
    console.log(map.get("key")); // => "value1"
    // 要素の上書き
    map.set("key", "value2");
    console.log(map.get("key")); // => "value2"
    // キーの存在確認
    console.log(map.has("key")); // => true
    console.log(map.has("foo")); // => false
  ```
  - deleteメソッド：マップから要素を削除する  
  - clearメソッド：マップが持つすべての要素を削除する
  ```
    const map = new Map();
    map.set("key1", "value1");
    map.set("key2", "value2");
    console.log(map.size); // => 2
    map.delete("key1");
    console.log(map.size); // => 1
    map.clear();
    console.log(map.size); // => 0
  ```
- マップの反復処理  
マップが持つ要素を列挙するメソッドとして、forEach、keys、values、entriesがある
  - forEachメソッド：マップが持つすべての要素を、マップへの挿入順に反復処理する
  ```
    const map = new Map([["key1", "value1"], ["key2", "value2"]]);
    const results = [];
    map.forEach((value, key) => {
        results.push(`${key}:${value}`);
    });
    console.log(results); // => ["key1:value1","key2:value2"]
  ```
  - keysメソッド：マップが持つすべての要素のキーを挿入順に並べたIteratorオブジェクトを返す
  - valuesメソッド：マップが持つすべての要素の値を挿入順に並べたIteratorオブジェクトを返す
  ```
    const map = new Map([["key1", "value1"], ["key2", "value2"]]);
    const keys = [];
    // keysメソッドの返り値(Iterator)を反復処理する
    for (const key of map.keys()) {
        keys.push(key);
    }
    console.log(keys); // => ["key1","key2"]
    // keysメソッドの返り値(Iterator)から配列を作成する
    const keysArray = Array.from(map.keys());
    console.log(keysArray); // => ["key1","key2"]
  ```
  - entriesメソッド：マップが持つすべての要素をエントリーとして挿入順に並べたIteratorオブジェクトを返す
  ```
    const map = new Map([["key1", "value1"], ["key2", "value2"]]);
    const entries = [];
    for (const [key, value] of map.entries()) {
        entries.push(`${key}:${value}`);
    }
    console.log(entries); // => ["key1:value1","key2:value2"]
  ```
  - マップ自身もiterableなオブジェクト
  ```
    const map = new Map([["key1", "value1"], ["key2", "value2"]]);
    const results = [];
    for (const [key, value] of map) {
        results.push(`${key}:${value}`);
    }
    console.log(results); // => ["key1:value1","key2:value2"]
  ```
- マップとしてのObjectとMap  
以前は、JavaScriptにおいてマップ型を実現するためにObjectが利用された
  - 欠点
    - Objectのprototypeオブジェクトから継承されたプロパティによって、意図しないマッピングを生じる危険性がある
    - プロパティとしてデータを持つため、キーとして使えるのは文字列かSymbolに限られる  
  - 利点
    - リテラル表現があるため作成しやすい
    - 規定のJSON表現があるため、JSON.stringify関数を使ってJSONに変換するのが簡単である
    - ネイティブAPI・外部ライブラリを問わず、多くの関数がマップとしてObjectを渡される設計になっている
- WeakMap  
  - Mapと同じくマップを扱うためのビルトインオブジェクト
  - Mapと違う点は、キーを弱い参照（Weak Reference）で持つこと
    - 弱い参照：ガベージコレクション（GC）によるオブジェクトの解放を妨げないための特殊な参照
    ```
      const map = new WeakMap();
      // キーとなるオブジェクト
      let obj = {};
      // {} への参照をキーに値をセットする
      map.set(obj, "value");
      // {} への参照を破棄する
      obj = null;
      // GCが発生するタイミングでWeakMapから値が破棄される
    ```
  - WeakMapはMapと似ていますがiterableではない
    - キーとして使えるのは参照型のオブジェクトだけ
  - 使い方
    - クラスにプライベートの値を格納すること
    - あるオブジェクトから計算した結果を一時的に保存する用途
- [コラム] キーの等価性とNaN  
  - キーにはあらゆるオブジェクトが使える
  - 特定のキーを持っているか判定は`===`演算子と同じ
    - キーがNaNの扱いだけが例外的に違う
    - Same-value-zeroアルゴリズム：NaN同士は常に等価である
    ```
      const map = new Map();
      map.set(NaN, "value");
      // NaNは===で比較した場合は常にfalse
      console.log(NaN === NaN); // => false
      // MapはNaN同士を比較できる
      console.log(map.has(NaN)); // => true
      console.log(map.get(NaN)); // => "value"
    ```

#### Set  
セット型のコレクションを扱うためのビルトインオブジェクト  
セットとは、重複する値がないことを保証したコレクションのこと  
配列と違って要素は順序を持たず、インデックスによるアクセスはできない
- セットの作成と初期化
```
  const set = new Set();
  console.log(set.size); // => 0
```
```
  // "value2"が重複するため、片方は無視される
  const set = new Set(["value1", "value2", "value2"]);
  // セットのサイズは2になる
  console.log(set.size); // => 2
```
- 値の追加と取り出し
  - addメソッド：セットに値を追加する
  - hasメソッド：セットが特定の値を持っているかどうかを確認する
  ```
    const set = new Set();
    // 値の追加
    set.add("a");
    console.log(set.size); // => 1
    // 重複する値は追加されない
    set.add("a");
    console.log(set.size); // => 1
    // 値の存在確認
    console.log(set.has("a")); // => true
    console.log(set.has("b")); // => false
  ```
  - deleteメソッド：セットから値を削除する
  - clearメソッド：セットが持つすべての値を削除する
  ```
    const set = new Set();
    set.add("a");
    set.add("b");
    console.log(set.size); // => 2
    set.delete("a");
    console.log(set.size); // => 1
    set.clear();
    console.log(set.size); // => 0
  ```
- セットの反復処理
  - forEachメソッド：セットが持つすべての要素を、セットへの挿入順に反復する
  ```
    const set = new Set(["a", "b"]);
    const results = [];
    set.forEach((value) => {
        results.push(value);
    });
    console.log(results); // => ["a","b"]
  ```
  - valuesメソッド：値のIteratorオブジェクトを作成する
  - keysメソッド：valuesメソッドのエイリアス
  - entriesメソッド：[値, 値]という形のエントリーを挿入順に列挙するIteratorオブジェクトを返す  
  ※あまり使い道がない
  - Setオブジェクト自身もiterableなオブジェクト
  ```
    const set = new Set(["a", "b"]);
    const results = [];
    for (const value of set) {
        results.push(value);
    }
    console.log(results); // => ["a","b"]
  ```
- WeakSet  
  - 弱い参照で値を持つセット
  - iterableではないので追加した値を反復処理できない
  - 値の追加と削除、存在確認以外のことができない
  - データの一意性を確認することに特化したセット

### JSON  
- JSONとは
  - JSON：JavaScript Object Notation
  - JavaScriptのオブジェクトリテラルをベースに作られた軽量なデータフォーマット
  - 仕様はECMA-404として標準化
  - 人間にとって読み書きが容易
  - マシンにとっても簡単にパースや生成を行える形式
  ```
    {
        "object": {
            "number": 1,
            "string": "js-primer",
            "boolean": true,
            "null": null,
            "array": [1, 2, 3]
        }
    }
  ```
  - [json.orgの日本語ドキュメント](https://www.json.org/json-ja.html)
- JSONオブジェクト  
JavaScriptでJSONを扱う、ビルトインオブジェクト
- JSON文字列をオブジェクトに変換する
  - JSON.parseメソッド：引数に与えられた文字列をJSONとしてパース、結果をJavaScriptのオブジェクトとして返す関数
  ```
    // JSONはダブルクォートのみを許容するため、シングルクォートでJSON文字列を記述
    const json = '{ "id": 1, "name": "js-primer" }';
    const obj = JSON.parse(json);
    console.log(obj.id); // => 1
    console.log(obj.name); // => "js-primer"
    const json = "[1, 2, 3]";
    console.log(JSON.parse(json)); // => [1, 2, 3]
  ```
  - 与えられた文字列がJSON形式でパースできない場合：例外  
  JSON.parseメソッドは基本的にtry...catch構文で例外処理をするべき
  ```
    const userInput = "not json value";
    try {
        const json = JSON.parse(userInput);
    } catch (error) {
        console.log("パースできませんでした");
    }  
  ```
- オブジェクトをJSON文字列に変換する
  - JSON.stringifyメソッド：第一引数に与えられたオブジェクトをJSON形式の文字列に変換して返す関数
  ```
    const obj = { id: 1, name: "js-primer", bio: null };
    console.log(JSON.stringify(obj)); // => '{"id":1,"name":"js-primer","bio":null}'
  ```
  - オプショナルな引数
    - replacer引数（第二引数）：関数あるいは配列を渡せる
      - 関数：引数にプロパティのキーと値が渡され返り値で制御できる
      ```
        const obj = { id: 1, name: "js-primer", bio: null };
        const replacer = (key, value) => {
            if (value === null) {
                return undefined;
            }
            return value;
        };
        console.log(JSON.stringify(obj, replacer)); // => '{"id":1,"name":"js-primer"}'
      ```
      - 配列を渡した場合はプロパティのホワイトリスト
      ```
        const obj = { id: 1, name: "js-primer", bio: null };
        const replacer = ["id", "name"];
        console.log(JSON.stringify(obj, replacer)); // => '{"id":1,"name":"js-primer"}'
      ```
    - space引数（第三引数）  
    変換後のJSON形式の文字列を読みやすくフォーマットする際のインデントを設定
    ```
      const obj = { id: 1, name: "js-primer" };
      // replacer引数を使わない場合はnullを渡して省略するのが一般的です
      console.log(JSON.stringify(obj, null, 2));
      /*
      {
        "id": 1,
        "name": "js-primer"
      }
      */
    ```
- JSONにシリアライズできないオブジェクト
|シリアライズ前の値|シリアライズ後の値|
|---|---|
|文字列・数値・真偽値|対応する値|
|null|null|
|配列|配列|
|オブジェクト|オブジェクト|
|関数|変換されない（配列のときはnull）|
|undefined|変換されない（配列のときはnull）|
|Symbol|変換されない（配列のときはnull）|
|RegExp|{}|
|Map, Set|{}|
```
  // 値が関数のプロパティ
  console.log(JSON.stringify({ x: function() {} })); // => '{}'
  // 値がSymbolのプロパティ
  console.log(JSON.stringify({ x: Symbol("") })); // => '{}'
  // 値がundefinedのプロパティ
  console.log(JSON.stringify({ x: undefined })); // => '{}'
  // 配列の場合
  console.log(JSON.stringify({ x: [10, function() {}] })); // => '{"x":[10,null]}'
  // キーがSymbolのプロパティ
  JSON.stringify({ [Symbol("foo")]: "foo" }); // => '{}'
  // 値がRegExpのプロパティ
  console.log(JSON.stringify({ x: /foo/ })); // => '{"x":{}}'
  // 値がMapのプロパティ
  const map = new Map();
  map.set("foo", "foo");
  console.log(JSON.stringify({ x: map })); // => '{"x":{}}'
```
- toJSONメソッドを使ったシリアライズ
  - オブジェクトがtoJSONメソッドを持っている場合
    - toJSONメソッドの返り値を使う
    ```
      const obj = {
          foo: "foo",
          toJSON() {
              return "bar";
          }
      };
      console.log(JSON.stringify(obj)); // => '"bar"'
      console.log(JSON.stringify({ x: obj })); // => '{"x":"bar"}'
    ```
  - オブジェクトがtoJSONメソッドを持っていない場合
    - 既定の文字列変換

### Date
- Dateオブジェクト  
  - ビルトインオブジェクト
  - インスタンス化することで、ある特定の時刻を表すオブジェクトが得られる
- インスタンスの作成
  - new演算子
    - 現在の時刻をインスタンス化する
    ```
      // 現在の時刻を表すインスタンスを作成する
      const now = new Date();
      // 時刻値だけが欲しい場合にはDate.nowメソッドを使う
      console.log(Date.now());

      // 時刻値を取得する
      console.log(now.getTime());
      // 時刻をISO 8601形式の文字列で表示する
      console.log(now.toISOString());
    ```
    - 任意の時刻をインスタンス化する
      - 時刻値を渡すもの
      ```
        // 時刻のミリ秒値を直接指定する形式
        // 1136214245999はUTCにおける"2006年1月2日15時04分05秒999"を表す
        const date = new Date(1136214245999);
        // 末尾の'Z'はUTCであることを表す
        console.log(date.toISOString()); // => "2006-01-02T15:04:05.999Z"
      ```
      - 時刻を示す文字列を渡すもの
      ```
        // UTCにおける"2006年1月2日15時04分05秒999"を表すISO 8601形式の文字列
        const inUTC = new Date("2006-01-02T15:04:05.999Z");
        console.log(inUTC.toISOString()); // => "2006-01-02T15:04:05.999Z"

        // 上記の例とは異なり、UTCであることを表す'Z'がついていないことに注意
        // Asia/Tokyo(+09:00)で実行すると、UTCにおける表記は9時間前の06時04分05秒になる
        const inLocal = new Date("2006-01-02T15:04:05.999");
        console.log(inLocal.toISOString()); // "2006-01-02T06:04:05.999Z" (Asia/Tokyoの場合)
      ```
      - 時刻の部分（年・月・日など）をそれぞれ数値で渡すもの
      ```
        // 実行環境における"2006年1月2日15時04分05秒999"を表す
        // タイムゾーンを指定することはできない
        const date1 = new Date(2006, 0, 2, 15, 4, 5, 999);
        console.log(date1.toISOString()); // "2006-01-02T06:04:05.999Z" (Asia/Tokyoの場合)

        // Date.UTCメソッドを使うとUTCに固定できる
        const ms = Date.UTC(2006, 0, 2, 15, 4, 5, 999);
        // 時刻値を渡すコンストラクタと併用する
        const date2 = new Date(ms);
        console.log(date2.toISOString()); // => "2006-01-02T15:04:05.999Z"
      ```
- Dateのインスタンスメソッド
```
  // YYYY/MM/DD形式の文字列に変換する関数
  function formatDate(date) {
      const yyyy = String(date.getFullYear());
      // String#padStartメソッド（ES2017）で2桁になるように0埋めする
      const mm = String(date.getMonth() + 1).padStart(2, "0");
      const dd = String(date.getDate()).padStart(2, "0");
      return `${yyyy}/${mm}/${dd}`;
  }

  const date = new Date("2006-01-02T15:04:05.999");
  console.log(formatDate(date)); // => "2006/01/02"
```
- 現実のユースケースとDate
  - 多くのユースケースにおいては機能が不十分
    - 任意の書式の文字列から時刻に変換するメソッドがない
    - 「時刻を1時間進める」のように時刻を前後にずらす操作を提供するメソッドがない
    - 任意のタイムゾーンにおける時刻を計算するメソッドがない
    - YYYY/MM/DDのようなフォーマットに基づいた文字列への変換を提供するメソッドがない
  - 標準のDateではなくライブラリを使うことが一般的
    - moment.js
    - js-joda
    - date-fns

### Math
- Mathオブジェクト
  - ビルトインオブジェクト
  - コンストラクタではない
  - すべての定数や関数はMathオブジェクトの静的なプロパティやメソッドとして提供される
  ```
    const rad90 = Math.PI * 90 / 180;
    const sin90 = Math.sin(rad90);
    console.log(sin90); // => 1
  ```
- 乱数を生成する
```
  for (let i = 0; i < 5; i++) {
      // 毎回ランダムな浮動小数点数を返す
      console.log(Math.random());
  }
```
- 数値の大小を比較する
```
  console.log(Math.max(1, 10)); // => 10
  console.log(Math.min(1, 10)); // => 1
  const numbers = [1, 2, 3, 4, 5];
  console.log(Math.max(...numbers)); // => 5
  console.log(Math.min(...numbers)); // => 1
```
- 数値を整数にする
  - Math.floorメソッド：引数として渡した数以下で最大の整数を返す（床関数）
  - Math.ceilメソッド：引数として渡した数以上で最小の整数を返す（天井関数）
  - Math.roundメソッド：一般的な四捨五入の処理
  - Math.truncメソッド：渡された数字の小数点以下を単純に切り落とした整数を返す
  ```
    // 底関数
    console.log(Math.floor(1.3)); // => 1
    console.log(Math.floor(-1.3)); // => -2
    // 天井関数
    console.log(Math.ceil(1.3)); // => 2
    console.log(Math.ceil(-1.3)); // => -1
    // 四捨五入
    console.log(Math.round(1.3)); // => 1
    console.log(Math.round(1.6)); // => 2
    console.log(Math.round(-1.3)); // => -1
    // 単純に小数部分を切り落とす
    console.log(Math.trunc(1.3)); // => 1
    console.log(Math.trunc(-1.3)); // => -1
  ```

###  ECMAScriptモジュール
- ECMAScriptモジュール：JavaScriptファイルをモジュール化する言語標準の機能
  - 保守性: 依存性の高いコードの集合を一箇所にまとめ、それ以外のモジュールへの依存性を減らせます
  - 名前空間: モジュールごとに分かれたスコープがあり、グローバルの名前空間を汚染しません
  - 再利用性: 便利な変数や関数を複数の場所にコピーアンドペーストせず、モジュールとして再利用できます
  - 1つのJavaScriptモジュールは1つのJavaScriptファイルに対応
- ECMAScriptモジュールの構文
  - 名前つきエクスポート
    - `named-export.js`
    ```
      const foo = "foo";
      // 宣言済みのオブジェクトを名前つきエクスポートする
      export { foo };
    ```
    - `named-export-declare.js`
    ```
      // 宣言と同時に名前つきエクスポートする
      export function bar() { };
    ```
  - 名前つきインポート
    - `my-module.js`
    ```
      export const foo = "foo";
      export function bar() { }
    ```
    - `named-import.js`
    ```
      // 名前つきエクスポートされたfooとbarをインポートする
      import { foo, bar } from "./my-module.js";
      console.log(foo); // => "foo"
      console.log(bar); // => "bar"
    ```
  - 名前つきエクスポート／インポートのエイリアス
    - `named-export-alias.js`
    ```
      const internalFoo = "foo";
      // internalFoo変数をfooとして名前つきエクスポートする
      export { internalFoo as foo };
    ```
    - `named-import-alias.js`
    ```
      // fooとして名前つきエクスポートされた変数をmyFooとしてインポートする
      import { foo as myFoo } from "./named-export-alias.js";
      console.log(myFoo); // => "foo"
    ```
  - デフォルトエクスポート：モジュールごとに1つしかエクスポートできない特殊なエクスポート
    - `default-export.js`
    ```
      const foo = "foo";
      // foo変数の値をデフォルトエクスポートする
      export default foo;
    ```
    ```
      // 宣言と同時に関数をデフォルトエクスポートする
      export default function() {}
    ```
  - デフォルトインポート
    - `my-module.js`
    ```
      export default {
          baz: "baz"
      };
    ```
    - `default-import.js`
    ```
      // デフォルトエクスポートをmyModuleとしてインポートする
      import myModule from "./my-module.js";
      console.log(myModule); // => { baz: "baz" }
    ```
    - `default-export-alias.js`
    ```
      const foo = "foo";
      // foo変数の値をデフォルトエクスポートする
      export { foo as default };
    ```
  - デフォルトエクスポートのエイリアス
    - `default-import-alias.js`
    ```
      // デフォルトエクスポートをmyModuleとしてインポートする
      import { default as myModule } from "./my-module.js";
      console.log(myModule); // => { baz: "baz" }
    ```
- その他の構文
  - 再エクスポート：別のモジュールからインポートしたものを、改めて自分自身からエクスポートし直すこと
  ```
    // ./my-module.jsのすべての名前つきエクスポートを再エクスポートする
    export * from "./my-module.js";
    // [ES2020] ./my-module.jsのすべての名前つきエクスポートを名前空間オブジェクトとして再エクスポートする
    export * as myNameSpace from "./my-module.js";
    // ./my-module.jsの名前つきエクスポートを選んで再エクスポートする
    export { foo, bar } from "./my-module.js";
    // ./my-module.jsの名前つきエクスポートにエイリアスをつけて再エクスポートする
    export { foo as myModuleFoo, bar as myModuleBar } from "./my-module.js";
    // ./my-module.jsのデフォルトエクスポートをデフォルトエクスポートとして再エクスポートする
    export { default } from "./my-module.js";
    // ./my-module.jsのデフォルトエクスポートを名前つきエクスポートとして再エクスポートする
    export { default as myModuleDefault } from "./my-module.js";
    // ./my-module.jsの名前つきエクスポートをデフォルトエクスポートとして再エクスポートする
    export { foo as default } from "./my-module.js";
  ```
  - すべてをインポート
    - `my-module.js`
    ```
      export const foo = "foo";
      export function bar() { }
      export default {
          baz: "baz"
      };
    ```
    - `namespace-import.js`
    ```
      // すべての名前つきエクスポートをmyModuleオブジェクトとしてまとめてインポートする
      import * as myModule from "./my-module.js";
      // fooとして名前つきエクスポートされた値にアクセスする
      console.log(myModule.foo); // => "foo"
      // defaultとしてデフォルトエクスポートされた値にアクセスする
      console.log(myModule.default); // => { baz: "baz" }
    ```
  - 副作用のためのインポート
    - `ide-effects.js`
    ```
      // グローバル変数を操作する(副作用)
      window.foo = "foo";
    ```
    ```
      // ./side-effects.jsのグローバルコードが実行される
      import "./side-effects.js";
    ```
- ECMAScriptモジュールを実行する
```
  <!-- my-module.jsをECMAScriptモジュールとして読み込む -->
  <script type="module" src="./my-module.js"></script>
  <!-- インラインでも同じ -->
  <script type="module">
  import { foo } from "./my-module.js";
  </script>
```

## 第一部：ユースケース
### アプリケーション開発の準備
### ユースケース: Ajax通信
#### エントリーポイント
- HTMLファイルとjsファイルの作成
- ウェブブラウザとDOM  
HTMLドキュメントをブラウザで読み込む => DOMと呼ばれるプログラミング用のデータ表現が生成される
  -  DOM（Document Object Model）  
    - HTMLドキュメントのコンテンツと構造をJavaScriptから操作できるオブジェクト
    - DOMツリー：DOMではHTMLドキュメントのタグの入れ子関係を木構造で表現する
    - DOMは言語機能（ECMAScript）ではなくブラウザが実装しているAPI
  - `document`グローバルオブジェクト：HTMLドキュメントそのものを表現する
  ```
    // CSSセレクタを使ってDOMツリー中のh2要素を取得する
    const heading = document.querySelector("h2");
    // h2要素に含まれるテキストコンテンツを取得する
    const headingText = heading.textContent;

    // button要素を作成する
    const button = document.createElement("button");
    button.textContent = "Push Me";
    // body要素の子要素としてbuttonを挿入する
    document.body.appendChild(button);
  ```

#### HTTP通信
- Fetch API
  - Fetch API：HTTP通信を行ってリソースを取得するためのAPI
    - ページ全体を再読み込みすることなく指定したURLからデータを取得できる
    - Fetch APIは同じくHTTP通信を扱うXMLHttpRequestと似たAPIですが、より強力で柔軟な操作が可能
- Github ユーザー情報取得API
```
  const userId = "任意のGitHubユーザーID";
  fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`);
```
- レスポンスの受け取り
  - fetchメソッド：Promiseを返す
    - Promiseインスタンス：リクエストのレスポンスを表すResponseオブジェクトでresolveされる
    - 送信したリクエストにレスポンスが返却されると、thenコールバックが呼び出される
    - statusプロパティ：HTTPレスポンスのステータスコードが取得
    - jsonメソッド：HTTPレスポンスボディをJSONとしてパースしたオブジェクトでresolveされる
    ```
      const userId = "js-primer-example";
      fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
          .then(response => {
              console.log(response.status); // => 200
              return response.json().then(userInfo => {
                  // JSONパースされたオブジェクトが渡される
                  console.log(userInfo); // => {...}
              });
          });
    ```
- エラーハンドリング：HTTP通信にはエラーがつきもの
  - ネットワークエラー
    - NetworkErrorオブジェクトでrejectされたPromiseが返される
    - thenメソッドの第二引数かcatchメソッドのコールバック関数が呼び出され
    ```
      const userId = "js-primer-example";
      fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
          .then(response => {
              console.log(response.status);
              return response.json().then(userInfo => {
                  console.log(userInfo);
              });
          }).catch(error => {
              console.error(error);
          });
    ```
  - リクエストが成功
    - Responseオブジェクトのokプロパティで認識できる
    - okプロパティがfalseとなるエラーレスポンスをハンドリングできる
    ```
      const userId = "js-primer-example";
      fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
          .then(response => {
              console.log(response.status); 
              // エラーレスポンスが返されたことを検知する
              if (!response.ok) {
                  console.error("エラーレスポンス", response);
              } else {
                  return response.json().then(userInfo => {
                      console.log(userInfo);
                  });
              }
          }).catch(error => {
              console.error(error);
          });
    ```
- まとめ
```
  function fetchUserInfo(userId) {
      fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
          .then(response => {
              console.log(response.status);
              // エラーレスポンスが返されたことを検知する
              if (!response.ok) {
                  console.error("エラーレスポンス", response);
              } else {
                  return response.json().then(userInfo => {
                      console.log(userInfo);
                  });
              }
          }).catch(error => {
              console.error(error);
          });
  }
```
```
  <!DOCTYPE html>
  <html lang="ja">
    <head>
      <meta charset="utf-8" />
      <title>Ajax Example</title>
    </head>
    <body>
      <h2>GitHub User Info</h2>

      <button onclick="fetchUserInfo('js-primer-example');">Get user info</button>
      <script src="index.js"></script>
    </body>
  </html>
```
  - ページを読み込むたびにGitHubのAPIを呼び出す：呼び出し回数の制限を超える
  - HTMLドキュメント側に手動でfetchUserInfo関数を呼び出すためのボタンを追加
- [コラム] XMLHttpRequest  
  - XMLHttpRequest（XHR）：Fetch APIと同じくHTTP通信を行うためのAPI
    - Fetch APIが標準化される以前は、ブラウザとサーバーの間で通信するにはXHRを使うのが一般的
    - 上記をXHRで記述
    ```
      function fetchUserInfo(userId) {
          // リクエストを作成する
          const request = new XMLHttpRequest();
          request.open("GET", `https://api.github.com/users/${encodeURIComponent(userId)}`);
          request.addEventListener("load", () => {
              // リクエストが成功したかを判定する
              // Fetch APIのresponse.okと同等の意味
              if (request.status >= 200 && request.status < 300) {
                  // レスポンス文字列をJSONオブジェクトにパースする
                  const userInfo = JSON.parse(request.responseText);
                  console.log(userInfo);
              } else {
                  console.error("エラーレスポンス", request.statusText);
              }
          });
          request.addEventListener("error", () => {
              console.error("ネットワークエラー");
          });
          // リクエストを送信する
          request.send();
      }
    ```
      - Fetch APIはXHRを置き換えるために作られたもの
      - 多くのユースケースではXHRを使う必要はなくなっている
      - 古いブラウザではFetch APIが実装されていない
      - ブラウザの互換性を保つためにXHRを使う場面もまだある

#### データを表示する
- HTMLを組み立てる
  - HTML文字列の生成にはテンプレートリテラルを使う
    - テンプレートリテラルは文字列中の改行が可能
    - HTMLのインデントを表現できて見通しが良くなる
    - 変数の埋め込みも簡単
    - 動的なデータをあてはめるのに適する
    ```
      const view = `
      <h4>${userInfo.name} (@${userInfo.login})</h4>
      <img src="${userInfo.avatar_url}" alt="${userInfo.login}" height="100">
      <dl>
          <dt>Location</dt>
          <dd>${userInfo.location}</dd>
          <dt>Repositories</dt>
          <dd>${userInfo.public_repos}</dd>
      </dl>
      `;
    ```
- HTML文字列をDOMに追加する
  - 目印となる要素をindex.htmlに追加
  - resultというidを持ったdiv要素（以降div#resultと表記します）を配置
  ```
    <body>
      <h2>GitHub User Info</h2>

      <button onclick="fetchUserInfo('js-primer-example');">Get user info</button>
      <!-- 整形したHTMLの挿入先 -->
      <div id="result"></div>

      <script src="index.js"></script>
    </body>
  ```
  - div#result要素の子要素としてHTML文字列を挿入
  - document.getElementByIdメソッド：div#result要素が取得
  - HTML文字列をinnerHTMLプロパティにセット
  ```
    const result = document.getElementById("result");
    result.innerHTML = view;
  ```
- avaScriptによってHTML要素をDOMに追加する方法
  - HTML文字列をElement#innerHTMLプロパティにセットする方法（今回の方法）
  - 文字列ではなくElementオブジェクトを生成して手続き的にツリー構造を構築する方法  
  セキュリティ的に安全ですが、コードは少し冗長
- HTML文字列をエスケープする
  - Element#innerHTMLに文字列をセット：その文字列はHTMLとして解釈される
  - <記号や>記号が含まれていると、意図しない構造のHTMLになる可能性がある
  - 特定の記号を安全な表現に置換する必要があ
  - この処理を一般にHTMLのエスケープと呼ぶ
  - 多くのViewライブラリは内部にエスケープ機構を持っている：デフォルトでエスケープ
  - エスケープ用のライブラリを利用するケースも多い
  - 今回のように独自実装するケースは特別
  ```
    function escapeSpecialChars(str) {
        return str
            .replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;")
            .replace(/"/g, "&quot;")
            .replace(/'/g, "&#039;");
    }
  ```
  - テンプレートリテラルをタグづけする
  - 明示的にエスケープ用の関数を呼び出す必要がないようにする
  - escapeHTML関数はタグ関数
  ```
    function escapeHTML(strings, ...values) {
        return strings.reduce((result, str, i) => {
            const value = values[i - 1];
            if (typeof value === "string") {
                return result + escapeSpecialChars(value) + str;
            } else {
                return result + String(value) + str;
            }
        });
    }
  ```
  ```
    const view = escapeHTML`
    <h4>${userInfo.name} (@${userInfo.login})</h4>
    <img src="${userInfo.avatar_url}" alt="${userInfo.login}" height="100">
    <dl>
        <dt>Location</dt>
        <dd>${userInfo.location}</dd>
        <dt>Repositories</dt>
        <dd>${userInfo.public_repos}</dd>
    </dl>
    `;

    const result = document.getElementById("result");
    result.innerHTML = view;
  ```
- まとめ
  - index.js
  ```
    function fetchUserInfo(userId) {
        fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
            .then(response => {
                if (!response.ok) {
                    console.error("エラーレスポンス", response);
                } else {
                    return response.json().then(userInfo => {
                        // HTMLの組み立て
                        const view = escapeHTML`
                        <h4>${userInfo.name} (@${userInfo.login})</h4>
                        <img src="${userInfo.avatar_url}" alt="${userInfo.login}" height="100">
                        <dl>
                            <dt>Location</dt>
                            <dd>${userInfo.location}</dd>
                            <dt>Repositories</dt>
                            <dd>${userInfo.public_repos}</dd>
                        </dl>
                        `;
                        // HTMLの挿入
                        const result = document.getElementById("result");
                        result.innerHTML = view;
                    });
                }
            }).catch(error => {
                console.error(error);
            });
    }

    function escapeSpecialChars(str) {
        return str
            .replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;")
            .replace(/"/g, "&quot;")
            .replace(/'/g, "&#039;");
    }

    function escapeHTML(strings, ...values) {
        return strings.reduce((result, str, i) => {
            const value = values[i - 1];
            if (typeof value === "string") {
                return result + escapeSpecialChars(value) + str;
            } else {
                return result + String(value) + str;
            }
        });
    }
  ```
  - index.html
  ```
    <!DOCTYPE html>
    <html lang="ja">
      <head>
        <meta charset="utf-8" />
        <title>Ajax Example</title>
      </head>
      <body>
        <h2>GitHub User Info</h2>

        <button onclick="fetchUserInfo('js-primer-example');">Get user info</button>
        <!-- 整形したHTMLの挿入先 -->
        <div id="result"></div>

        <script src="index.js"></script>
      </body>
    </html>
  ```

#### Promiseを活用する
- 関数の分割
  - fetchUserInfo関数を整理
    - Fetch APIを使ったデータの取得
    - HTML文字列の組み立て => createView関数
    - 組み立てたHTMLの表示 => displayView関数
  - ndex.jsに新しくmain関数を作り、fetchUserInfo関数を呼び出す（エントリーポイント）
  ```
    function main() {
        fetchUserInfo("js-primer-example");
    }

    function fetchUserInfo(userId) {
        fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
            .then(response => {
    ...
    }

    function createView(userInfo) {
        return escapeHTML`
        <h4>${userInfo.name} (@${userInfo.login})</h4>
    ...
    }

    function displayView(view) {
        const result = document.getElementById("result");
        result.innerHTML = view;
    }
  ```
  ```
    <body>
      <h2>GitHub User Info</h2>

      <input id="userId" type="text" value="js-primer-example" />
      <button onclick="main();">Get user info</button>  // mainに変更

      <div id="result"></div>

      <script src="index.js"></script>
    </body>
  ```
- Promiseのエラーハンドリング
  - fetchUserInfo関数を変更し、Fetch APIの返り値でもあるPromiseオブジェクトをreturnする
  - main関数のほうで非同期処理の結果を扱えるようになる
  - エラーは、Promise#catchメソッドを使って一箇所で受け取れる
  - Response#okで判定していた400や500などのエラーレスポンスがそのままではmain関数でハンドリングできない
  - Promise.rejectメソッドを使ってRejectedなPromiseを返し、Promiseチェーンをエラーの状態にする
  - Promiseチェーンがエラーとなるため、main関数のcatchでハンドリングできできる
  ```
    function main() {
        fetchUserInfo("js-primer-example")
            .catch((error) => {
                // Promiseチェーンの中で発生したエラーを受け取る
                console.error(`エラーが発生しました (${error})`);
            });
    }

    function fetchUserInfo(userId) {
        // fetchの返り値のPromiseをreturnする
        return fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
            .then(response => {
                if (!response.ok) {
                    // エラーレスポンスからRejectedなPromiseを作成して返す
                    return Promise.reject(new Error(`${response.status}: ${response.statusText}`));
                } else {
                    return response.json().then(userInfo => {
                        // HTMLの組み立て
                        const view = createView(userInfo);
                        // HTMLの挿入
                        displayView(view);
                    });
                }
            });
    }
  ```
- Promiseチェーンのリファクタリング  
  - 一連の流れをPromiseチェーンで行うように変更
    - データの取得（fetchUserInfo）
    - HTMLの組み立て（createView）
    - 表示（displayView）
  - Promise#thenメソッドでつながるPromiseチェーン
    - thenに渡されたコールバック関数の返り値をそのまま次のthenへ渡す
    - コールバック関数の返り値がPromiseである場合、Promiseで解決された値を次のthenに渡す
    - 同期処理から非同期処理に変わっても次のthenが受け取る値の型は変わらない
  - Promiseチェーンを使って処理を分割する利点
    - 同期処理と非同期処理を区別せずに連鎖できること
    - 同期的に書かれた処理を後から非同期処理へと変更するのは、全体を書き換える必要があるため難しい
    - 最初から処理を分けておき、処理をthenを使ってつなぐことで、変更に強いコードを書ける
    - 処理を区切り：それぞれの関数が受け取る値の型と、返す値の型に注目するのがよい
    ```
      function main() {
          fetchUserInfo("js-primer-example")
              // ここではJSONオブジェクトで解決されるPromise
              .then((userInfo) => createView(userInfo))
              // ここではHTML文字列で解決されるPromise
              .then((view) => displayView(view))
              // Promiseチェーンでエラーがあった場合はキャッチされる
              .catch((error) => {
                  console.error(`エラーが発生しました (${error})`);
              });
      }

      function fetchUserInfo(userId) {
          return fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
              .then(response => {
                  if (!response.ok) {
                      return Promise.reject(new Error(`${response.status}: ${response.statusText}`));
                  } else {
                      // JSONオブジェクトで解決されるPromiseを返す
                      return response.json();
                  }
              });
      }
    ```
- Async Functionへの置き換え
  - Promiseチェーン：Promiseの非同期処理と同じ見た目で同期処理を記述できる
  - Async Function：同期処理と同じ見た目でPromiseの非同期処理を記述できる
    - thenメソッドによるコールバック関数の入れ子がなくなる
    - 手続き的で可読性が高いコード
    - エラーハンドリングも同期処理と同じくtry...catch構文を使うことができる
    ```
      async function main() {
          try {
              const userInfo = await fetchUserInfo("js-primer-example");
              const view = createView(userInfo);
              displayView(view);
          } catch (error) {
              console.error(`エラーが発生しました (${error})`);
          }
      }
    ```
- ユーザーIDを変更できるようにする
  - index.html
    - <input>タグを追加
    - JavaScriptから値を取得するためにuserIdというIDを付与
- まとめ
  - `index.html`
  ```
    <!DOCTYPE html>
    <html lang="ja">
      <head>
        <meta charset="utf-8" />
        <title>Ajax Example</title>
      </head>
      <body>
        <h2>GitHub User Info</h2>

        <input id="userId" type="text" value="js-primer-example" />
        <button onclick="main();">Get user info</button>

        <div id="result"></div>

        <script src="index.js"></script>
      </body>
    </html>
  ```
  - `index.js`
  ```
    async function main() {
        try {
            const userId = getUserId();
            const userInfo = await fetchUserInfo(userId);
            const view = createView(userInfo);
            displayView(view);
        } catch (error) {
            console.error(`エラーが発生しました (${error})`);
        }
    }

    function fetchUserInfo(userId) {
        return fetch(`https://api.github.com/users/${encodeURIComponent(userId)}`)
            .then(response => {
                if (!response.ok) {
                    return Promise.reject(new Error(`${response.status}: ${response.statusText}`));
                } else {
                    return response.json();
                }
            });
    }

    function getUserId() {
        return document.getElementById("userId").value;
    }

    function createView(userInfo) {
        return escapeHTML`
        <h4>${userInfo.name} (@${userInfo.login})</h4>
        <img src="${userInfo.avatar_url}" alt="${userInfo.login}" height="100">
        <dl>
            <dt>Location</dt>
            <dd>${userInfo.location}</dd>
            <dt>Repositories</dt>
            <dd>${userInfo.public_repos}</dd>
        </dl>
        `;
    }

    function displayView(view) {
        const result = document.getElementById("result");
        result.innerHTML = view;
    }

    function escapeSpecialChars(str) {
        return str
            .replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;")
            .replace(/"/g, "&quot;")
            .replace(/'/g, "&#039;");
    }

    function escapeHTML(strings, ...values) {
        return strings.reduce((result, str, i) => {
            const value = values[i - 1];
            if (typeof value === "string") {
                return result + escapeSpecialChars(value) + str;
            } else {
                return result + String(value) + str;
            }
        });
    }
  ```

### ユースケース: Node.jsでCLIアプリケーション
#### Node.jsでHello World
- Node.jsとブラウザのグローバルオブジェクト
  - Node.js：Chromeと同じV8というJavaScriptエンジンを利用
  - ECMAScriptで定義されている基本構文はブラウザと同じように使える
  - ブラウザ環境とNode.js環境では利用できるグローバルオブジェクトが違う
    - ウェブブラウザ環境のグローバルオブジェクト：windowオブジェクト
      - document
      - XMLHttpRequest
    - Node.jsのグローバルオブジェクト：globalオブジェクト
      - process
      - Buffer

#### コマンドライン引数を処理する
- processオブジェクトとコマンドライン引数
  - processオブジェクト
    - Node.js実行環境のグローバル変数のひとつ
    - 現在のNode.jsの実行プロセスについて、情報の取得と操作をするAPI
    - processオブジェクトのargvプロパティ：コマンドライン引数へのアクセスを提供
    ```
      // コンソールにコマンドライン引数を出力する
      console.log(process.argv);
    ```
    `$ node main.js one two=three four`
    ```
      [
        '/usr/local/bin/node', // Node.jsの実行プロセスのパス
        '/Users/laco/nodecli/main.js', // 実行したスクリプトファイルのパス
        'one', // 1番目の引数
        'two=three', // 2番目
        'four'  // 3番目
      ]
    ```
- コマンドライン引数をパースする
  - アプリケーションでコマンドライン引数を扱うときには、一度パースして扱いやすい値に整形するのが一般的
  - commanderというライブラリを使ってコマンドライン引数をパース
- CommonJSモジュール
  - CommonJSモジュール：Node.js環境で利用されているJavaScriptのモジュール化の仕組み
  - 基本文法で学んだECMAScriptモジュールの仕様が策定されるより前からNode.jsで使われている
  - Node.jsの標準パッケージやnpmで配布されるパッケージは、CommonJSモジュールとして提供されている
  - Node.jsのグローバル変数であるmodule変数を使って変数や関数などをエクスポートする
  ```
    module.exports = {
        foo: "foo"
    };
  ```
  - require関数：CommonJSモジュールをインポートする
  ```
    // my-module.jsモジュールをmyModuleオブジェクトとしてインポートする。
    const myModule = require("./my-module");
    console.log(myModule.foo); // => "foo"
  ```
- コマンドライン引数からファイルパスを取得する
  - `$ node main.js ./sample.md`
  ```
    // commanderモジュールをprogramオブジェクトとしてインポートする
    const program = require("commander");
    // コマンドライン引数をパースする
    program.parse(process.argv);
  ```
- まとめ
```
  // commanderモジュールをprogramとしてインポートする
  const program = require("commander");
  // コマンドライン引数をcommanderでパースする
  program.parse(process.argv);

  // ファイルパスをprogram.args配列から取り出す
  const filePath = program.args[0];
  console.log(filePath);
```

#### ファイルを読み込む
- fsモジュールを使ってファイルを読み込む
  - fsモジュール
    - Node.jsでファイルの読み書きを行うための基本的な関数を提供するモジュール
    - メソッドとして非同期形式と同期形式の両方が提供されている
    - 非同期形式の関数：常にコールバック関数を受け取る
    ```
      const fs = require("fs");

      fs.readFile("sample.md", (err, file) => {
          if (err) {
              console.error(err);
          } else {
              console.log(file);
          }
      });
    ```
    - 同期形式readFileSyncメソッド
    ```
      const fs = require("fs");

      try {
          const file = fs.readFileSync("sample.md");
      } catch (err) {
          // ファイルが読み込めないなどのエラーが発生したときに呼ばれる
      }
    ```
    - Node.jsはシングルスレッド：他の処理をブロックしにくい非同期形式のAPIを選ぶことがほとんど
- readFile関数を使う
```
  const program = require("commander");
  // fsモジュールをfsオブジェクトとしてインポートする
  const fs = require("fs");

  // コマンドライン引数からファイルパスを取得する
  program.parse(process.argv);
  const filePath = program.args[0];

  // ファイルを非同期で読み込む
  // ファイルをUTF-8として非同期で読み込む
  fs.readFile(filePath, { encoding: "utf8" }, (err, file) => {
    console.log(file);
  });
```
- エラーハンドリング
  - ファイルの読み書きは例外が発生しやすい
    - 存在の有無や権限
    - ファイルシステムの違い
    - 必ずエラーハンドリング処理を書く
    ```
      const program = require("commander");
      const fs = require("fs");

      program.parse(process.argv);
      const filePath = program.args[0];

      // ファイルを非同期で読み込む
      fs.readFile(filePath, { encoding: "utf8" }, (err, file) => {
          if (err) {
              console.error(err.message);
              // 終了ステータス 1（一般的なエラー）としてプロセスを終了する
              process.exit(1);
              return;
          }
          console.log(file);
      });
    ```

#### MarkdownをHTMLに変換する
- markedパッケージを使う
```
  const program = require("commander");
  const fs = require("fs");
  // markedモジュールをmarkedオブジェクトとしてインポートする
  const marked = require("marked");

  program.parse(process.argv);
  const filePath = program.args[0];

  fs.readFile(filePath, { encoding: "utf8" }, (err, file) => {
      if (err) {
          console.error(err.message);
          process.exit(1);
          return;
      }
      // MarkdownファイルをHTML文字列に変換する
      const html = marked(file);
      console.log(html);
  });
```
- 変換オプションを作成する
  - gfmオプション
    - GitHubにおけるMarkdownの仕様（GitHub Flavored Markdown, GFM）に合わせて変換するかを決めるオプション
    - デフォルト：true
- コマンドライン引数からオプションを受け取る
  - デフォルト設定を定義する
    - ??（Nullish coalescing演算子）を使ってデフォルトの値をセット
    ```
      // コマンドライン引数のオプションを取得する
      const options = program.opts();

      // コマンドライン引数で指定されなかったオプションにデフォルト値を上書きする
      const cliOptions = {
          gfm: options.gfm ?? false,
      };
    ```
- まとめ
```
  const program = require("commander");
  const fs = require("fs");
  const marked = require("marked");

  // gfmオプションを定義する
  program.option("--gfm", "GFMを有効にする");
  program.parse(process.argv);
  const filePath = program.args[0];

  // コマンドライン引数のオプションを取得する
  const options = program.opts();

  // コマンドライン引数で指定されなかったオプションにデフォルト値を上書きする
  const cliOptions = {
      gfm: options.gfm ?? false,
  };

  fs.readFile(filePath, { encoding: "utf8" }, (err, file) => {
      if (err) {
          console.error(err.message);
          process.exit(1);
          return;
      }
      const html = marked(file, {
          // オプションの値を使用する
          gfm: cliOptions.gfm,
      });
      console.log(html);
  });
```

#### ユニットテストを記述する
- アプリケーションをモジュールに分割する
  - `md2html.js`
  ```
    const marked = require("marked");

    module.exports = (markdown, cliOptions) => {
        return marked(markdown, {
            gfm: cliOptions.gfm,
        });
    };
  ```
  - `main.js`
  ```
    const program = require("commander");
    const fs = require("fs");
    // md2htmlモジュールをインポートする
    const md2html = require("./md2html");

    program.option("--gfm", "GFMを有効にする");
    program.parse(process.argv);
    const filePath = program.args[0];

    const cliOptions = {
        gfm: false,
        ...program.opts(),
    };

    fs.readFile(filePath, { encoding: "utf8" }, (err, file) => {
        if (err) {
            console.error(err);
            process.exit(1);
            return;
        }
        // md2htmlモジュールを使ってHTMLに変換する
        const html = md2html(file, cliOptions);
        console.log(html);
    });
  ```
- ユニットテスト実行環境を作る
  - Mocha：テスティングフレームワーク
  - `$ npm install --save-dev mocha`
  ```
    {
        ...
        "scripts": {
            "test": "mocha test/"
        },
        ...
    }
  ```
- ユニットテストを記述する
  - `test/md2html-test.js`
  ```
    const assert = require("assert");
    const fs = require("fs");
    const path = require("path");
    const md2html = require("../md2html");

    it("converts Markdown to HTML (GFM=false)", () => {
        // fs.readFileSyncは同期的にファイルを読み込むメソッド
        const sample = fs.readFileSync(path.resolve(__dirname, "./fixtures/sample.md"), { encoding: "utf8" });
        const expected = fs.readFileSync(path.resolve(__dirname, "./fixtures/expected.html"), { encoding: "utf8" });
        // 末尾の改行の有無の違いを無視するため、変換後のHTMLのスペースをtrimメソッドで削除してから比較しています
        assert.strictEqual(md2html(sample, { gfm: false }).trimEnd(), expected.trimEnd());
    });

    it("converts Markdown to HTML (GFM=true)", () => {
        const sample = fs.readFileSync(path.resolve(__dirname, "./fixtures/sample.md"), { encoding: "utf8" });
        const expected = fs.readFileSync(path.resolve(__dirname, "./fixtures/expected-gfm.html"), { encoding: "utf8" });
        // 末尾の改行の有無の違いを無視するため、変換後のHTMLのスペースをtrimメソッドで削除してから比較しています
        assert.strictEqual(md2html(sample, { gfm: true }).trimEnd(), expected.trimEnd());
    });
  ```
  - `test/fixtures/sample.md`
  ```
    # サンプルファイル

    これはサンプルです。
    https://jsprimer.net/

    - サンプル1
    - サンプル2
  ```
  - `test/fixtures/expected.html`
  ```
    <h1 id="サンプルファイル">サンプルファイル</h1>
    <p>これはサンプルです。
    https://jsprimer.net/</p>
    <ul>
    <li>サンプル1</li>
    <li>サンプル2</li>
    </ul>
  ```
  - `test/fixtures/expected-gfm.html`
  ```
    <h1 id="サンプルファイル">サンプルファイル</h1>
    <p>これはサンプルです。
    <a href="https://jsprimer.net/">https://jsprimer.net/</a></p>
    <ul>
    <li>サンプル1</li>
    <li>サンプル2</li>
    </ul>
  ```
  - test
  ```
    $ npm test
    > mocha

      ✓ converts Markdown to HTML (GFM=false)
      ✓ converts Markdown to HTML (GFM=true)

      2 passing (31ms)
  ```
- なぜユニットテストを行うのか
  - 早期にバグが発見できる
  - 安心してリファクタリングを行えるようになる
  - ユニットテストが可能な状態を保つこと自体に意味がある
    - テストしやすいコードになるよう心がけること => 適切にモジュール化する指針
  - ユニットテストには生きたドキュメントとしての側面もある

### ユースケース: Todoアプリ
#### エントリーポイント
- エントリーポイント
  - アプリケーションの中で一番最初に呼び出される部分のこと
  - TodoアプリではエントリーポイントとしてHTMLとJavaScriptの2つを用意
    - index.html: 最初に読み込まれるファイル、index.jsを読み込む
    - index.js: index.htmlから読み込まれるファイル、JavaScriptでは最初に読み込まれる
- プロジェクトディレクトリを作成
 - `todoapp`
- HTMLファイルの用意
  - `index.html`
  ```
    <!DOCTYPE html>
    <html lang="ja">
      <head>
        <meta charset="utf-8" />
        <title>Todo App</title>
      </head>
      <body>
        <h1>Todo App</h1>
        <script type="module" src="index.js"></script>
      </body>
    </html>
  ```
  - `index.js`
  ```
    console.log("index.js: loaded");
  ```
- ローカルサーバーでHTMLを確認する
- モジュールのエントリーポイントの作成
  - index.jsから別のJavaScriptファイルをモジュールとして読み込む
  - src/App.js
  ```
    console.log("App.js: loaded");
    export class App {
        constructor() {
            console.log("App initialized");
        }
    }
  ```

#### アプリの構成要素
- アプリの構成要素
  - Todoアイテムを追加する
  - Todoアイテムを更新する
  - Todoアイテムを削除する
  - Todoアイテム数（合計）の表示
- HTMLとCSSとJavaScript
  - HTML: コンテンツの構造を記述するためのマークアップ言語
  - CSS: HTMLの見た目を装飾するスタイルシート言語
  - JavaScript: インタラクションといった動作を扱うプログラミング言語
- Todoアプリの構造をHTMLで定義する
  1. CSSファイルを読み込み
  2. class属性をCSSのために指定
  3. id属性をJavaScriptのために指定
  4. TodoアプリのメインとなるTodoリスト
  5. Todoアイテム数の表示
  ```
    <!DOCTYPE html>
    <html lang="ja">
      <head>
        <meta charset="UTF-8" />
        <title>Todo App</title>
        <!-- 1. CSSファイルを読み込み -->
        <link
          href="https://jsprimer.net/use-case/todoapp/final/final/index.css"
          rel="stylesheet"
        />
      </head>
      <body>
        <!-- 2. class属性をCSSのために指定 -->
        <div class="todoapp">
          <!-- 3. id属性をJavaScriptのために指定 -->
          <form id="js-form">
            <input
              id="js-form-input"
              class="new-todo"
              type="text"
              placeholder="What need to be done?"
              autocomplete="off"
            />
          </form>
          <!-- 4. TodoアプリのメインとなるTodoリスト -->
          <div id="js-todo-list" class="todo-list">
            <!-- 動的に更新されるTodoリスト -->
          </div>
          <footer class="footer">
            <!-- 5. Todoアイテム数の表示 -->
            <span id="js-todo-count">Todoアイテム数: 0</span>
          </footer>
        </div>
        <script src="./index.js" type="module"></script>
      </body>
    </html>
  ```

#### Todoアイテムの追加を実装する
- Todoアイテムの追加
  - 入力欄にTodoアイテムのタイトルを入力する
  - 入力欄でEnterキーを押して送信する
  - TodoリストにTodoアイテムが追加される
- JavaScriptで実現するには次のことが必要
  - Todoアイテムのタイトルを取得：input要素（入力欄）から内容を取得
  - Enterキーで送信されたことを知る：form要素のsubmitイベント（送信）を監視
  - 入力内容をタイトルにしたTodoアイテムを作成：Todoリスト（#js-todo-list）にTodoアイテム要素を追加
- 入力内容をコンソールに表示する
  - form要素でEnterキーを押して送信する => submitイベントが発生
  - submitイベント => HTML要素のaddEventListenerメソッドを利用することで受け取れる
  ```
    // `id="js-form`の要素を取得
    const formElement = document.querySelector("#js-form");
    // form要素から発生したsubmitイベントを受け取る
    formElement.addEventListener("submit", (event) => {
        // イベントが発生したときに呼ばれるコールバック関数（イベントリスナー）
    });
  ```
  - イベントリスナー：イベントが発生した際に呼ばれるコールバック関数のこと
- 入力内容をTodoリストに表示する
  - HTML文字列からHTML要素を生成するユーティリティモジュールを作成
  - 可読性を上げるため
  - `src/view/html-util.js`
  ```
    export function escapeSpecialChars(str) {
        return str
            .replace(/&/g, "&amp;")
            .replace(/</g, "&lt;")
            .replace(/>/g, "&gt;")
            .replace(/"/g, "&quot;")
            .replace(/'/g, "&#039;");
    }

    /**
    * HTML文字列からHTML要素を作成して返す
    * @param {string} html 
    */
    export function htmlToElement(html) {
        const template = document.createElement("template");
        template.innerHTML = html;
        return template.content.firstElementChild;
    }

    /**
    * HTML文字列からDOM Nodeを作成して返すタグ関数
    * @return {Element}
    */
    export function element(strings, ...values) {
        const htmlString = strings.reduce((result, str, i) => {
            const value = values[i - 1];
            if (typeof value === "string") {
                return result + escapeSpecialChars(value) + str;
            } else {
                return result + String(value) + str;
            }
        });
        return htmlToElement(htmlString);
    }

    /**
    * コンテナ要素の中身をbodyElementで上書きする
    * @param {Element} bodyElement コンテナ要素の中身となる要素
    * @param {Element} containerElement コンテナ要素
    */
    export function render(bodyElement, containerElement) {
        // containerElementの中身を空にする
        containerElement.innerHTML = "";
        // containerElementの直下にbodyElementを追加する
        containerElement.appendChild(bodyElement);
    }
  ```

#### イベントとモデル
- 直接DOMを更新する問題
  - 状態がDOM上にしか存在しない
  - HTML要素は文字列しか扱えないため、Todoアイテムのデータを文字列にしないといけないという制限が発生
  - 1つの操作に対して複数の箇所の表示が更新されることもある
|機能|操作|表示|
|---|---|---|
|Todoアイテムの追加|フォームを入力して送信|Todoリスト（#js-todo-list）にTodoアイテム要素を作成して子要素として追加。合わせてTodoアイテム数（#js-todo-count）を更新|
|Todoアイテムの更新|チェックボックスをクリック|Todoリスト（#js-todo-list）にある指定したTodoアイテム要素のチェック状態を更新|
|Todoアイテムの削除|削除ボタンをクリック|Todoリスト（#js-todo-list）にある指定したTodoアイテム要素を削除。合わせてTodoアイテム数（#js-todo-count）を更新|
  - 問題点
    - Todoリストの状態がDOM上にしか存在しないため、状態をすべてDOM上に文字列で埋め込まないといけない
    - 操作に対して更新する表示箇所が増えてくると、表示の処理が複雑化する
- モデルを導入する
  - モデル：モノの状態や操作方法を定義したオブジェクト
  - クラスでモデルを表現
    - 操作方法はメソッドとして実装
    - 状態はインスタンスのプロパティで管理
|機能|操作|モデルの処理|表示|
|---|---|---|---|
|Todoアイテムの追加|フォームを入力して送信|TodoListModelへ新しいTodoItemModelを追加|TodoListModelを元に表示を更新|
|Todoアイテムの更新|チェックボックスをクリック|TodoListModelの指定したTodoItemModelの状態を更新|TodoListModelを元に表示を更新|
|Todoアイテムの削除|削除ボタンをクリック|TodoListModelから指定のTodoItemModelを削除|TodoListModelを元に表示を更新|
  - Todoリストの状態がDOM上にしか存在しないため、状態をすべてDOM上に文字列で埋め込まないといけない
    - クラスのインスタンスを参照：Todoアイテムの情報
    - 文字列ではない情報も保持
  - 操作に対して更新する表示箇所が増えてくると、表示の処理が複雑化する
    - モデルの状態を元にしてHTML要素を作成し、表示を更新
    - モデルの状態が変化したタイミングで表示を更新すればよい
- モデルの変化を伝えるイベント
  - EventEmitter
    - ディスパッチ側: emitメソッドは、指定されたイベント名に登録済みのすべてのコールバック関数を呼び出す
    - リッスン側: addEventListenerメソッドは、指定したイベント名に任意のコールバック関数を登録できる
    ```
      export class EventEmitter {
          constructor() {
              // 登録する [イベント名, Set(リスナー関数)] を管理するMap
              this._listeners = new Map();
          }

          /**
          * 指定したイベントが実行されたときに呼び出されるリスナー関数を登録する
          * @param {string} type イベント名
          * @param {Function} listener イベントリスナー
          */
          addEventListener(type, listener) {
              // 指定したイベントに対応するSetを作成しリスナー関数を登録する
              if (!this._listeners.has(type)) {
                  this._listeners.set(type, new Set());
              }
              const listenerSet = this._listeners.get(type);
              listenerSet.add(listener);
          }

          /**
          * 指定したイベントをディスパッチする
          * @param {string} type イベント名
          */
          emit(type) {
              // 指定したイベントに対応するSetを取り出し、すべてのリスナー関数を呼び出す
              const listenerSet = this._listeners.get(type);
              if (!listenerSet) {
                  return;
              }
              listenerSet.forEach(listener => {
                  listener.call(this);
              });
          }

          /**
          * 指定したイベントのイベントリスナーを解除する
          * @param {string} type イベント名
          * @param {Function} listener イベントリスナー
          */
          removeEventListener(type, listener) {
              // 指定したイベントに対応するSetを取り出し、該当するリスナー関数を削除する
              const listenerSet = this._listeners.get(type);
              if (!listenerSet) {
                  return;
              }
              listenerSet.forEach(ownListener => {
                  if (ownListener === listener) {
                      listenerSet.delete(listener);
                  }
              });
          }
      }
    ```
  - 親クラス（EventEmitter）: イベントをディスパッチ：コールバック関数（イベントリスナー）を呼び出すクラス
  - 子クラス（TodoListModel）: 値を更新したとき、登録されているコールバック関数を呼び出すクラス
- EventEmitterを継承したTodoListモデル
  - `src/model/TodoItemModel.js`
  ```
    // ユニークなIDを管理する変数
    let todoIdx = 0;

    export class TodoItemModel {
        /**
        * @param {string} title Todoアイテムのタイトル
        * @param {boolean} completed Todoアイテムが完了済みならばtrue、そうでない場合はfalse
        */
        constructor({ title, completed }) {
            // idは自動的に連番となりそれぞれのインスタンスごとに異なるものとする
            this.id = todoIdx++;
            this.title = title;
            this.completed = completed;
        }
    }
  ```
  - `src/model/TodoListModel.js`
  ```
    import { EventEmitter } from "../EventEmitter.js";

    export class TodoListModel extends EventEmitter {
        /**
        * @param {TodoItemModel[]} [items] 初期アイテム一覧（デフォルトは空の配列）
        */
        constructor(items = []) {
            super();
            this.items = items;
        }

        /**
        * TodoItemの合計個数を返す
        * @returns {number}
        */
        getTotalCount() {
            return this.items.length;
        }

        /**
        * 表示できるTodoItemの配列を返す
        * @returns {TodoItemModel[]}
        */
        getTodoItems() {
            return this.items;
        }

        /**
        * TodoListの状態が更新されたときに呼び出されるリスナー関数を登録する
        * @param {Function} listener
        */
        onChange(listener) {
            this.addEventListener("change", listener);
        }

        /**
        * 状態が変更されたときに呼ぶ。登録済みのリスナー関数を呼び出す
        */
        emitChange() {
            this.emit("change");
        }

        /**
        * TodoItemを追加する
        * @param {TodoItemModel} todoItem
        */
        addTodo(todoItem) {
            this.items.push(todoItem);
            this.emitChange();
        }
    }
  ```
- モデルを使って表示を更新する
  1. TodoListの初期化
  2. TodoListModelの状態が更新されたら表示を更新する
  3. フォームを送信したら、新しいTodoItemを追加する
  - src/App.js
  ```
    import { TodoListModel } from "./model/TodoListModel.js";
    import { TodoItemModel } from "./model/TodoItemModel.js";
    import { element, render } from "./view/html-util.js";

    export class App {
        constructor() {
            // 1. TodoListの初期化
            this.todoListModel = new TodoListModel();
        }
        mount() {
            const formElement = document.querySelector("#js-form");
            const inputElement = document.querySelector("#js-form-input");
            const containerElement = document.querySelector("#js-todo-list");
            const todoItemCountElement = document.querySelector("#js-todo-count");
            // 2. TodoListModelの状態が更新されたら表示を更新する
            this.todoListModel.onChange(() => {
                // TodoリストをまとめるList要素
                const todoListElement = element`<ul />`;
                // それぞれのTodoItem要素をtodoListElement以下へ追加する
                const todoItems = this.todoListModel.getTodoItems();
                todoItems.forEach(item => {
                    const todoItemElement = element`<li>${item.title}</li>`;
                    todoListElement.appendChild(todoItemElement);
                });
                // containerElementの中身をtodoListElementで上書きする
                render(todoListElement, containerElement);
                // アイテム数の表示を更新
                todoItemCountElement.textContent = `Todoアイテム数: ${this.todoListModel.getTotalCount()}`;
            });
            // 3. フォームを送信したら、新しいTodoItemModelを追加する
            formElement.addEventListener("submit", (event) => {
                event.preventDefault();
                // 新しいTodoItemをTodoListへ追加する
                this.todoListModel.addTodo(new TodoItemModel({
                    title: inputElement.value,
                    completed: false
                }));
                inputElement.value = "";
            });
        }
    }
  ```

#### Todoアイテムの更新と削除を実装する
- Todoアイテムの更新
  - TodoListModelに指定したTodoアイテムの更新処理を追加する
  - チェックボックスのchangeイベントが発生したら、モデルの状態を更新する
- TodoListModelに指定したTodoアイテムの更新処理を追加する
- チェックボックスのchangeイベントが発生したら、Todoアイテムの完了状態を更新する
- 削除機能
  - 表示には削除ボタンを追加
  - 削除ボタンがクリックされたときに指定したTodoアイテムを削除する処理を呼び出す
- TodoListModelに指定したTodoアイテムを削除する処理を追加する
- 削除ボタンのclickイベントが発生したら、Todoアイテムを削除する

#### Todoアプリのリファクタリング
- App.jsを見てみるとほとんどがHTML要素の処理
  - 肥大化してコードが読みにくくなる
  - メンテナンス性が低下
- App.jsの役割
  - Modelの初期化やHTML要素とModel間で発生するイベントを中継する役割
  - HTML要素を作成する処理を別のクラスへ切り出す
- Viewコンポーネント
  - TodoアイテムViewコンポーネント
  - TodoアイテムをリストとしてまとめたTodoリストViewコンポーネント
- TodoItemViewを作成する
  - `src/view/TodoItemView.js`
  ```
    import { element } from "./html-util.js";

    export class TodoItemView {
        /**
        * `todoItem`に対応するTodoアイテムのHTML要素を作成して返す
        * @param {TodoItemModel} todoItem
        * @param {function({id:string, completed: boolean})} onUpdateTodo チェックボックスの更新イベントリスナー
        * @param {function({id:string})} onDeleteTodo 削除ボタンのクリックイベントリスナー
        * @returns {Element}
        */
        createElement(todoItem, { onUpdateTodo, onDeleteTodo }) {
            const todoItemElement = todoItem.completed
                ? element`<li><input type="checkbox" class="checkbox" checked>
                                        <s>${todoItem.title}</s>
                                        <button class="delete">x</button>
                                    </li>`
                : element`<li><input type="checkbox" class="checkbox">
                                        ${todoItem.title}
                                        <button class="delete">x</button>
                                    </li>`;
            const inputCheckboxElement = todoItemElement.querySelector(".checkbox");
            inputCheckboxElement.addEventListener("change", () => {
                // コールバック関数に変更
                onUpdateTodo({
                    id: todoItem.id,
                    completed: !todoItem.completed
                });
            });
            const deleteButtonElement = todoItemElement.querySelector(".delete");
            deleteButtonElement.addEventListener("click", () => {
                // コールバック関数に変更
                onDeleteTodo({
                    id: todoItem.id
                });
            });
            // 作成したTodoアイテムのHTML要素を返す
            return todoItemElement;
        }
    }
  ```
- TodoListViewを作成する
  - `src/view/TodoListView.js`
  ```
    import { element } from "./html-util.js";
    import { TodoItemView } from "./TodoItemView.js";

    export class TodoListView {
        /**
        * `todoItems`に対応するTodoリストのHTML要素を作成して返す
        * @param {TodoItemModel[]} todoItems TodoItemModelの配列
        * @param {function({id:string, completed: boolean})} onUpdateTodo チェックボックスの更新イベントリスナー
        * @param {function({id:string})} onDeleteTodo 削除ボタンのクリックイベントリスナー
        * @returns {Element} TodoItemModelの配列に対応したリストのHTML要素
        */
        createElement(todoItems, { onUpdateTodo, onDeleteTodo }) {
            const todoListElement = element`<ul />`;
            // 各TodoItemモデルに対応したHTML要素を作成し、リスト要素へ追加する
            todoItems.forEach(todoItem => {
                const todoItemView = new TodoItemView();
                const todoItemElement = todoItemView.createElement(todoItem, {
                    onDeleteTodo,
                    onUpdateTodo
                });
                todoListElement.appendChild(todoItemElement);
            });
            return todoListElement;
        }
    }
  ```
- Appのリファクタリング
  - TodoListView#createElementメソッド
    - onUpdateTodoのコールバック関数ではTodoListModel#updateTodoメソッドを呼ぶ
    - onDeleteTodoのコールバック関数ではTodoListModel#deleteTodoメソッドを呼ぶ
    - `src/App.js`
    ```
      import { TodoListModel } from "./model/TodoListModel.js";
      import { TodoItemModel } from "./model/TodoItemModel.js";
      import { TodoListView } from "./view/TodoListView.js";
      import { render } from "./view/html-util.js";

      export class App {
          constructor() {
              this.todoListModel = new TodoListModel();
          }

          mount() {
              const formElement = document.querySelector("#js-form");
              const inputElement = document.querySelector("#js-form-input");
              const containerElement = document.querySelector("#js-todo-list");
              const todoItemCountElement = document.querySelector("#js-todo-count");
              this.todoListModel.onChange(() => {
                  const todoItems = this.todoListModel.getTodoItems();
                  const todoListView = new TodoListView();
                  // todoItemsに対応するTodoListViewを作成する
                  const todoListElement = todoListView.createElement(todoItems, {
                      // Todoアイテムが更新イベントを発生したときに呼ばれるリスナー関数
                      onUpdateTodo: ({ id, completed }) => {
                          this.todoListModel.updateTodo({ id, completed });
                      },
                      // Todoアイテムが削除イベントを発生したときに呼ばれるリスナー関数
                      onDeleteTodo: ({ id }) => {
                          this.todoListModel.deleteTodo({ id });
                      }
                  });
                  render(todoListElement, containerElement);
                  todoItemCountElement.textContent = `Todoアイテム数: ${this.todoListModel.getTotalCount()}`;
              });
              formElement.addEventListener("submit", (event) => {
                  event.preventDefault();
                  this.todoListModel.addTodo(new TodoItemModel({
                      title: inputElement.value,
                      completed: false
                  }));
                  inputElement.value = "";
              });
          }
      }
    ```
- Appのイベントリスナーを整理する
|イベントの流れ|リスナー関数|役割|
|---|---|---|
|Model → View|this.todoListModel.onChange(listener)|TodoListModelが変更イベントを受け取る|
|View → Model|formElement.addEventListener("submit", listener)|フォームの送信イベントを受け取る|
|View → Model|onUpdateTodo: listener|Todoアイテムのチェックボックスの更新イベントを受け取る|
|View → Model|onDeleteTodo: listener|Todoアイテムの削除イベントを受け取る|
  - リスナー関数をhandleメソッドとして実装
  - `src/App.js`
  ```
    import { render } from "./view/html-util.js";
    import { TodoListView } from "./view/TodoListView.js";
    import { TodoItemModel } from "./model/TodoItemModel.js";
    import { TodoListModel } from "./model/TodoListModel.js";

    export class App {
        constructor() {
            this.todoListView = new TodoListView();
            this.todoListModel = new TodoListModel([]);
        }

        /**
        * Todoを追加するときに呼ばれるリスナー関数
        * @param {string} title
        */
        handleAdd(title) {
            this.todoListModel.addTodo(new TodoItemModel({ title, completed: false }));
        }

        /**
        * Todoの状態を更新したときに呼ばれるリスナー関数
        * @param {{ id:number, completed: boolean }}
        */
        handleUpdate({ id, completed }) {
            this.todoListModel.updateTodo({ id, completed });
        }

        /**
        * Todoを削除したときに呼ばれるリスナー関数
        * @param {{ id: number }}
        */
        handleDelete({ id }) {
            this.todoListModel.deleteTodo({ id });
        }

        mount() {
            const formElement = document.querySelector("#js-form");
            const inputElement = document.querySelector("#js-form-input");
            const todoItemCountElement = document.querySelector("#js-todo-count");
            const containerElement = document.querySelector("#js-todo-list");
            this.todoListModel.onChange(() => {
                const todoItems = this.todoListModel.getTodoItems();
                const todoListElement = this.todoListView.createElement(todoItems, {
                    // Appに定義したリスナー関数を呼び出す
                    onUpdateTodo: ({ id, completed }) => {
                        this.handleUpdate({ id, completed });
                    },
                    onDeleteTodo: ({ id }) => {
                        this.handleDelete({ id });
                    }
                });
                render(todoListElement, containerElement);
                todoItemCountElement.textContent = `Todoアイテム数: ${this.todoListModel.getTotalCount()}`;
            });

            formElement.addEventListener("submit", (event) => {
                event.preventDefault();
                this.handleAdd(inputElement.value);
                inputElement.value = "";
            });
        }
    }
  ```