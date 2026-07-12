# メタプログラミングRuby
---
## 注意点
- 大いなる力には大いなる責任が伴う
- メタプログラミングはただのプログラミング
---
## 1. オブジェクト、クラス、モジュール

### 1-1. オブジェクト、クラス、モジュールの関係
- オブジェクト：インスタンス変数＋クラスへの参照
- メソッド：クラスに存在する
- クラス：オブジェクト、クラスのクラスは「Classクラス」
- Classクラス：Moduleを継承している（Moduleがsuperclass）
- クラス、モジュール：定数 → 「::」でpathを指定可能

```
         Object ←  Module
 obj1 ←┐   ↑  └──->┐ ↑
 obj2 ←┼ MyClass → Class-┐
 obj3 ←┘ メソッド      └<┘
 @変数
```

### 1-2. スコープゲート(Scope Gate)
class、module、def キーワードでスコープを区切る。
```
a = 1

def my_method
  b = 1
  p defined? a  #=> nil
  p defined? b  #=> "local-variable"
end
my_method

class MyClass
  c = 1
  p defined? a  #=> nil
  p defined? b  #=> nil
  p defined? c  #=> "local-variable"
end
MyClass.new

module Mymodule
  d = 1
  p defined? a  #=> nil
  p defined? b  #=> nil
  p defined? c  #=> nil
  p defined? d  #=> "local-variable"
end

p defined? a    #=> "local-variable"
p defined? b    #=> nil
p defined? c    #=> nil
p defined? d    #=> nil
```

### 1-3. クラスとモジュールの継承とメソッド探索
- メソッド探索：下図の矢印のように行われる（メソッドが見つかったら実行）
- 多重include/prepend：無視される
- あとからinclude/prepend：より下位の層でinclude/prependはしない（Ruby2.xは無視、3.0以降は可も挙動に問題の可能性）
- トップレベル：self #=> main となる
- メソッド呼び出し：レシーバがself
- Module/Class定義：そのModule/Classがself
- インスタンス変数：常にselfのインスタンス変数
- レシーバを明示的に指定しないメソッド呼び出し：selfのメソッド

```
        BasicObject
        ↑
        Kernel
        ↑<="include Kernel"
        Object
        ↑
        M1
        ↑<="include M1"
        C1
        ↑<="prepend M2"
        M2
        ↑
 obj → D1
```

### 1.4. クラスと特異クラスの継承
- より上層に特異クラス（「#」で明示）が出現する

```
                     Class
                     ↑
       BasicObject → #BasicObject
       ↑             ↑
       Object -----→ #Object
       ↑             ↑
       C1 ---------→ #C1
       ↑             ↑
       D1 ---------→ #D1
       ↑
obj → #obj
```
---
## 2. 既存のクラスの修正

### 2-1. オープンクラス(Open Class)
既存クラスをを修正する。
```
class String
  def my_method
    "私のメソッド"
  end
end

"abc".my_method #=> "私のメソッド"
```

### 2-2. モンキーパッチ(Monkeypatch)
既存のクラスの振る舞いを変更する。
- 影響力が大きく、オープンクラスの弱点にもなる。
- 使用する場合は慎重に。まずは、既存のメソッドが使えないかを検討すること。

```
"abc".reverse #=> "cba"

class String
  def reverse
    "オーバーライド"
  end
end

"abc".reverse #=> "オーバーライド"
```

### 2-3. ネームスペース(Namespace)
定数をモジュール内に定義して、名前の衝突を回避する。
- 有効範囲限定のテクニック

```
module MyNamespace
  class Array
    def to_s
      "私のクラス"
    end
  end
end

Array.new.to_s              #=> "[]"
MyNamespace::Array.new.to_s #=> "私のクラス"
```

### 2-4. Refinements
ファイルの終わり、あるいはモジュールのincludeが有効な範囲まで、クラスにパッチを当てる。
- 有効範囲限定のテクニック

```
module MyRefinements
  refine String do
    def reverse
      "my reverse"
    end
  end
end

"abc".reverse #=> "cba"

using MyRefinements
"abc".reverse #=> "my reverse"
```

### 2-5. カーネルメソッド(Kernel Method)
すべてのオブジェクトで使えるように、Kernelモジュールにメソッドを定義する。
```
module Kernel
  def a_method
    "カーネルメソッド"
  end
end

a_method #=> "カーネルメソッド"
```

---
## 3. 動的にメソッドを定義
大きく2つの方法論がある。
- 動的メソッド＋動的ディスパッチ
- ゴーストメソッド（複雑なバグを引き起こす可能性もあり）

可能であれば動的メソッドを使い、方法がなければゴーストメソッドを使うのが良い。

### 3-1. 動的メソッド(Dynamic Method)
実行時にメソッドの定義方法を決める。
```
class C
end

C.class_eval do
  define_method :my_method do
    "動的メソッド"
  end
end

obj = C.new
obj.my_method #=> "動的メソッド"
```

### 3-2. 動的ディスパッチ(Dynamic Dispatch)
実行時に呼び出すメソッドを決める。
```
method_to_call = :reverse
obj = "abc"

obj.send(method_to_call) #=> "cba"
```

