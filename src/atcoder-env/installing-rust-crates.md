<!-- -*- coding:utf-8-unix -*- -->

# クレートのコンパイルとインストール

このページではAtCoderのジャッジサーバにRustの外部ライブラリであるクレートをインストールする手順を説明します。


## インストールするクレートについて

今回インストールするクレートは、SlackのRust日本語コミュニティ「rust-jp」のメンバーがAtCoderでぜひ使いたいと考えているものです。
大半は既存のクレートですが、この機会に新たに開発したものもあります。

これらのクレートは主に以下の種類に分類されます。

- Rust の標準ライブラリにないが、他のいくつかの言語では標準の機能であって、かつ競プロ以外でも広く使われているもの
  - 例：C++の`lower_bound`や`bitset`に相当する機能
- 競技プログラミング特化型だが、競プロの面白さを損なわない範囲（ズルにならない範囲）で便利になるもの
  - 例：Rustでは入力関連の記述が煩雑になりがちなので、それらを簡潔に記述できるマクロ集
- セキュリティなどの理由から高速性が犠牲になっている機能を置き換え、特にマラソンにおいて威力を発揮するもの
  - 例：標準ライブラリのハッシュ関数はDoS攻撃を避けるために暗号強度があり、計算量が多い。
    競技プログラミングでは暗号強度は求められず、より計算量の少ないハッシュ関数で十分


### AtCoder運営者様へのお願い

対象クレートの一覧は[このページ][crates-2019]にあります。
ジャッジサーバで使用して問題ない内容か、インストール前にレビューをお願いいたします。

質問や要望などがありましたら、[GitHub Issue][gh-issue]などでご連絡ください。

