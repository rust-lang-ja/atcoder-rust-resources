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


## クレートのテスト

一度、導入したクレートが正しく動作するのかを確認しましょう。

テストコードは`$RUST_HOME/lib/src/main.rs`にあります。実際に導入したクレートに応じて適宜テストをコメントアウトしてください。テストの実行には、`cargo test`コマンドを入力します。

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

