# [cargo] ファイルレイアウトとコンパイルの流れ

選手がRustプログラムを提出すると、その内容がジャッジサーバ上のファイルシステムに書き出されます。
ジャッジサーバがどのような設計になっているかわからないので、ここでは仮にLinuxユーザのホームディレクトリ配下に`WORKAREA`という作業用の一時ディレクトリができるものとします。

まずは先ほど依存クレートをコンパイルするのに利用したプロジェクトのディレクトリを、そっくり`WORKAREA`内に丸ごとコピーしてください。依存クレートをコンパイルするのに利用したプロジェクトは、本書の通りにセットアップした場合`/usr/local/lib/rust/lib`となっているはずです。

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

そして、この`WORKAREA/lib/main.rs`をユーザーが入力したファイルにそっくりそのまま置き換えます。

以上でCargoプロジェクトが整いました。

# コンパイルコマンド

ツールチェインのインストールのページで説明したように、コンパイルを実行するシェルに以下の環境変数が設定されている必要があります。

```console
$ echo $RUST_HOME
/usr/local/lib/rust

$ echo $RUSTUP_HOME
/usr/local/lib/rust/rustup

$ echo $CARGO_HOME
/usr/local/lib/rust/cargo

$ echo $PATH
... /usr/local/lib/rust/cargo/bin; ...
```

コンパイルコマンドは以下の通りです。

```console
$ cd $HOME/WORKAREA/lib
$ cargo build --release --offline
```

すると`$HOME/WORKAREA/lib/target/release`ディレクトリに`atcoder-rust-base`という名前の実行ファイルが生成されます。


## コンパイルオプションについて

Cargoを利用する場合、ネットワーク接続がない環境でも安定して動作させるため、`--offline`オプションを利用します。それ以外は、通常のRustプロジェクトのビルドと同様です。


## コンパイルコマンドが動作することの確認

実際のコンテストでは`WORKAREA/lib/main.rs`ファイルをユーザーの入力でそっくり置き換えて実行するのですが、置き換える前の`WORKAREA/lib/main.rs`には各依存クレートに対応する簡単なテストコードが含まれています。[クレートをインストールした際に確かめたのと同様の方法で](./installing-rust-crates.md#クレートのコンパイル)このファイルがコンパイルできることを確かめましょう。

まずは動作の確認です。

```console
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

続いてコンパイルができることを確認します。

```console
$ cargo build --release --offline
(...略...)
    Finished release [optimized] target(s) in 0.86s
```

上のように`Finished`が出力されれば、実行可能バイナリが`$HOME/WORKAREA/lib/target/release/atcoder-rust-base`に生成されているはずです。


## 実行コマンドが動作することの確認

実行コマンドが動作することを確認しましょう。
先ほど作成した実行ファイルを実行します。

```console
## 一般ユーザで実行

$ cd $HOME/WORKAREA

$ target/release/atcoder-rust-base
...

## いろいろなメッセージが表示されるが、テスト用の出力なので内容は確認しなくてよい
## 終了コード0で終了したことだけを確認すればOK

$ echo $?
0
```

AtCoder運営者向けの情報は以上となります。
内容について質問などがありましたら、[GitHub Issue][gh-issue]などでご連絡ください。

[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues
