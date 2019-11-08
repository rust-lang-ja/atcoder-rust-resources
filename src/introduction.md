<!-- -*- coding:utf-8-unix -*- -->

# はじめに

**TODO** このページは書きかけです。

本書はAtCoderのコンテストにRustで参加するためのガイドブックです。


## AtCoderとは？

AtCoderは、オンラインで参加できるプログラミングコンテスト(競技プログラミング)のサイトです。リアルタイムのコンテストで競い合ったり、約3000問のコンテストの過去問にいつでも挑戦することが出来ます。 ([AtCoderのトップページ](https://atcoder.jp/)より引用)

**Note:** 競技プログラミングはプログラミングで解決できるような問題をなるべく早く正確に解く競技です。競技プログラミングについてよく知らないが興味があるという方は、インターネット上に初心者向けの詳しい情報がたくさんありますので検索してみてください。AtCoderで開催されているものでは、大きく分けて二種類あります。

- 与えられる問題に対して、その解を出力するようなプログラムを書く競技 (アルゴリズム系)
    - 定期開催のもの: AtCoder Beginner Contest (ABC), AtCoder Regular Contest (ARC), AtCoder Grand Contest (AGC) があり、難易度は通常 ABC &lt; ARC &lt; AGC です。
    - 定期開催の他、企業によって開かれるコンテストもあります。そういったコンテストで上位成績をとると、その企業への就職、インターン、アルバイトなどで多少優遇されることがあります (コンテストによります) 。
    - 例: 「整数`N`が与えられます。`N`以下の正整数から等確率に1つを選ぶとき、それが奇数になる確率を求めなさい」 (AtCoder Beginner Contest 142 A問題)
    - 例: 「`N`人の身長が与えられます。`K`cm以上の人の人数を出力してください」 (AtCoder Beginner Contest 142 B問題)
- 与えられる問題に対して、少しでも良い解を出力するようなプログラムを書く競技 (マラソン系)
    - 定期開催のものはまだありません。企業が自社の取り組みやそこでの課題をテーマに出題することが多いようです。
    - 例: 「ある観測データが与えられるので、可能な限り圧縮するプログラムとそれを解凍するプログラムを書いてください」 (Wethernews Programming Competition)


## なぜRustなのか？

AtCoderで使える言語は非常にたくさんあります。どの言語を使ってもよいですし、問題によって使い分けても構いません。その中でなぜRustを使うのか、そのメリットとデメリットをまとめてみました。できるだけ一般論で比較するよう心がけますが、競技プログラミングにおけるC++人口がそれなりに多いことと、Rustはその特性上C++と比較されることが多いので、具体的にC++との比較になっている部分も多くあります。

### メリット

#### 高速である

AtCoder含め、競技プログラミングでは「実行時間制限」とよばれるものがあります。この時間内にプログラムの実行が終わらないと「TLE (Time Limit Exceeded)」という判定が付いて誤答扱いとなります。多くの場合は想定されている解法であれば多少の余裕をもって解けるように設定されていますが、非常にたくさんの言語が使える都合上全ての言語で公平になるようにはできません。遅い言語に合わせて設定すると速い言語では強引な解法でゴリ押しできてしまうことがありますし、速い言語に合わせると遅い言語では想定されている解法でも通せないということになります。いずれにせよ、基本的には速い言語であるほうが計算時間的には有利です。 (もちろん遅い言語と言われるものにも、例えば書き易さであったり、ライブラリが充実していたり、なにかしらのメリットがあるはずです。どちらかが絶対的に有利ということではありません。)

Rustは最速と言われるC/C++並みに速いとされていますので、速度面で不利になることはないと言えるでしょう。

#### 信頼性が高い

信頼性は、ここではRustの[公式トップページ](https://www.rust-lang.org/)に倣いメモリ安全性、スレッド安全性、バグの起こしにくさであるとします。競技プログラミングで特に大事になってくるのはメモリ安全性とバグの起こしにくさです。

> Reliability
> Rust’s rich type system and ownership model guarantee memory-safety and thread-safety — and enable you to eliminate many classes of bugs at compile-time.

これは様々なプログラミング言語が様々なアイデアで対処している部分です。たとえばメモリの確保と解放を正しく行うために、C++であればスマートポインタやコンテナを用意したり、他の言語ではガベージコレクタという実行時の機構を用意したりしています。RustでもC++のスマートポインタやコンテナと同様なものを用意し、自分でメモリの確保と解放を行わなくてよいようにできています。ガベージコレクタを利用すると確かに非常に安全でメモリ管理に関してほとんど何も考えなくてよいものの、そのためにほとんどのデータをヒープに置いて参照経由で扱わなければならず、実行速度にも多少影響します。かといってC++のスマートポインタやコンテナは間違った使い方が簡単にできてしまいます。特にイテレータはコレクションに対する操作を行うための標準的なツールにもかかわらず実質的に単なるポインタと同様なので、しばしば無効なイテレータが発生します。例えば (少々意図的な例ですが) 次のように簡単に問題を起こせてしまいます。

```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std::string_literals;
int main() {
    std::vector<std::string> v = {"hello"s, "world"s};
    v.shrink_to_fit();
    for (auto const &i: v) {
        if (i == "hello") v.push_back("c++"s); // イテレータを無効化してしまう
        // 未定義動作なので、プログラム全体が何を起こすか分からない
        std::cout << i << std::endl;
    }
}
```

このようなことはRustではコンパイルエラーとしてコンパイル時に検出されます。

```ignore
let mut v = vec!["hello", "world"];
for &i in &v {
    if i == "hello" {
        v.push("rust");
        // E0502: cannot borrow `v` as mutable because it is also borrowed as immutable
        // 4 | for &i in &v {
        //   |           --
        //   |           immutable borrow occurs here
        //   |           immutable borrow later used here
        // 5 |     if i == "hello" {
        // 6 |         v.push("rust");
        //   |         ^^^^^^^^^^^^^^ mutable borrow occurs here
    }
    println!("{}", i);
}
```

また、複雑なアルゴリズムにバグはつきものです。例えば、添字計算をしていてちょっとした書き間違いで配列のサイズを超えたところを参照してしまったといったことは度々起こりえます。こういうとき、例えばC/C++では配列外参照をしてしまったプログラムがどのように動作するかの保証がなく、`segmentation fault`とだけ表示して異常終了したり、たまたまうまくいってしまったり、手元で正解するケースがサーバーでは誤答となったりし得ます。こういったよく分からない動作が起きてしまうと、バグの原因特定が難しくなったり、無関係なところを原因と思い込んだりしてしまい、デバッグにかなりの時間を費やしてしまうこともしばしばあります。この配列外参照の例ではRustは必ずエラーを起こしますし、どこで起こしたかも表示してくれます。それが自分のコードでない場合でも (`-g`オプション付きでビルドされたかCargoでデバッグビルドされたバイナリであれば) バックトレースを表示させることで呼出元となる自分のコードを特定できます。

```should_panic
let x = vec![1, 2, 3];
let y = 3;
let _z = x[y]; // 配列外参照!
```

```text
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 3', (...略)
stack backtrace:
  (...中略...)
  16: alloc::vec::{{impl}}::index<i32,usize>
             at /rustc/4560ea788cb760f0a34127156c78e2552949f734\src\liballoc\vec.rs:1796
  17: test::main
             at .\test.rs:4
  (...中略...)
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
```

#### 多くの問題をコンパイル時に発見できる

RustはC/C++並みの速度を確保するため、実行時にやらなければいけないことをなるべく減らす方針の言語です。たとえば、先ほど触れたように、多くの言語にあるガベージコレクタがありません。それだけならばC++と変わりないのですが、速度と安全性を両立させるために、Rustではできるだけ多くのことをコンパイル時に確認する仕組みになっています。C++が受け入れてしまうような危険なコードもコンパイルエラーにします。

その他にも、例えば整数型同士の暗黙の型変換がない (例え`u8`から`u32`であっても) ことも、一長一短ではありますが、助かる場合があります。たとえばC++で総和をとるために`accumulate()`関数を利用する際、気をつけなければオーバーフローしないはずのところでオーバーフローを起こせてしまいます (もし符号付き整数型であったなら未定義動作にもなってしまいます) 。

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <limits>
int main() {
    std::vector<long long> s = { std::numeric_limits<long long>::max() };
    // s は1要素の配列であり、総和といってもなにもしないはず。ところが初期値を
    // `0` としてしまうと、この配列の要素の型を int 型だと思って計算するので本来
    // するはずのないオーバーフローを起こしてしまう。特に何のエラーもなし。
    std::cout << std::accumulate(s.begin(), s.end(), 0) << std::endl;
}
```

Rustではこのようなことは最初から型の不一致によりコンパイルエラーとなります。

```ignore
let s = vec![std::i64::MAX];
let x: i32 = s.into_iter().sum();
// E0277: the trait bound `i32: std::iter::Sum<i64>` is not satisfied
// 3 | let x: i32 = s.into_iter().sum();
//   |                            ^^^ the trait `std::iter::Sum<i64>` is not implemented for `i32`
```

他にもジェネリクスとトレイトの仕組みも強力です。たとえばジェネリックな関数が型変数`T`をもつとき、この`T`のとりうる型を特定のトレイト (＝機能一覧) を実装しているものだけに制限することができます。逆に`T`に対してできることはその特定のトレイトが定める機能のみです。従って、一度コンパイルが通った関数はその制約を満たす限りのどのような`T`を与えても関数の内部でコンパイルエラーとなることはありません。特にライブラリを整備するにあたって、実際に使ってみなくてもその関数が定義として成立しているかどうかが分かりますし、将来作られうるどんなユーザー定義型を与えようとも動くことが保証できます。このことは後述するコンパイルエラーの分かりやすさにも繋がっています。動的型付け言語やC++のテンプレートでは仕組み上、実際に実行または実体化してみなければエラーの存在が分かりません。これが分かりにくいエラーにも繋がっています。

#### コンパイルエラーが分かりやすい

これは少々主観的な話になるのかもしれませんが、Rustのコンパイルエラーは読みやすく分かりやすいという評判があります。実際にコンパイルエラーが発生したとき、まずエラーが起きた場所はもちろんとして、エラーが関連する他の場所 (例えば以前に借用された場所など) などをアスキーアート的な手法で視覚的に分かりやすく表示してくれます。さらに、なぜそれが間違っているのか/それをどのように修正することができるかのヒントが提示されることもあります。例えば、先ほどのエラー全体は次のようになっていました。

```console
error[E0277]: the trait bound `i32: std::iter::Sum<i64>` is not satisfied
--> (filename):3:32
|
3 |     let x: i32 = s.into_iter().sum();
|                                ^^^ the trait `std::iter::Sum<i64>` is not implemented for `i32`
|
= help: the following implementations were found:
            <i32 as std::iter::Sum<&'a i32>>
            <i32 as std::iter::Sum>

error: aborting due to previous error

For more information about this error, try `rustc --explain E0277`.
```

またRustでは、トレイト/ジェネリクスの仕組みによりエラーの所在が明確化されています。C++のテンプレートでは莫大で難解な上に根本的な原因がどこにあるのかが分かりにくいエラーメッセージがしばしば見られますが、Rustではそういったことは比較的起きづらいと言えるでしょう。

一部のエラーには詳細な説明が用意されており、`rustc --explain (エラーコード)`とすることにより、そのエラーが何を言っているのか/どういうコードでそのエラーが発生するのかなどをもりこんだ詳細な解説を読むこともできます。たとえば上のエラーの`E0277`にも用意されています。

    $ rustc --explain E0277
    You tried to use a type which doesn't implement some trait in a place which
    expected that trait. Erroneous code example:

    ```
    // here we declare the Foo trait with a bar method
    trait Foo {
        fn bar(&self);
    }

    // we now declare a function which takes an object implementing the Foo trait
    fn some_func<T: Foo>(foo: T) {
        foo.bar();
    }

    fn main() {
        // we now call the method with the i32 type, which doesn't implement
        // the Foo trait
        some_func(5i32); // error: the trait bound `i32 : Foo` is not satisfied
    }
    ```
    (以下略)

### デメリット