### 3-3. ゴーストメソッド(Ghost Method)
該当するメソッドのないメッセージに応答する。
- respond_to?では、ゴーストメソッドは認識できない。→ respond_to_missing?をオーバーライド

```
class C
  def method_missing(name, *args)
    name.to_s.reverse
  end
end

obj = C.new
obj.my_ghost_method #=> "dohtem_tsohg_ym"
```

### 3-4. 動的プロキシ(Dynemic Proxy)
メソッド呼び出しを動的に他のオブジェクトに転送する。
```
class MyDynamicProxy
  def initialize(target)
    @target = target
  end

  def method_missing(name, *args, &block)
    "result: #{@target.send(name, *args, &block)}"
  end
end

obj = MyDynamicProxy.new("a string")
obj.reverse #=> "result: gnirs a"
```

### 3-5. ブランクスレート(Blank Slate)
オブジェクトからメソッドを削除して、すべてゴーストメソッドにする。
- BasicObjectから継承にする
- この手法は動的プロキシでも有効

```
class C
  def method_missing(name, *args)
    "ゴーストメソッド"
  end
end

obj = C.new
obj.to_s #=> "#<C:0x00005624c0839f88>"

class D < BasicObject
  def method_missing(name, *args)
    "ゴーストメソッド"
  end
end

obj = D.new
obj.to_s #=> "ゴーストメソッド"
```
---
## 4. ブロック
- ブロック：クロージャ（コードと束縛の集まり）
- スコープゲートを飛び越えて、束縛に忍び込みたいときにブロックが使える

### 4-1. フラットスコープ(Flat Scope)
クロージャを使って、2つのスコープで変数を共有する。
```
class C
  def an_attribute
    @attr
  end
end

obj = C.new
a_variable = 100
obj.an_attribute #=> nil

#flat scope:
obj.instance_eval do
  @attr = a_variable
end
obj.an_attribute #=> 100
```

### 4-2. 共有スコープ(Shared Scope)
同じフラットスコープの複数のコンテキストで変数を共有する。
```
lambda {
  shared = 10
  self.class.class_eval do
    define_method :counter do
      shared
    end
    define_method :down do
      shared -= 1
    end
  end
}.call

counter #=> 10
3.times { down }
counter #=> 7
```

### 4-3. コンテキスト探査機(Context Probe)
オブジェクトのコンテキストにある情報にアクセスするためにブロックを実行する。
- カプセル化の破壊が可能：irb、テストなどで有用

```
class C
  def initialize
    @x = "privateインスタンス変数"
  end
end

obj = C.new
obj.instance_eval { @x } #=> "privateインスタンス変数"
```

### 4-4. クリーンルーム(Clean Room)
ブロックを評価する環境としてオブジェクトを使う。
- BasicObjectのインスタンスが最適（ブランクスレート）

```
class CleanRoom
  def a_useful_method(x)
    x * 2
  end
end

CleanRoom.new.instance_eval { a_useful_method(3) } #=> 6
```

## Proc、lambda
- Proc：ブロックがオブジェクトになったもの
- lambda：Procの変形
- Proc、lambdaの違い：Procの機能が必要でなければ、lambdaの方が扱い易い
1. returnの違い lambda→lambdaから戻る、proc→定義されたスコープから戻る
2. 引数の項数について lambda→厳格、proc→寛容

### 4-5. あとで評価(Deferred Evaluation)
Procやlambdaにコードとコンテキストを保管して、あとで評価する。
```
class C
  def store(&block)
    @my_code_capsule = block
  end

  def execute
    @my_code_capsule.call
  end
end

obj = C.new
obj.store { $X = 1 }
$X = 0
$X #=> 0
obj.execute
$X #=> 1
```

## メソッド

### 4-6. Methodオブジェクト
メソッドそのものをMethodオブジェクトとして取得できる。
```
class MyClass
  def initialize(value)
    @x = value
  end

  def my_method
    @x
  end
end

object = MyClass.new(1)
m = object.method :my_method
m.call #=> 1
```

### 4-7. UnboundMethod
クラスやモジュールから切り離されたようなメソッド。（特殊）
```
module MyModule
  def my_method
    42
  end
end

unbound = MyModule.instance_method(:my_method)
unbound.class #=> UnboundMethod
String.send :define_method, :another_method, unbound
"abc".another_method #=> 42
```
---
## 5. クラス定義

### 5-1. クラスインスタンス変数(Class Instance Variable)
クラスの状態をClassオブジェクトのインスタンス変数に格納する。
```
class C
  @my_class_instance_variable = "何らかの値"

  def self.class_attribute
    @my_class_instance_variable
  end
end

C.class_attribute #=> "何らかの値"
```

クラスに変数を保存したいなら「クラス変数：@@name」を使う。

### 5-2. 特異メソッド(Singleton Method)
特定のオブジェクトにメソッドを定義する。
```
obj = "abc"

class << obj
  def my_singleton_method
    "x"
  end
end

obj                     #=> "abc"
obj.my_singleton_method #=> "x"
```