[crates-2019]: https://github.com/rust-lang-ja/atcoder-rust-resources/wiki/Crates-2019
[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues


## ファイルレイアウトとクレートの事前コンパイル

インストール作業に入る前に、背景を説明します。
一般的なCargoを使用した開発では、`Cargo.toml`という設定ファイルに依存クレートの情報を記述します。
`cargo build`コマンドを実行すると、Cargoはそれらのクレートのソースコードをダウンロードし、適切なオプションと共に`rustc`を実行することでクレートをコンパイルします。
クレートのソースコードは`$CARGO_HOME/registory/src`にダウンロードされ、コンパイル済みのクレート（`rlib`ファイル）は`Cargo.toml`が置かれたディレクトリを起点とする相対ディレクトリ`./target/release/deps`配下に出力されます。


### 一般的な開発時のファイルレイアウト

```console
$HOME   # ユーザのホームディレクトリ
|-- $CARGO_HOME (~/.cargo)
|   └-- registory
|       └-- src
|           └-- クレートのソースコード
|
└-- my-package
    |-- Cargo.toml    # 設定ファイル。依存クレートの情報が書かれている
    |-- src
    |   └-- main.rs   # Rustプログラムのソースコード
    └-- target
        └-- release
            |-- deps
            |   |-- X.rlib  # コンパイル済みのクレート
            |   └-- Y.rlib
            └-- main        # コンパイル、リンク済みの実行ファイル
```

ジャッジサーバーでは様々な条件が通常の環境と異なるため、[最初に説明した通り](./supported-environment.md#一般的なrustプログラム開発環境との違い)いくつか工夫をする必要があります。

まず、今回は各ファイルを以下のように配置し、クレートのコンパイルは導入時に済ませておくことにします。


### AtCoderジャッジサーバでのファイルレイアウト（導入時に配置するもの）

```console
$RUST_HOME (/usr/local/lib/rust)
|-- $CARGO_HOME (/usr/local/lib/rust/cargo)
|   └-- registory
|       └-- src
|           └-- クレートのソースコード
└-- lib
    |-- Cargo.toml          # 設定ファイル。依存クレートの情報が書かれている
    └-- target
        └-- release
            └-- deps
                |-- X.rlib  # コンパイル済みのクレート
                └-- Y.rlib
```

そしてジャッジの際には[以前説明した](./supported-environment.md#一般的なrustプログラム開発環境との違い)Cargoを利用する方法と`rustc`を利用する方法のいずれかを利用してコンパイルします。


### [cargo] AtCoderジャッジサーバでのファイルレイアウト（ジャッジの際に作成するもの）

```console
$HOME                      # ユーザのホームディレクトリ
└-- WORKAREA               # ジャッジ用の一時ディレクトリ
    └-- lib                # 全部入りプロジェクトをまるまるコピーしたもの
        |-- main.rs        # ユーザプログラム（提出されたプログラム）のソースコードに置き換える
        |-- Cargo.toml
        └-- target
            └-- release
                |-- deps   # (外部クレートのコンパイルキャッシュ)
                └-- atcoder-rust-base # コンパイル、リンク済みの実行ファイル
```


### [rustc] AtCoderジャッジサーバでのファイルレイアウト（ジャッジの際に作成するもの）

Cargoを使用せず、`rustc`に適切なオプション（ライブラリ検索パスなど）を与えて実行することで、事前にコンパイルしておいた`rlib`ファイルとリンクさせます。

```console
$HOME             # ユーザのホームディレクトリ
└-- WORKAREA      # ジャッジ用の一時ディレクトリ
    |-- main.rs   # ユーザプログラム（提出されたプログラム）のソースコード
    └-- main      # コンパイル、リンク済みの実行ファイル
```


## クレートのコンパイルに使用するCargoパッケージのダウンロード

それではインストール作業に入りましょう。実際のコンパイルにCargoを使う場合も`rustc`を使う場合もここは同様です。
クレートの事前コンパイルに使用するCargoパッケージは、GitHub [rust-lang-ja/atcoder-rust-base（ja-all-enabledブランチ）][rust-base-branch]に用意されています。
このパッケージには`Cargo.toml`ファイルなどが含まれており、インストール対象のクレートがすでに設定されています。
（`[dependencies]`セクションに書かれています）

このリポジトリを`git clone`し、`/usr/local/lib/rust/lib`に配置します。以下のコマンドを実行します。

```console
## rootユーザで作業する
$ sudo -i
# whoami
root

# echo $RUST_HOME
/usr/local/lib/rust

## Cargoパッケージをgit cloneする
# git clone https://github.com/rust-lang-ja/atcoder-rust-base.git \
    --branch ja-all-enabled --single-branch \
    ${RUST_HOME}/lib
```

[rust-base-branch]: https://github.com/rust-lang-ja/atcoder-rust-base/tree/ja-all-enabled


## クレートを削除する

検討の結果、一部のクレートは残念ながら導入すべきでないクレートだと判断されることもあるかと思います。そういった場合は[GitHub Issue][gh-issue]を通してご連絡頂ければ当該クレートとそれに関連するテストの削除等の対応をさせていただきますが、一応、その方法も説明しておこうと思います。

例えば、競プロ入出力補助の`proconio`が相応しくないので削除したいとなったとします。まずはこのクレートを依存から削除する必要があります。`git clone`したディレクトリ内の`Cargo.toml`ファイルを開き、`[dependencies]`セクションを見つけてください。すると例えば次のようにクレートが並んでいるかと思います。

```toml
[dependencies]
# AtCoder 2019年言語アップデート以降に使用できるクレート

# 競技プログラミングの入出力サポート
proconio = { version = "=0.3.4", features = ["derive"] }

# f64のOrd/Eq実装
ordered-float = "=1.0.2"

(...以下略...)
```

クレートによってオプションが付せられていることもありますが、`{削除したいクレート名} = ...`となっている行をコメントアウトまたは削除してください。

続いてテストを削除します。`src/main.rs`ファイルを開くと、ずらっとテスト関数が並んでいます。これらの関数から`run_{削除したいクレート名}`と`test_{削除したいクレート名}`という関数を丸ごと削除してください。`test_{削除したいクレート名}`関数は、その前の行に`#[test]`アトリビュートがついているかと思いますので、それごと削除してください。

そして`main()`関数内にある`run_{削除したいクレート名}`関数を呼び出す文を削除してください。

基本的にはこれで削除は完了です。この後クレートのテストを行いますが、ここで`unresolved import`系のエラーが出るようなら適宜削除してください。

削除するべきコードが無い場合、テストが別のファイルに分かれている場合があります。例えば`jemallocator`系を削除したいならば`tests/test_jemallocator.rs`というファイルを削除してください。

また`jemallocator`系を削除する場合、`Cargo.toml`内の以下の部分も削除してください。

```toml
[features]
jemalloc = ["jemalloc-ctl", "jemallocator"]
default = ["jemalloc"]
```

```toml
# 代替ヒープアロケータ。条件によってはシステムアロケータより速いことも
[target.'cfg(not(windows))'.dependencies]
jemallocator = { version = "=0.3.2", optional = true }
jemalloc-ctl = { version = "=0.3.3", optional = true }
```

```toml
[[test]]
name = "jemallocator"
path = "tests/test_jemallocator.rs"
required-features = ["jemalloc"]
```

[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues


## クレートのテスト

一度、導入したクレートが正しく動作するのかを確認しましょう。

テストコードは`$RUST_HOME/lib/src/`及び`$RUST_HOME/lib/tests`にあります。先に述べたように使わないクレートに関わるコードは削除してください。実行するには、`cargo test`コマンドを入力します。

```console
# cd $RUST_HOME/lib
$ cargo test --release
   Compiling atcoder-rust-base v0.1.0 (...)
    Finished release [optimized] target(s) in 7.31s
     Running target\release\deps\...

running 11 tests
test test_ascii ... okYes
Yes

test test_bitset_fixed ... ok
test test_modtype ... ok
test test_ordered_float ... ok
test test_permutohedron ... ok
test test_itertools ... ok
test test_rand_family ... ok
test test_proconio ... ok
test test_regex ... ok
test test_rustc_hash ... ok
test test_superslice ... ok

test result: ok. 11 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

多少表示されるテストの個数は異なるかもしれませんが、一番下の行が`test result: ok.`で`0 failed;`となっていれば問題ありません。

また今回、使用するクレート自体のテストを実行するためのツール、`dep-tests`を作成しました。
`dep-tests`はcargo commandのエイリアスとして、`cargo dep-tests`で起動できます。
ただし`alias`は任意のスクリプトを実行できないのでcwdが`$RUST_HOME/lib`である必要があります。
以下、cwdを`$RUST_HOME/lib`とします。

参考: [cargo で npm-scripts 的なことをする][qiita-article]

```toml
[alias]
dep-tests = ["run", "--manifest-path", "./dep-tests/Cargo.toml", "--"]
```

```console
$ cargo dep-tests --help
    Finished dev [unoptimized + debuginfo] target(s) in 0.07s
     Running `dep-tests/target/debug/dep-tests --help`
dep-tests 0.0.0
Run all of the tests in the dependency graph.

USAGE:
    dep-tests [FLAGS] [OPTIONS] <dir>

FLAGS:
        --all-features           Activate all available features
        --no-default-features    Do not activate the `default` feature
        --frozen                 Require Cargo.lock and cache are up to date
        --locked                 Require Cargo.lock is up to date
        --offline                Run without accessing the network
    -h, --help                   Prints help information
    -V, --version                Prints version information

OPTIONS:
    -p, --package <SPEC>...         Package to run test for
        --features <FEATURES>...    Space-separated list of features to activate
        --color <WHEN>              Coloring: auto, always, never
    -d, --depth <N>                 How deep in the dependency chain to search

ARGS:
    <dir>    Directory to run tests [default: /tmp/atcoder-rust-base-dep-tests]
```

```console
$ cargo dep-tests --all-features -d 1
```

いくつかのクレートは`dep-tests`で動かないのでテストしないように除外されています。
そのリストと理由は`$RUST_HOME/lib/dep-tests.toml`に記述されています。

`dep-tests`の動作の説明をしておきます。

実は`cargo`にはdependency graph上にあるクレートのテストをそのまま実行する機能があります。

```console
$ cargo test -p maplit -p num
```

しかしこれには致命的な欠点があり、[`[dev-dependencies]`が一つでもあると問答無用で][cargo-error]拒否されます。

```console
$ cargo test -p regex
error: package `regex` cannot be tested because it requires dev-dependencies and is not a member of the workspace
```

このエラーは[`rust-lang/cargo#6192`][rust-lang-cargo-pull-6192]で追加されました。
禁止されている理由はこのPRで言及されている通り、dev-dependencyがある場合ワースクペース上には無い新たなクレートが必要になることがあるためです。
`dep-tests`は『拡張』したワークスペースを新たに作成してその上でテストを実行します。

例えば`itertools v0.8.1`, `regex v1.3.1`を対象にした場合、以下のような`Cargo.toml`が生成されます。

```toml
[package]
name = "atcoder-rust-base-dep-tests"
version = "0.0.0"
edition = "2018"
publish = false

[workspace]
members = ["./itertools-0.8.1", "./regex-1.3.1"]

[patch.crates-io]
itertools = {path = "./itertools-0.8.1"}
regex = {path = "./regex-1.3.1"}

[dependencies]
_0 = {package = "aho-corasick",version = "=0.7.6",default-features = false,features = ["default", "std"]}
_1 = {package = "alga",version = "=0.9.2",default-features = false,features = ["default", "std"]}
# 略
_25 = {package = "itertools",path = "./itertools-0.8.1",default-features = false,features = ["default", "use_std"]}
# 略
_84 = {package = "regex",path = "./regex-1.3.1",default-features = false,features = ["aho-corasick", "default", "memchr", "perf", "perf-cache", "perf-dfa", "perf-inline", "perf-literal", "std", "thread_local", "unicode", "unicode-age", "unicode-bool", "unicode-case", "unicode-gencat", "unicode-perl", "unicode-script", "unicode-segment"]}
# 略
_107 = {package = "version_check",version = "=0.9.1",default-features = false,features = []}
_108 = {package = "whiteread",version = "=0.4.4",default-features = false,features = []}
```

具体的な動作は以下の通りです。

1. 現在のdependency graph上の`atcoder-rust-base`から[現在のプラットフォームに適合する][the-cargo-book-platform-specific-dependencies]、[normal-dependency][the-cargo-book-specifying-dependencies]のみで繋がれた部分グラフを求める。
   そしてその節点から`atcoder-rust-base`を除いたクレートを得る。
   またコマンドラインオプションで`-d`, `--depth`が指定されている場合さらにそのうちの一部に絞る。
   `-d 1`を指定したならば`Cargo.toml`の`[dependencies]`にあるものだけになる。
2. コマンドラインオプションの`<dir>`で指定された場所に1.のうちdev-dependencyを含むもののためのワークスペースを一つ、以下の手順で作成する。
    - 対象のクレートについて、`$CARGO_HOME/registory/src`に展開されている`.crate`ファイルの中身をコピーする。
    - このコピーしたクレートをworkspace membersとして`Cargo.toml`を作成する。
      このときダミーの`dependencies`として元のnormal-dependencyとdev-dependencyをバージョンとフィーチャを指定する。
    - `Cargo.lock`を`atcoder-rust-base`のもので上書きする。これで大体は元のバージョンが再現できる。
3. 1.のうちdev-dependencyを含まないものは`atcoder-rust-base`上で直接テストを実行する。
4. 2.で作ったワークスペース上でテストを実行する。

ワークスペースを一つにまとめることには以下の問題があり、ワークスペースを分割することで軽減できそうですがどうせ厳密なバージョン, フィーチャの保存は無理だしビルド時間と消費するディスク容量を激増させてまで分割するべきではない、と考えたため一つにまとめてしまいました。

1. 同一の名前のpackageはworkspace memberとしては共存できない
2. 既存のpackageのminor, patch versionの増加
3. オフになっていたフィーチャの有効化

[qiita-article]:                                 https://qiita.com/ubnt_intrepid/items/9600fd734e6d116bc9cb
[cargo-error]:                                   https://github.com/rust-lang/cargo/blob/0.40.0/src/cargo/ops/cargo_compile.rs#L346-L355
[rust-lang-cargo-pull-6192]:                     https://github.com/rust-lang/cargo/pull/6192
[the-cargo-book-platform-specific-dependencies]: https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html#platform-specific-dependencies
[the-cargo-book-specifying-dependencies]:        https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html#specifying-dependencies


## クレートのコンパイル

クレートをコンパイルしましょう。
以下のコマンドを実行します。

```console
# cd $RUST_HOME/lib
# cargo build --release
```

コンパイルに成功すると以下のように表示されます。

```console
# cargo build --release
...（中略）...

Finished release [optimized] target(s) in 1m 39s
$
```

`*.rlib`ファイルが作られていることを確認します。
以下のコマンドを実行します。

```console
$ find target/release/deps/ -type f | egrep -c '\.(rlib|so)$'
66
# ↑ 上の数字を確認
```
