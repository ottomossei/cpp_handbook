# C++(14)基礎知識
参考：  
 - [Google C++ スタイルガイド 日本語全訳](https://ttsuki.github.io/styleguide/cppguide.ja.html)
 - Effective Modern C++ (書籍)
 - C++のためのAPIデザイン (書籍)

## 目次
- [C++(14)基礎知識](#c14基礎知識)
  - [目次](#目次)
- [値とポインタと参照](#値とポインタと参照)
  - [簡易早見表](#簡易早見表)
  - [ポインタ](#ポインタ)
    - [constポインタ](#constポインタ)
    - [ダブルポインタ](#ダブルポインタ)
    - [(生)ポインタの欠点](#生ポインタの欠点)
      - [ダングリングポインタ](#ダングリングポインタ)
      - [二重開放](#二重開放)
    - [スマートポインタ](#スマートポインタ)
      - [RAIIパターン](#raiiパターン)
      - [スマートポインタの特徴の早見表](#スマートポインタの特徴の早見表)
      - [unique\_ptr](#unique_ptr)
      - [shared\_ptr](#shared_ptr)
      - [循環参照](#循環参照)
      - [weak\_ptr](#weak_ptr)
        - [weak\_ptrのlockメソッド](#weak_ptrのlockメソッド)
  - [参照](#参照)
    - [左辺値参照（Lvalue Reference）](#左辺値参照lvalue-reference)
    - [右辺値参照（Rvalue Reference）](#右辺値参照rvalue-reference)
- [コピー操作とムーブ操作](#コピー操作とムーブ操作)
  - [クラス実装における活用例](#クラス実装における活用例)
    - [コンストラクタにおける実装](#コンストラクタにおける実装)
    - [代入演算子における実装](#代入演算子における実装)
    - [メソッド引数における実装](#メソッド引数における実装)
      - [値渡し](#値渡し)
      - [ポインタ渡し](#ポインタ渡し)
      - [参照渡し](#参照渡し)
      - [結論：どの渡し方が良いか？](#結論どの渡し方が良いか)
- [ラムダ式](#ラムダ式)
  - [利点/欠点](#利点欠点)
  - [キャプチャなしのラムダ式](#キャプチャなしのラムダ式)
  - [キャプチャありのラムダ式](#キャプチャありのラムダ式)
    - [値キャプチャ](#値キャプチャ)
    - [参照キャプチャ](#参照キャプチャ)
    - [一部をキャプチャ](#一部をキャプチャ)
- [オブジェクト指向](#オブジェクト指向)
  - [classとstruct](#classとstruct)
  - [アドホック多相](#アドホック多相)
    - [オーバーロード](#オーバーロード)
    - [オーバーライド](#オーバーライド)
  - [テンプレートメタプログラミング(パラメータ多相)](#テンプレートメタプログラミングパラメータ多相)
    - [関数テンプレート](#関数テンプレート)
    - [クラステンプレート](#クラステンプレート)
    - [パラメータパック](#パラメータパック)
- [トリビアル(自明)](#トリビアル自明)
- [assertとNDEBUG](#assertとndebug)
- [キャスト](#キャスト)
- [マルチスレッド](#マルチスレッド)
  - [std::this\_thread](#stdthis_thread)
    - [sleep\_for() / sleep\_until()](#sleep_for--sleep_until)
    - [get\_id()](#get_id)
    - [yield()](#yield)
  - [std::thread](#stdthread)
    - [コンストラクタ](#コンストラクタ)
    - [デストラクタ](#デストラクタ)
    - [joinable()](#joinable)
    - [join()](#join)
    - [detach()](#detach)
    - [get\_id()](#get_id-1)
    - [実装例](#実装例)
  - [std::mutex](#stdmutex)
    - [基本](#基本)
      - [lock()を同一スレッドで2回呼び出す際のデッドロックの流れ](#lockを同一スレッドで2回呼び出す際のデッドロックの流れ)
    - [lock()](#lock)
    - [unlock()](#unlock)
  - [std::lock\_guard](#stdlock_guard)
  - [std::unique\_lock](#stdunique_lock)
  - [std::condition\_variable](#stdcondition_variable)
    - [wait()](#wait)
    - [wait\_for()](#wait_for)
    - [wait\_until()](#wait_until)
    - [notify\_all()](#notify_all)
    - [notify\_one()](#notify_one)
    - [実装例](#実装例-1)
  - [std::future](#stdfuture)
  - [std::atomic\_flag](#stdatomic_flag)
- [キーワード](#キーワード)
  - [inline(インライン関数)](#inlineインライン関数)
  - [virtual(仮想関数)](#virtual仮想関数)
    - [純粋仮想関数 / 抽象関数](#純粋仮想関数--抽象関数)
    - [デストラクタにvirtualがある理由](#デストラクタにvirtualがある理由)
  - [final(継承の禁止)](#final継承の禁止)
  - [override（オーバーライドの明示）](#overrideオーバーライドの明示)
  - [delete](#delete)
  - [default（デフォルトの明示）](#defaultデフォルトの明示)
    - [Rule of five (Rule of three)](#rule-of-five-rule-of-three)
  - [explicit(暗黙的型変換の防止)](#explicit暗黙的型変換の防止)
  - [friend（provate/protectedメンバへのアクセス）](#friendprovateprotectedメンバへのアクセス)
    - [friend関数](#friend関数)
    - [friendクラス](#friendクラス)
  - [constexpr（定数式）](#constexpr定数式)
    - [constとの違い](#constとの違い)
  - [this（自己参照）](#this自己参照)
  - [noexcept（例外の禁止）](#noexcept例外の禁止)
    - [条件付きnoexcept](#条件付きnoexcept)
    - [noexcept演算子](#noexcept演算子)

# 値とポインタと参照
## 簡易早見表
| 形態      | 変数   | ポインタ変数                                                      | 左辺値参照           | 右辺値参照                            |
| --------- | ------ | ----------------------------------------------------------------- | -------------------- | ------------------------------------- |
| 宣言/定義 | int a; | int *p;<br>int *p = &a;                                           | int &r;<br>int &r=a; | int &&rr;<br>int &&rr = std::move(a); |
| 値        | a      | *p<br>（間接参照/デリファレンス）                                 | r                    | rr                                    |
| アドレス  | &a     | p（ポインタが指してる値のアドレス）<br>&p(ポインタ自身のアドレス) | &r                   | &rr                                   |
## ポインタ
 - 型(型のサイズ)とアドレスから決まる。
 - 別のアドレスや型を再割当てできる。
 - nullptrを定義できる。  
（型が異なるのは通常は非推薦）

### constポインタ

```cpp
// ポインタが指し示す値を不変にする
// *の前にconstを付ける
int x = 123;
const int* p = &x;
*p = 456; // コンパイルエラー

// ポインタ自身を不変にする
// *の後にconstを付ける
int x = 123;
int* const p = &x;
p = nullptr; // コンパイルエラー

// ポインタが指し示す値とポインタ自身を両方とも不変にする
const int* const p = &x;
```

### ダブルポインタ
`**`で定義する。  
ポインタが指す先がポインタであるもの  
高度なアルゴリズムや多次元配列で利用されるが、モダンなC++ではあまり利用しないため、詳細は割愛  
```cpp
int var = 10;      // 通常の整数変数
int *ptr = &var;   // varへのポインタ
int **dptr = &ptr; // ptrへのダブルポインタ

// 値とアドレスを表示
cout << "varのアドレス: " << &var << endl; // 0x001

cout << "ptrが指す値のアドレス: " << ptr << endl; // 0x001
cout << "ptrが指す値: " << *ptr << endl;          // 10
cout << "ptr自身のアドレス: " << &ptr << endl;    // 0x002

cout << "dptrが指す値のアドレス: " << dptr << endl; // 0x002
cout << "dptrが指す値: " << *dptr << endl;          // 0x001
cout << "dptr自身のアドレス: " << &dptr << endl;    // 0x003

// ダブルポインタを通してvarの値を変更
**dptr = 20;
cout << var << endl; // 20
```
### (生)ポインタの欠点
#### ダングリングポインタ
メモリ開放済のポインタをダングリングポインタといい、未定義動作を助長する
```cpp
int *ptr = new int(10); // 動的メモリ割り当て
delete ptr;             // メモリ解放

// ptrはダングリングポインタとなる
// ptrは解放されたメモリ領域を指しているが、その領域はもう有効ではない
std::cout << *ptr; // 未定義動作
```
未定義動作を防止するため、nullptrを定義するとよい。
```cpp
int* ptr = new int(10);
delete ptr;
ptr = nullptr; // nullptrに設定

// ここでptrを参照しても、プログラムは安全
if (ptr != nullptr) {
    std::cout << *ptr;
}
```
#### 二重開放
動的メモリを二重で開放(delete)した際に、実行時エラーを誘発する。
```cpp
int *ptr = new int(10); // 動的メモリ割り当て
delete ptr;             // メモリ解放
delete ptr;             // 実行時にcore dumpする。
```
実行時エラーを防止するためには、開放したポインタにnullptrを設定する。
```cpp
int *ptr = new int(10); // 動的メモリ割り当て
delete ptr;             // メモリ解放
ptr = nullptr;
delete ptr;             // 実行時にcore dumpしない
```

### スマートポインタ
new/deleteなどヒープメモリにデータを当てる場合、上記のように(生)ポインタには欠点がある。  
それを仕組みとして防止するため、C++では`スマートポインタ`が開発された。  
※ スタックメモリであれば、スマートポインタにする明確な利点はないが、慣習的にスマートポインタにするPJも多い。  

#### RAIIパターン  
スマートポインタはRAII（Resource Acquisition Is Initialization）パターンに基づいて設計されいる。  
RAIIは、リソース（メモリ、ファイルハンドル、ネットワーク接続等）の取得と解放を、  
オブジェクトの生存期間に結びつけるC++の設計哲学である。  
このパターンにより、一般的にはオブジェクトのコンストラクタがリソースを取得し、デストラクタがリソースを解放する。  

#### スマートポインタの特徴の早見表  
| 特徴               | `std::unique_ptr`                                                          | `std::shared_ptr`                                                                          | `std::weak_ptr`                                                        |
| ------------------ | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------- |
| 所有権             | 唯一の所有権<br>オブジェクトに対して一つの`unique_ptr`のみが所有権を持つ。 | 共有された所有権<br>複数の`shared_ptr`インスタンスが同一のオブジェクトの所有権を共有する。 | 所有権を持たない<br>（弱参照）<br>オブジェクトの生存期間を延長しない。 |
| メモリリークの防止 | 所有権があるオブジェクトがスコープ外に出ると自動的に破棄される。           | 参照カウントが0になると自動的にメモリが解放される。                                        | 所有権にかかわらないため、循環参照の防止が可能。                       |
| コピー             | 不可                                                                       | 可                                                                                         | 可                                                                     |
| ムーブ             | 可                                                                         | 可                                                                                         | 可                                                                     |

下記でさらに詳しく解説する。  
各スマートポインタは、Testクラスに対して動的なメモリを確保/開放する。
```cpp
// 説明のためのclass
class Test {
public:
  std::shared_ptr<Test> other; // 他のTestインスタンスへのsharedポインタ
  Test() { std::cout << "コンストラクタ生成" << std::endl; }
  ~Test() { std::cout << "デストラクタコール" << std::endl; }
  void greet() { std::cout << "関数コール" << std::endl; }
};
```
#### unique_ptr
unique_ptrは、指定されたオブジェクトに対する唯一の所有権を持つ。  
そのオブジェクトが不要になったとき（つまり、unique_ptrがスコープを抜けるとき）に自動的にデストラクタを呼び出してオブジェクトを破棄する。

```cpp
{
  // Testオブジェクトを生成し、ptrが所有権を持つ
  std::unique_ptr<Test> ptr(new Test()); // コンストラクタ生成
  ptr->greet(); // 関数コール
} // デストラクタコール
// ptrがスコープ外に出ると、自動的にTestオブジェクトは破棄される

// std::unique_ptrはムーブ可能だが、コピーはできない
std::unique_ptr<Test> ptr1(new Test()); // コンストラクタ生成
std::unique_ptr<Test> ptr2 = std::move(ptr1); // 所有権がptr2に移動する
// ptr1はもはや何も所有していない

// スマートポインタは boolコンテキストで使用されると、
// ポインタが非nullの場合にtrue、nullの場合にfalseを返すように
// オーバーロードされている。
// スマートポインタの現在の状態が何も指していないかどうかをチェックできる。
if (!ptr1) {
  std::cout << "ptr1 is empty\n";
}
```
unique_ptrはコピーできない。  
unique_ptrの設計方針は、ポインタが指すメモリに対して「唯一の所有権」を持つことである。  
もしunique_ptrをコピーできると、元のポインタとコピーされたポインタの両方が同じメモリ領域を指すことになり、それぞれが所有権を主張することになる。  
(=唯一の所有権ではなくなる)  
したがって、unique_ptrの基本的な設計原則に反することになる。
#### shared_ptr
unique_ptrと異なり、コンストラクタ生成にはnewではなく専用のファクトリ関数である`make_shared`関数を利用する。  
1. 参照カウントのインクリメント:   
   ptr1が指すオブジェクトの参照カウントがインクリメントされる。  
   これにより、ptr1とptr2が同じオブジェクトを指していることが追跡され、そのオブジェクトは複数のshared_ptr によって共有されていることがわかる。  
2. リソースの安全な共有:   
   複数のshared_ptrインスタンスが同じオブジェクトを指している場合、  
   そのオブジェクトは最後のshared_ptrインスタンスがスコープを抜けるか、リセットされるまで解放されない。  
   これにより、リソース（メモリ）の安全な管理が可能となる。
3. 自動的なリソースの解放:   
   オブジェクトの最後のshared_ptrがスコープを抜けるか、リセットされた時点で、  
   そのオブジェクトの参照カウントがゼロになり、オブジェクトは自動的に解放される。
```cpp
// make_sharedを使ってTestオブジェクトを作成
std::shared_ptr<Test> ptr1 =
    std::make_shared<Test>(); // コンストラクタ生成
ptr1->greet(); // 関数コール

{
  // ptr2はptr1と同じオブジェクトを指す。
  std::shared_ptr<Test> ptr2 = ptr1; // コピー操作
  ptr2->greet();　// 関数コール
  // ptr2のスコープ終了時にもオブジェクトは解放されない（ptr1がまだ存在するため）
}
```
shared_ptrはコピー可能である。  
shared_ptrの設計方針は、「所有権を共有する」ことである。  
そのため、コピーされてもshared_ptrは参照カウンタにより、二重開放のリスクを防ぐ仕組みがある。

#### 循環参照
下記のように、互いのポインタが参照しあっている場合、shared_ptrのデストラクタが呼ばれない。  
理由は下記の通り。
1. ptr1の破壊:   
   ptr1がスコープを抜けると、ptr1が指すTestオブジェクトの参照カウントがデクリメントされる。しかし、ptr2->otherがまだ ptr1が指していたオブジェクトを参照しているため、参照カウントはゼロにならない。その結果、ptr1が指していたオブジェクトはまだ破壊されない。  
   参照カウントはptr1が1(ptr2経由)、ptr2は2(ptr2自体と、ptr1経由)
2. ptr2の破壊:   
   次に、ptr2がスコープを抜けると、ptr2が指すTestオブジェクトの参照カウントがデクリメントされる。この時点で、ptr1->otherがptr2が指していたオブジェクトを参照しているため、参照カウントは再びゼロにならない。    
   参照カウントはptr1が1(ptr2経由)、ptr2は1(ptr1経由)

```cpp
{
  auto ptr1 = std::make_shared<Test>(); // ptr1:count1 ptr2:count0
  auto ptr2 = std::make_shared<Test>(); // ptr1:count1 ptr2:count1

  ptr1->other = ptr2; // ptr1:count1 ptr2:count2
  ptr2->other = ptr1; // ptr1:count2 ptr2:count2
} // メモリリーク！ptr1:count1 ptr2:count1
```
また、unique_ptrでも同様に循環参照が生じる可能性がある。  
Testが`std::unique_ptr<Test> other`を持っていた場合、下記のようになる。
```cpp
{
  auto test1 = std::make_unique<Test>(); // 最初のTestインスタンス
  auto test2 = std::make_unique<Test>(); // 二番目のTestインスタンス
  test1->other = std::move(test2);        // test1がtest2を所有
  test1->other->other = std::move(test1); // test2がtest1を所有
} // メモリリーク！
// test1が開放されようとするとtest2があるため開放されない。逆も同様。
```
#### weak_ptr
上述の循環参照を伏せぐためは、weak_ptrを利用する。  
weak_ptrは弱参照（参照カウンタを増加させない）であるため、循環参照とならない。  
```cpp
class Test {
public:
  std::weak_ptr<Test> other; // sharedポインタではなく、weakポインタを利用する
  Test() { std::cout << "コンストラクタ生成" << std::endl; }
  ~Test() { std::cout << "デストラクタコール" << std::endl; }
  void greet() { std::cout << "関数コール" << std::endl; }
};

{
  auto ptr1 = std::make_shared<Test>(); // ptr1:count1 ptr2:count0
  auto ptr2 = std::make_shared<Test>(); // ptr1:count1 ptr2:count1
  ptr1->other = ptr2; // ptr1:count1 ptr2:count1 
  ptr2->other = ptr1; // ptr1:count1 ptr2:count1
} // メモリは完全開放される。ptr1:count0 ptr2:count0
```
##### weak_ptrのlockメソッド
weak_ptrは参照カウンタを増加させないため、参照先のオブジェクトのポインタが有効かわからない。  
lockメソッドは、weak_ptrをshared_ptrに変換することで、そのオブジェクトがまだ解放されていないかどうかがわかる。  
もしweak_ptrが指しているオブジェクトが既に解放されている場合、lockは空のshared_ptrを返すため、オブジェクトの有効性がチェックできる。  
ただしshared_ptrを生成するため、若干のオーバーヘッドがある。  

```cpp
std::weak_ptr<Test> ptr1_weak;

{
  auto ptr1 = std::make_shared<Test>();
  ptr1_weak = ptr1; // ptr1_weakはptr1を指すweakポインタなる。
}                   // ptr1がスコープを抜け、解放される

if (ptr1_weak.lock()) { // shared_ptrに変換し、オブジェクトが有効かどうか判断
  std::cout << "alive" << std::endl;
} else {
  std::cout << "freed" << std::endl; // こちらが出力される
}
```

## 参照
前述のポインタから制限が加えられており、ポインタより安全に取り扱うことができる。
 - 別のアドレスや型を`再割当てできない`ので、必ず`宣言時に初期化`が必要。
 - 再割当てできないため、`nullptrは利用しない`
 - ポインタは自身のアドレスを取得できるが、参照自身のアドレスは取得できない。
  ただし、参照先のアドレスはポインタ同様に取得できる。

### 左辺値参照（Lvalue Reference）
メモリ上に明確なアドレスを持つオブジェクトへの参照のこと。
```cpp
int x = 10;
int& ref = x; // refはxの左辺値参照

ref = 20;     // refを通じてxの値を変更
std::cout << "x: " << x << std::endl; // x=20
```
### 右辺値参照（Rvalue Reference）
一時的な値やオブジェクトへの参照のこと。  
左辺値を右辺値にするには、`std::move`で左辺値を右辺値可能な状態にする。
```cpp
int x = 10;
int&& rref = std::move(x); // xの右辺値参照

rref = 30;
// std::move後にxにアクセスするのは、そもそも非推薦
std::cout << "x: " << x << std::endl; // x=30
```

# コピー操作とムーブ操作
・コピー操作
 - オブジェクトを丸ごと複製する  
   一方のオブジェクトが他方のオブジェクトに影響を与えない
 - コピーコンストラクタや代入演算子により実行される。  

・ムーブ操作
 - オブジェクトを移動する  
   ムーブ元のオブジェクトは不定値となる。
 - ムーブコンストラクタやムーブ代入基本空に影響を与えない演算子により実行される。
 - リソースの複製を避けるため、通常コピー操作よりも効率的


## クラス実装における活用例
下記のクラスに関して考える。

```cpp
class MyClass {
private:
  int *data;
  size_t size;

public:
  MyClass(size_t size) : size(size), data(new int[size]) {
    std::cout << "デフォルトコンストラクタ\n";
  }

  ~MyClass() {
    delete[] data;
    std::cout << "デストラクタ\n";
  }
};

// コール方法
MyClass a(10);           // デフォルトコンストラクタ
```
### コンストラクタにおける実装
コピーコンストラクタには、 引数に`const`を利用する。  
これにより、コピー元のオブジェクトがコピーコンストラクタ内で変更されないことが保証できる。  
ムーブコンストラクタでは、`noexcept`を利用する。  
ムーブ操作はリソースの所有権を移動するため、途中で失敗するとオブジェクトが未定義となるリスクがある。  
noexceptを付けることで、ムーブ操作が途中で失敗しないことを保証し、オブジェクトの状態を安全に保つことができる。  
もしnoexceptを指定しなかった場合、例外を投げる可能性があると見なされ、安全を確保するためにコピー操作が使用されることがある。

```cpp
class MyClass {
...
public:
  MyClass(const MyClass &other) : size(other.size), data(new int[other.size]) {
    std::copy(other.data, other.data + size, data);
    std::cout << "コピーコンストラクタ\n";
  }
  // もしコピーコンストラクタを禁止したい場合は、deleteキーワードを利用する
  // コピーコンストラクタを呼ぼうとするとコンパイルエラーとなる
  // MyClass(const MyClass& other) = delete;

  MyClass(MyClass &&other) noexcept : size(other.size), data(other.data) {
    other.data = nullptr;
    other.size = 0;
    std::cout << "ムーブコンストラクタ\n";
  }
  // もしムーブコンストラクタを禁止したい場合は、deleteキーワードを利用する
  // ムーブコンストラクタを呼ぼうとするとコンパイルエラーとなる
  // MyClass(MyClass &&other) = delete;
};

// コール方法
MyClass a(10);           // デフォルトコンストラクタ
MyClass b(a);            // コピーコンストラクタ
MyClass c(std::move(a)); // ムーブコンストラクタ
```
### 代入演算子における実装
コピー代入演算子では、 引数に`const`、ムーブ代入演算子では、`noexcept`を利用する。  
理由は上述したコンストラクタの説明と同様である。  
また`if (this != &other)`で自己代入をチェックする必要もある。
```cpp
class MyClass {
  ...
  MyClass &operator=(const MyClass &other) {
    if (this != &other) {
      delete[] data;
      size = other.size;
      data = new int[size];
      std::copy(other.data, other.data + size, data);
    }
    std::cout << "コピー代入演算子\n";
    return *this;
  }

  MyClass &operator=(MyClass &&other) noexcept {
    if (this != &other) {
      delete[] data;
      data = other.data;
      size = other.size;
      other.data = nullptr; other.size = 0;
    }
    std::cout << "ムーブ代入演算子\n";
    return *this;
  }
};

MyClass a(10); MyClass b(20); MyClass c(20); MyClass d(20);
c = a;                     // コピー代入演算子
d = std::move(b);          // ムーブ代入演算子
```

### メソッド引数における実装
基本的な使い方は下記の通り
```cpp
// 値渡し
void passByValue(int x) { x = x + 1; }
// ポインタ渡し
void passByPointer(int *ptr) { *ptr = *ptr + 1; }
// （左辺値）参照渡し
void passByLeftReference(int &ref) { ref = ref + 1; }
// （右辺値）参照渡し
void passByRightReference(int &&ref) { ref = ref + 1; }

int num = 5;
passByValue(num);                    // num=5（値渡しは呼び出し元に影響なし）
passByPointer(&num);                 // num=6（ポインタ渡しは呼び出し元に影響の可能性あり）
passByLeftReference(num);            // num=7（参照渡しは呼び出し元に影響の可能性あり）
passByLeftReference(std::move(num)); // num=8（参照渡しは呼び出し元に影響の可能性あり）
```
#### 値渡し
 - 値そのものを引数として要求する
 - 関数内で値がコピーされる
 - 元の引数に影響なし  

→ 特に採用するメリットはない
#### ポインタ渡し
 - 値のポインタ（≒アドレス）を引数として要求する
 - 関数内でポインタを通じて引数の値を変更でき、元の引数に影響が生じる場合がある
 - NULLを許容できる（そもそもポインタ自体がNULLを許容できるため）

```cpp
// 実装
void passByPointer(int *ptr) {
  if (ptr == nullptr) {
    std::cout << "nullptr" << std::endl;
  } else {
    *ptr = *ptr + 1;  // 通常の処理
    std::cout << "incremented: " << *ptr << std::endl;
  }
}

// 呼び出し側
int num = 5;
passByPointer(&num); // 通常のポインタ
passByPointer(nullptr); // NULL ポインタ
```  

#### 参照渡し
 - 値の参照を引数として要求する
 - 関数内で参照を通じて引数の値を変更でき、元の引数に影響が生じる場合がある
 - NULLを許容できない（そもそも参照自体がNULLを許容できるため）
 - 右辺値参照の場合、ムーブ操作により参照元が不定値となる。


#### 結論：どの渡し方が良いか？
 - std::optionalを利用している場合は、nullptrの可能性を考慮できるポインタ渡しを利用する
 - そうでなければ、ポインタ渡しより安全で直感的な参照渡しを利用する

# ラムダ式
## 利点/欠点
・利点
 - 可読性の向上
 - パフォーマンスの最適化  
関数ポインタやstd::functionよりも高いパフォーマンスが出る場合がある。  
(ラムダ式内部が単純な操作な場合、コンパイラがインライン化やその他の最適化を行いやすい)  

・欠点
 - 匿名関数であるため、デバッグで追跡しにくい
 - パフォーマンスの悪化  
ラムダ式内部で複雑な操作があった場合、意図せず型消去やヒープ割当が実施され、ランタイムコストが発生する可能性がある。

## キャプチャなしのラムダ式
ラムダ式の外部の値を利用せず、純粋に引数のみを利用したラムダ式
```cpp
#include <functional>
...
std::function<int(int, int)> lamda = [](int x, int y) { return x + y; };
int result = lamda(5, 3); // 8
// ラムダ式の型は一般的に複雑であるため、下記のようにautoとして定義するのが好まれている
auto lamda = [](int x, int y) { return x + y; };
int result = lamda(5, 3); // 8
...
```
## キャプチャありのラムダ式
### 値キャプチャ
`[=]`により、外部変数の全ての値をコピーする。  
しかし、コピーのコストが大きいため、基本的には利用しない。  
なお値をコピーしているため、ラムダ式内部で値を変更しても外部変数に影響はない
```cpp
int a = 10;
int b = 20;
auto lambda1 = [=]() { return a + b; };
// 仮にラムダ式内部でaの値を変更したい場合はmutableを付ける。
// ただし、mutableを付けても外部変数(a)に影響はない
auto lambda2 = [=]() mutable {
  a = 1;
  return a + b;
};

int result1 = lambda1(); // result1=30, a=10
int result2 = lambda2(); // result2=21, a=10
```

### 参照キャプチャ
```cpp
int a = 10;
auto lambda = [&]() {
  a = 20; // 外部のaを参照して変更する
};
lambda(); // この時点でa=20となる
```

### 一部をキャプチャ
下記のように、部分的にキャプチャも可能。  
また値/参照どちらも組み合わせてキャプチャできるが、前述の通り値キャプチャはコストが大きいため、基本参照キャプチャが推薦  
（参照先が消去される可能性のあるものに関してのみコピーを利用する）  
```cpp
int a = 10;
int b = 20;

// mutableを付けることで、ラムダ式のスコープ内でのみ変数の値を変更できる
// ただし、ラムダ式のスコープを抜けると変数は元に戻る
auto lambda = [a, &b]() mutable {
  // aは値によってキャプチャされるので、ラムダ式内での変更は外部のaに影響しない
  // bは参照によってキャプチャされるので、ラムダ式内での変更が外部のbに影響する
  a = 30; // この変更は外部のaには影響しない
  b = 40; // この変更は外部のbを変更する
};

lambda(); // a=10, b=40
```

# オブジェクト指向
## classとstruct
C++におけるclassとstructはどちらもメンバ関数、コンストラクタ、デストラクタ、メンバ変数、継承などの機能を持つ。  
主な違いはアクセス制御であり、classで宣言されたメンバはデフォルトでprivate、structの場合はpublicである。  
`classのメンバがデフォルトでprivateであるのは、カプセル化を意識したオブジェクト指向に基づいている。`  
そのため、データとロジックが混在する場合はclass、単純なデータ構造を示す場合はstructを使用するのが一般的。  
またstructは下記でも利用が検討される。
    
**・テンプレートメタプログラミング**  
structはメンバがデフォルトでpublicであるため、テンプレートパラメータとしてのアクセスや操作が容易  
  
**・C言語との互換**  
C++がC言語との互換性を保つためにstructを導入した背景がある。  
C言語のコードをC++に移植する際、structをそのまま利用できるという利点がある。

## アドホック多相
### オーバーロード  
同じコンストラクタ/関数で引数が異なる
```cpp
class Calc {
    Calc(); // デフォルトコンストラクタ
    Calc(int a, int b); // 引数付きコンストラクタ
    int add();
    int add(int a, int b);
}
```
### オーバーライド  
継承元と同名の関数
```cpp
class Car {
    void drive();
}

class Tank : public Car{
    // Carと同じ動きであれば、不要な定義
    void drive(); // Tankのdriveとして定義。継承元と別の実装が可能
}
```


## テンプレートメタプログラミング(パラメータ多相)
### 関数テンプレート
下記の例では`typename`が宣言されているが、どのような名前で表現しても良い。  
慣習として、`class`は、パラメータがクラス型であることを強調する場合に使われることがある。  

```cpp
// 実装
template <typename T> T add(T a, T b) { return a + b; }

//　呼び出し側
cout << add(3, 4) << endl; // 7
cout << add(3.1, 4.4) << endl; // 7.5
// 自動判定のchar*だとoperator+がないので、明示的にstringを指定して内部でcastする
// cout << add("a", "b") << endl;
cout << add<string>("a", "b") << endl; // ab
// Tで型を１つに指定しているため、別の型同士はできない
// cout << add(3.1, 4) << endl; // Error：doubleとint
```

### クラステンプレート
通常、クラステンプレートはヘッダに記載する。
```cpp
template <typename T> class Calc {
private:
  T m_n1;
  T m_n2;

public:
  inline void set(const T n1, const T n2) {
    m_n1 = n1;
    m_n2 = n2;
  }
  inline T add() const { return m_n1 + m_n2; }
};

// 呼び出し側
Calc<int> i1;
i1.set(1, 2);
cout << i1.add() << endl; // 3
Calc<string> i2;
i2.set("c", "d");
cout << i2.add() << endl; // ab
```

### パラメータパック


# トリビアル(自明)


# assertとNDEBUG
```cpp
#include <cassert>

int main() {
    int x = 5;
    assert(x == 5); // assertがtrueなので、何もしない

    x = 3;
    assert(x == 5); // assertがfalseとなり、実行時にcoredumpする
}
```
assertは、プログラム内の特定の条件が真であることをチェックするために使用される。  
ただし、`-DNDEBUG`マクロで、assertコマンド自体がコンパイルのプリプロセッサ過程で削除される。  
一般的に、デバッグビルドでは未定義とし、リリースビルドで定義することで、リリースのパフォーマンスを向上させる。  
```shell
clang++ -std=c++14 -DNDEBUG -o main main.cpp
```

下記は詳細。ifdefでassertの振る舞いが変化している。
```cpp
// asset.h

#ifdef	NDEBUG
...
# define assert(expr)		(__ASSERT_VOID_CAST (0))
...

#else /* Not NDEBUG.  */
...
#  define assert(expr)							\
     (static_cast <bool> (expr)						\
      ? void (0)							\
      : __assert_fail (#expr, __FILE__, __LINE__, __ASSERT_FUNCTION))
...
#endif /* NDEBUG.  */
```
# キャスト

# マルチスレッド
## std::this_thread
現在のスレッドに対して、何らかの処理を実施する。

### sleep_for() / sleep_until()
現在のスレッドをスリープ（一時停止）状態にする。
```cpp
// 指定時間まで停止
std::this_thread::sleep_for(std::chrono::seconds(1));

// いつまで停止するかを指定して停止
// システム時刻が動作に影響する
auto wakeUpTime = std::chrono::system_clock::now() + std::chrono::hours(1);
std::this_thread::sleep_until(wakeUpTime);
```

### get_id()
現在のスレッドのIDを取得する。  
スレッドが開放されると、IDは削除され再割当てされるため、実行中に厳密には一意に決まらない。 
デバッグ用に利用される。
```cpp
std::cout << "現在のスレッドID: " << std::this_thread::get_id() << std::endl;
```

### yield()
現在実行中のスレッドがプロセッサの使用権を自発的に放棄し、他のスレッドに実行の機会を譲るようOSに指示する。  
ただし、yieldの呼び出しが必ず他のスレッドに実行機会が与えられるわけではない。  
スケジューラは状況に応じて、同じスレッドを再び実行することもありえる。  
使用理由は、下記が挙げられる。  
リソースのオーバーコミットを避ける:   
すべてのスレッドが同時にアクティブになるとリソースのオーバーコミットが発生する可能性がある。  
yieldを使うことで、一時的にスレッドの実行を中断し、リソースの圧迫を緩和する。  
busy-waitingの改善:   
スピンロックのようなbusy-waitingを行う場合に、CPUの無駄な使用を避けるためにyieldを使用する。  
```cpp
// ある条件（readyがtrueになる）が満たされるまで、
// 現在のスレッドが実行を一時中断し、他のスレッドに実行の機会を譲る
while (!ready) {
    std::this_thread::yield(); // 他のスレッドに処理の機会を与える
}
```
## std::thread
最も基本的なスレッドの生成方法

下記のように、1秒ずつ標準出力する関数を考える。
```cpp
void threadFunc()
{
  for (int i = 0; i < 5; ++i)
  {
    std::cout << "スレッド: " << i << std::endl;
    std::this_thread::sleep_for(std::chrono::seconds(1));
  }
}
```
### コンストラクタ
std::threadのコンストラクタ作成時に、スレッドの生成と実行が同時に走る。

### デストラクタ
std::threadのデストラクタで、joinableがtrueの場合は、std::terminalで強制終了する。

### joinable()
std::threadのメソッドで、対象のインスタンスがjoin()もしくはdetach()可能かを判断する。

### join()
std::threadのメソッドで、対象のインスタンスが終了するまで、呼び出し元スレッドを待機する。  
joinableがtrueでなければ、coredumpする。また実行が成功することでjoinableがfalseになる。  

### detach()
std::threadのメソッドで、呼び出し元スレッドと切り離して実行する。  
(ライフサイクルが元スレッドと分離する）  
これにより、呼び出し元のスレッドが終了しても、スレッドは処理が完了するまで残存する。  
joinableがtrueでなければ、coredumpする。また実行が成功することでjoinableがfalseになる。  

### get_id()
joinableなインスタンスに関して、IDを取得できる。  
joinableでない場合は、IDが取得できない。

### 実装例
```cpp
std::cout << "start" << std::endl;

std::thread t(threadFunc); // スレッドの作成と実行が開始

std::this_thread::sleep_for(std::chrono::seconds(3));
std::cout << "call by main" << std::endl;

t.join(); // スレッドの終了を待つ。その間、呼び出し元はロックされる。

// detach()はスレッドを分離し、呼び出し元の処理が完了しても実行し続ける。  
// 既にjoinをコールしてjoinableがfalseであるため、実際にコールするとcoredumpする
// t.detach(); 
std::cout << "end" << std::endl;
```

## std::mutex
### 基本  
std::mutexは非再帰的で、一度ロックされると、別のスレッドによって解放されるまで、  
他のどのスレッドも（ロックを取得した同じスレッドであっても）ロックを取得することができない。  
また他スレッドがmutexをブロックしている状態で別のスレッドがロックしようとすると、  
mutexが開放されるまで待機（ブロック）する。  
そのため、mutexを同一スレッドで2回以上ロックすると、デッドロックとなる。  

#### lock()を同一スレッドで2回呼び出す際のデッドロックの流れ

1. **最初の `lock` 呼び出し**
   - スレッドAが `std::mutex` の `lock` メソッドを呼び出す。
   - mutexはロックされ、スレッドAはそのmutexの所有者となる。

2. **スレッドAの処理**
   - スレッドAは何らかの処理を行う。この段階では問題はない。

3. **2回目の `lock` 呼び出し**
   - 同じスレッドAが再び `lock` メソッドを呼び出す。
   - しかし、このmutexはすでにスレッドAによってロックされている。`std::mutex` は非再帰的であり、同じスレッドによる再ロックは許可されていない。

4. **ブロック**
   - スレッドAは、自分自身が解放するまで、すでにロックされているmutexの解放を待つことになる。
   - しかし、スレッドA自身がmutexのロックを保持しているため、その解放は永遠に発生しない。

5. **デッドロックの発生**
   - この結果、スレッドAは無限にブロックされ、プログラムはデッドロック状態に陥る。

結論として、スレッドは自分自身が解放する前に、自分自身が保持するmutexを再ロックしようとして無限に待機することになる。この状態から抜け出すためには、外部からの介入（例えばプログラムの強制終了）が必要となる。


### lock()
※通常は仕様が非推薦
mutexをロックする。  
`mutexがすでに別のスレッドによってロックされている場合、このメソッドを呼び出したスレッドは、mutexが利用可能になるまでブロック（待機）され、次の処理まで進まない。`   
また同一スレッドでlock()を2回呼び出すと、自分自身が解放するまでロックを解除できず、結果的にデッドロック状態となる。


### unlock()
※通常は仕様が非推薦
mutexをアンロックする。  
既にアンロックされているmutexに対して unlock()を呼び出すと、プログラムの動作は未定義となり、  
ランタイムエラーまたはクラッシュを引き起こす可能性がある。  

```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;
int sharedResource = 0;

void incrementResource() {
    mtx.lock();
    // mtx.lock();　// デッドロックしてしまう
    // クリティカルセクション（排他制御が必要な処理）
    ++sharedResource;
    std::cout << "Resource value: " << sharedResource << std::endl;
    mtx.unlock();
}

int main() {
    std::thread t1(incrementResource);
    std::thread t2(incrementResource);

    t1.join();
    t2.join();

    return 0;
}
```

## std::lock_guard
RAIIパターンにより、コンストラクタでmutexをロックし、デストラクタで自動的にアンロックする。  
これにより、例外が発生しても確実にmutexが解放される。  
一方で、そのスレッドが終了（デストラクタ呼び出し）しなければmutexをずっとlockしてしまう。  
**詳細:**  
例外が発生した場合、C++はスタックアンワインディング（stack unwinding）を行い、例外が発生したスコープに存在するすべてのオブジェクトのデストラクタを呼び出す。std::lock_guardオブジェクトもこの対象であるため、デストラクタによってミューテックスが確実にアンロックされる。

また、`std::lock_guard<std::mutex>`が同一スレッドで2回呼び出されると、デッドロックする。  
```cpp
std::mutex mtx;

void print_block(int n, char c) {
    std::lock_guard<std::mutex> lock(mtx);
    // 2回呼び出すと、デッドロックされる。
    // std::lock_guard<std::mutex> lock2(mtx);
    for (int i = 0; i < n; ++i) { std::cout << c; }
    std::cout << '\n';
}

int main() {
    std::thread th1(print_block, 50, '1');
    std::thread th2(print_block, 50, '2');

    th1.join();
    th2.join();

    return 0;
}
```

## std::unique_lock
`std::lock_guard`ではデストラクタコールでのみロックの開放ができたが、  
`std::unique_lock`はstd::lock_guardよりも柔軟で、ロックの取得と解放を明示的に制御できる。  
デストラクタコールでもロックは開放されるが、unlock()後にデストラクタが呼ばれても二重でアンロックすることはない。  
std::unique_lockでmutexのロック状態を内部的に追跡しているため、既にアンロックされたmutexは再アンロックされないようになっている。  

一方で、lockの管理はできていない。  
下記の例では、`lock.lock()`を2回実施するとcoredumpするが、  
`std::_unique_lock<std::mutex>`を2回実施するとデッドロックする。  

```cpp
std::mutex mtx;

void print_block(int n, char c) {
    std::unique_lock<std::mutex> lock(mtx);
    // std::unique_lock<std::mutex> lock(mtx);　2個作成するとcoredumpする。
    for (int i = 0; i < n; ++i) { std::cout << c; }
    std::cout << '\n';
    // ロックを手動で解放できる
    lock.unlock();
    // 何か他の処理を行う
    // ...
    // 再度ロックを取得
    lock.lock();
    // lock.lock(); // 2回コールすると、coredumpする。
    for (int i = 0; i < n; ++i) { std::cout << c; }
    std::cout << '\n';
}

int main() {
    std::thread th1(print_block, 50, '*');
    std::thread th2(print_block, 50, '$');

    th1.join();
    th2.join();

    return 0;
}
```


## std::condition_variable
スレッド間でのシグナル通知(norify)を可能にし、特に複数のスレッドが特定の条件が満たされるのを待つために使用される。
std::condition_variableのベストプラクティスは主に下記の流れ
1. ミューテックスをロックする。
2. 共有データ（下記の場合はready）を変更する。
3. ミューテックスをアンロックする前に条件変数を通知する。
4. ミューテックスをアンロックする。

### wait()
waitの引数でmutexのlockと条件式を要求しており、条件式が整うまでスレッドを待機させる。  
また条件式が整ったかどうかは、同インスタンスでnotifyされた時にチェックする。   
なお待機中はmutexを一時的に開放する。  


### wait_for()
bool値を返す。  
指定された時間が経過するか、条件が満たされるまで、スレッドを待機する。  
時間内に条件が満たされた場合はtrueを返す。  
またwait_forで待機中はmutexが一時的に開放される。

### wait_until()
`wait_for()`は指定時間までスレッドを待機するが、  
`wait_until()`は、  いつまで停止するかを時刻指定してスレッドを待機する。  
そのため、システム時刻が動作に影響する。

### notify_all()
notify_all() は、条件変数で待機中の全てのスレッドに通知を送る。  
全ての待機中のスレッドがブロックから解放され、実行を再開することができる。  

### notify_one()
notify_one()は、待機中のスレッドのうち一つをランダムに選んで通知する。  
複数のスレッドが条件変数で待機している場合でも、選ばれた一つのスレッドのみがブロックから解放される。  
notify_one() は、notify_all() よりも効率的な場合があるが、複数のスレッドが異なる条件を待機している場合、再度待機状態となる。  
notify_all()を使用すると、必要のないスレッドまで起動してしまうことになるが、少なくとも待機条件が満たされているスレッドが確実に処理を再開することが保証される。  

### 実装例
```cpp
std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void print_id(int id)
{
  // mutexをロック（orブロック（待機））
  // ロックするまで次の処理に進まない
  std::unique_lock<std::mutex> lock(mtx);
  // wait()で、readyがtrueになるまでmutexを開放。
  // mutexを動的に操作するため、std::lock_guardではなくstd::unique_lockを利用する
  cv.wait(lock, [] { return ready; });
  // 本当は複数のスレッドが同時に出力を行うと出力が混在する可能性があるため、
  // 出力操作もミューテックスで保護するか、スレッドごとに異なる出力ストリームを使用するなどの
  // 対策が必要となる。
  std::cout << "Thread " << id << '\n';
}

int main()
{
  std::thread threads[10];

  for (int i = 0; i < 10; ++i)
  {
    // 各スレッドが0~9を引数としてもらう。
    threads[i] = std::thread(print_id, i);
  }

  std::cout << "10 threads ready to race...\n";

  // ready変数は別スレッドも見ているため、排他制御が必要
  std::unique_lock<std::mutex> lock(mtx);
  ready = true;
  // 排他制御中に、条件変数を通知する。
  cv.notify_all();
  lock.unlock();

  for (std::thread &th : threads)
  {
    th.join(); // スレッドが完了するまでmainスレッドをロックする。
  }
}
```

## std::future


## std::atomic_flag

# キーワード
## inline(インライン関数)
inlineを付けることで、インライン関数となり、コンパイル時にインライン展開される。  
インライン関数は通常、ヘッダに記載される。  
比較的小さなインライン関数は、処理が呼び出し元に直接埋め込まれるため、処理速度が向上する。  
一方で、非常に大きな関数をインライン化してしまうと、コード全体のサイズが劇的に大きくなりパフォーマンスが劣化する。  
(現代的なCPUでは、一般にコードサイズが小さい方が命令キャッシュを有効に使えるため、高速に動作するため)  
またインライン関数はビルド成果物の容量が大きくなる。  
また名前空間をinline化できる。  
(例えばouter::inner::foo()とouter::foo()が同義に扱える。)  
しかし困惑の元となるため、Google Coding Styleでは非推薦とされる。

## virtual(仮想関数)
仮想関数(virtual)では、子クラスでオーバーライドされた同名のメンバ関数が実行される。  
またGoogle Coding Styleより、`仮想関数や仮想デストラクタをオーバーライドする際には、後述するoverride指定子かfinal指定子のいずれか1つをつけて、オーバーライドを明示的にする。` 
```cpp
// ヘッダ
class Bird {
    void fly();
    virtual void sing();
}

class Clow : public Bird {
    void fly();
    void sing();
}

// 実装
Bird* pBird;
pBird1 = new Crow(); // 親クラスの型のポインタでインスタンス生成
pBird1->fly(); // 親で呼び出し
pBird1->sing(); // 親がvirtual経由で子を呼び出し
```

### 純粋仮想関数 / 抽象関数
抽象的なBirdの具体的な鳴き声は存在しない。
そのような場合、virtualの定義に=0を付ける。  
これを純粋仮想関数と呼ぶ。
```cpp
// ヘッダ
class Bird {
    void fly();
    virtual void sing()=0;
}

// 実装
// 抽象クラスはインスタンス化できない。
b = new Bird(); // コンパイルエラー

```

### デストラクタにvirtualがある理由
デストラクタが発動するdelete命令は、親クラスから実行される。  
そのため、親クラスのデストラクタにvirtualがなければ、小クラスのデストラクタが実行されないため、virtualが必須となる。  


## final(継承の禁止)
クラスを継承させたくない場合
```cpp
class Base final {
    ...
};

class Derived : public Base { // コンパイル時にエラー
    ...
};
```
関数を継承させたくない場合
```cpp
class Base {
public:
  virtual void doSomething() final;
};

class Derived : public Base {
public:
  void doSomething(); // コンパイル時にエラー
};
```

## override（オーバーライドの明示）
親クラスにvirtualがない状態で小クラスがoverrideを利用するとコンパイルエラーとなる。  
可読性と保守性を向上させるために有用で、特に大規模なプロジェクトの場合、意図しない継承やオーバーライドを防ぎ、より明確な設計に役立つ。
```cpp
class Base {
public:
  // 子クラスのoverrideより、下記のvirtualもしくは
  // 下記の行を丸ごとコメントアウトするとコンパイルエラーとなる
  virtual void doSomething();
};

class Derived : public Base {
public:
  void doSomething() override; // 明示的に Base::doSomething() をオーバーライド
};
```

## delete
deleteには２つの使用方法がある。  
 - 動的メモリの解放:  
delete（単一オブジェクト用）または delete[]（オブジェクトの配列用）を使用することで、動的メモリを開放できる。  
例えば、int* p = new int; として確保したメモリは delete p; で解放され、int* arr = new int[10]; として確保したメモリは delete[] arr; で解放される。

 - 関数の削除:  
特定の関数やコンストラクタの使用を禁止するために使用される。  
クラスのメンバ関数またはコンストラクタに対して = delete; を指定することで禁止できる。  
これにより、その関数の使用を試みるとコンパイル時にエラーが発生する。

## default（デフォルトの明示）
defaultは、特殊メンバ関数（コンストラクタ、デストラクタ、コピー/ムーブコンストラクタ、コピー/ムーブ代入演算子）に対して、コンパイラが提供するデフォルトの実装を使用することを示す。  
主に下記で解説するRule of fiveで可読性向上を目的に利用される。  

### Rule of five (Rule of three)
`Rule of five (Rule of three)`はクラス設計におけるベストプラクティスである。  
このルールの背景には、（動的メモリの）リソース管理がある。  
下記で解説する一部が未実装だと、リソースの二重解放やメモリリークなどの問題が発生する可能性がある。

`Rule of Three`は、ムーブセマンティクスが実装されるC++11より前に考えられたものである。  
あるクラスが下記のいずれかのカスタム操作(≒defaultでない)
を持つ場合、残りの二つも提供する必要がある。

1. デストラクタ
2. コピーコンストラクタ
3. コピー代入演算子


`Rule of Five`は、ムーブセマンティクスが実装されたC++11以後で考えられたものである。  
上記３つに加えて、下記のムーブ特性も考慮して実装する必要がある。  

4. ムーブコンストラクタ  
5. ムーブ代入演算子


## explicit(暗黙的型変換の防止)
explicitは、クラスのコンストラクタや型変換演算子に対して、暗黙の型変換を禁止するために使用される。  
これにより、意図しない型変換を防ぎ、プログラムのバグを減らすことができる。
```cpp
class Foo {
public:
  explicit Foo(int x) : _x(x) {}

private:
  int _x;
};

void someFunction(Foo foo) { std::cout << "call" << std::endl; }

int main() {
  // someFunction({1}); // 暗黙的型変換: explicitがある場合はコンパイルエラー
  someFunction(Foo(1)); // 型が明示的に強制される
}
```

Google Coding Styleでは、下記のように明記される  
explicitが必要なケース
 - 型変換演算子と引数が1つのコンストラクタ

explicitが不要なケース
 - 引数が1つでないコンストラクタ:  
  explicitは不要。暗黙の型変換が起きることはない想定のため。

 - コピーコンストラクタとムーブコンストラクタ:  
  explicit非推薦。コピーコンストラクタとムーブコンストラクタは型変換を行わないため、暗黙的に使用されるべき

 - ある型が別の型に簡単に変換でき、かつ意味的に同じ値を表す場合:  
  （例：std::chrono::duration）  
  explicitは基本不要。プロジェクトの方針に従って決定すべき。

 - std::initializer_listを引数に取るコンストラクタ:  
  explicit省略推奨。  
  初期化リストを用いたコピー初期化（例：MyType m = {1, 2};）ができるため。


## friend（provate/protectedメンバへのアクセス）
friendはprovate/protectedメンバへのアクセスできるようになり、クラス内部でのみ宣言できる。  
これにより、ある特定のクラスにだけメンバへのアクセス権を与えたい場合、そのメンバを単にpublicにするよりも、フレンドを用いる方が適切な場合がある。  

### friend関数
関数にfriendを宣言することで、そのクラス内部のプライベートメンバにアクセスできる。  
また下記のコードに記載はないが、演算子オーバーロード（特に二項演算子）でプライベートメンバの比較をする際によく利用される。
```cpp
class MyClass {
private:
  int x;

public:
  MyClass(int val) : x(val) {}

  // friendでプライベートメンバ(x)にアクセスできる
  friend void showX(MyClass &m) { 
    std::cout << m.x << std::endl; 
  }
};

int main() {
  auto myClass = MyClass(1);
  showX(myClass); // 1
}
```
### friendクラス
friend関数と異なり、他のクラスのプライベートメンバにアクセスしたい場合に利用する。  
Google Coding Styleでは、カプセル化破壊を懸念し、ほとんどのクラス間のやりとりはpublicメンバを推奨している。
```cpp
class B {
  friend class A; // クラスAはクラスBのプライベートメンバにアクセス可能となる
  int value;

public:
  B(int v) : value(v) {}
};

class A {
public:
  // クラスAはクラスBのfriendであるため、プライベートメンバにアクセスできる
  void showB(B &b) { std::cout << "B value: " << b.value << std::endl; }
};

int main() {
  A a;
  B b(10);
  a.showB(b);
}
```


## constexpr（定数式）
`constant expression`の略で、constexprを利用することでコンパイラはその式や関数の結果をコンパイル時に確定することができる。  
コンパイル時に確定できる式としては、数学関数や配列の要素が挙げられる。
 - 利点  
  コンパイル時に値が確定するため、実行時のメモリ割り当てが減少する。  
 - 欠点  
  最適化計算により、コンパイル時間が増加する  
  コンパイル時に確定できないものは、コンパイル時にエラーとなる。   
  （動的メモリ割り当て、乱数生成、ユーザ入力、ファイル読み込み等）

また、Google Coding Styleでは、`constexprは真の定数や、それらの定義に用いる関数を定義するために利用する`との記載がある。
```cpp
constexpr int multiplyByTwo(int n) { return n * 2; }

int main() {
  int x;
  std::cin >> x; // ユーザー入力
  constexpr int result = multiplyByTwo(x); // コンパイルエラー
}
```
### constとの違い
`const`は変数の値が変更不可能であることを示すが、その値がコンパイル時に知られている必要はない。  
constexprは変数、関数、オブジェクトがコンパイル時に既知でなければならない。

## this（自己参照）
thisは、メンバ関数が呼び出されたインスタンスを指す。  
（インスタンスを指すため、静的メンバ関数では使用不可）  
これにより、そのインスタンスの他のメンバ（private含め）にアクセスできる。  
thisはポインタであるため、メンバへのアクセスにはアロー演算子を使用する。  
またラムダ式のキャプチャとしてもthisは使用できる。  

thisを使う必要性として、`メンバ名がローカル変数やパラメータと名前が衝突する場合`は、this->を使って明示的にメンバを指定する必要がある。  
→PJにもよるが、多くの場合、名前の衝突がない場合はthisを省略するのが一般的  
```cpp
class MyClass {
public:
    int value;

    MyClass(int value) {
        // メンバ変数のvalueとコンストラクタのパラメータのvalueが衝突
        this->value = value; // this-> を使用してメンバ変数valueを指定
    }
};
```

## noexcept（例外の禁止）
noexcept指定された関数が例外を投げると、std::terminateが呼び出され、プログラムが異常終了する。
例外を投げないことが保証されている場合、コンパイラは特定の最適化を適用することができるため、
パフォーマンスが向上する可能性がある。

### 条件付きnoexcept
noexceptの後にbool値を指定することで、例外を投げるかどうかを明示的にできる。

```cpp
void myFunction() noexcept(true); // 例外を投げないことを保証
void anotherFunction() noexcept(false); // 例外を投げる可能性がある
```

### noexcept演算子
関数を例外禁止化する。  
```cpp
bool noThrow = noexcept(myFunction());
```