### 5-3. クラスマクロ(Class Macro)
クラスの定義のなかでクラスメソッドを使う。
attr_* が代表例
```
class C; end

class << C
  def my_macro(arg)
    "my_macro(#{arg}) called"
  end
end

class C
  my_macro :x #=> "my_macro(x) called"
end
```

### 5-4. オブジェクト拡張(Object Extension)
オブジェクトの特異クラスにモジュールをincludeして、特異メソッドを定義する。
```
obj = Object.new

module M
  def my_method
    "特異メソッド"
  end
end

class << obj
  include M
end

obj.my_method #=> "特異メソッド"
```

### 5-5. クラス拡張(Class Extension)
クラスの特異クラスにモジュールをインクルードして、クラスメソッドを定義する
（オブジェクト拡張の特殊ケース）
```
class C; end

module M
  def my_method
    "クラスメソッド"
  end
end

class << C
  include M
end

C.my_method #=> "クラスメソッド"
```

## メソッドラッパー
メソッドの中にメソッドをラップする方法。

### 5-6. アラウンドエイリアス(Around Alias)
再定義したメソッドから以前のメソッドをエイリアスで呼び出す。
```
class String
  alias_method :old_reverse, :reverse

  def reverse
    "x#{old_reverse}x"
  end
end

"abc".reverse #=> "xcbax"
```

### 5-7. Refinementsラッパー(Refinements Wrapper)
Refinementsしたほうから、Refinementsしていないメソッドを呼び出す。
```
module StringRefinements
  refine String do
    def reverse
      "x#{super}x"
    end
  end
end

using StringRefinements
"abc".reverse #=> "xcbax"
```

### 5-8. Prependラッパー(Prepend Wrapper) ←これが綺麗な手法！
プリペンドした側からメソッドを呼び出す。
```
module M
  def reverse
    "x#{super}x"
  end
end

String.class_eval do
  prepend M
end

"abc".reverse #=> "xcbax"
```
---

## 6. コードを記述するコード
コードはただの文字列なため、そのまま処理させることも可能である。
- コードインジェクション攻撃の危険がある
- $SAFE：セーフレベルは完全に安全でなく、Ruby2.7で廃止されている

### 6-1. コード文字列(String of Code)
文字列のRubyコードを評価する。
```
my_string_of_code = "1 + 1"
p eval(my_string_of_code)   #=> 2
```

### 6-2. コードプロセッサ(Code Processor)
外部ソース等、コード文字列を処理する。
```
File.readlines("a_file_containing_lines_of_ruby.txt").each do |line|
  puts "#{line.chomp} ==> #{eval(line)}"
end

#=> 1 + 1 ==> 2
#=> 3 * 2 ==> 6
#=> Math.log10(100) ==> 2.0
```

### 6-3. サンドボックス(Sandbox) ←$SAFEはRuby2.7で廃止されている
信頼できないコードを安全な環境で実行する。
※Ruby3以降は$SAFEはただのグローバル変数になりました。（安全ではないです）
```
def sandbox(&code)
  proc {
    $SAFE = 2 #=> セーフレベル廃止
    yield
  }.call
end
begin
  sandbox { File.delete "a_file" }
rescue Exception => ex
  p ex 
end
```

### 6-4. フックメソッド(Hook Method)
メソッドをオーバーライドして、オブジェクトモデルのイベントを捕まえる。
```
$INHERITORS = []
class C
  def self.inherited(subclass)
    $INHERITORS << subclass
  end
end

class D < C
end

class E < C
end

class F < C
end

p $INHERITORS #=> [D, E, F]
```
---

## 7. よくあるイディオム

### 7-1. ミミックメソッド(Mimic Method)
他の言語要素に偽装したメソッド
```
def BaseClass(name)
  name == "string" ? String : Object
end

class C < BaseClass "string"  #=> クラスのように見えるメソッド
  attr_accessor :an_attribute #=> キーワードのように見えるメソッド
end

obj = C.new
obj.an_attribute = 1 #        => アトリビュートのように見えるメソッド
```

### 7-2. nilガード(Nil Guard)
nilへの参照をOR演算子で置き換える
```
x = nil
y = x || "何らかの値"　#=> "何らかの値"
```

### 7-3. 遅延インスタンス変数(Lazy Instance Variable)
最初にアクセスされるまでインスタンス変数を初期化しない
```
class C
  def attribute
    @attribute = @attribute || "何らかの値"
  end
end

obj = C.new
obj.instance_eval do
  p @attr #=> nil
end
p obj.attribute #=> "何らかの値"
```

### 7-4. 自己yeild(Self Yeild)
ブロックにselfを渡す。
```
class Person
  attr_accessor :name, :surname

  def initialize
    yield self
  end
end

joe = Person.new do |p|
  p.name = "Joe"
  p.surname = "Smith"
end

p joe.name    #=> "Joe"
p joe.surname #=> "Smith"
```

### 7-5. SymbolのProc変換(Symbol To Proc)
ひとつのメソッドを呼び出すブロックにシンボルを変換する。
```
p [*1..4].map(&:even?)  #=> [false, true, false, true]
```
---