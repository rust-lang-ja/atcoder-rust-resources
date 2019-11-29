<!-- -*- coding:utf-8-unix -*- -->

# （オプション）クレート検索パス生成ツールのインストール

**Note:** この作業は実際のビルドに`rustc`を利用する場合で、クレート検索パスを指定するコンパイルオプションをツールで生成する場合に必要となります。実際のビルドで`cargo`を利用する場合と`cargo build -v`などの出力を元にしてクレート検索パスを指定するコンパイルオプションを手書きする場合などはツールは不要です。

このページでは`rustc`のコマンドライン・オプションを生成するツールである`rustc-dep-option-generator`のインストール手順を説明します。
このツールはRustで書かれています。


## 依存ソフトウェアのインストール

ツールが依存するシステムライブラリをインストールします。

```console
$ sudo apt update
$ sudo apt install -y libssl-dev
```

なお`libssl-dev`は`rustc-dep-option-generator`が`cargo`をライブラリとして用いているために必要です。

## ツールのインストール

`cargo install`コマンドでインストールします。
以下のコマンドを実行すると、GitHubのリポジトリからツールのソースコードがダウンロードされ、ビルドが実行されます。
ビルドに成功すると、生成された実行ファイルが`$CARGO_HOME/bin`配下にインストールされます。

```console
$ sudo -i

# whoami
root

# cargo install --git https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git
```

インストールの成功時は以下のように表示されます。

```console
# cargo install --git https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git
    Updating git repository `https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git`
  Installing rustc-dep-option-generator v0.2.0 (https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git#...)
    Updating crates.io index
  Downloaded failure v0.1.5
   ...（中略）...

   Compiling cargo v0.35.0
   Compiling rustc-dep-option-generator v0.2.0 (https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git#...)
    Finished release [optimized] target(s) in 4m 28s
  Installing /usr/local/lib/rust/cargo/bin/rustc-dep-option-generator
```


## インストール後の動作確認

ツールが動作することを確認しましょう。
以下のコマンドを実行します。

```console
## 一般ユーザで実行
$ which rustc-dep-option-generator
/usr/local/lib/rust/cargo/bin/rustc-dep-option-generator

$ echo $RUST_HOME
/usr/local/lib/rust

$ rustc-dep-option-generator --help
rustc-dep-option-generator 0.2.0
rust-lang-ja Developers
A program to generate Rust compiler options to locate prebuilt crates.

USAGE:
    rustc-dep-option-generator [OPTIONS]

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

OPTIONS:
        --manifest-path <PATH>    Path to Cargo.toml
        --format <FORMAT>         Output format [default: shell]  [possible values: shell, json]

$ RUST_LIB=${RUST_HOME}/lib
$ rustc-dep-option-generator
...（中略）...
       Fresh atcoder-rust-base v0.1.0 (/usr/local/rust/lib)
    Finished release [optimized] target(s) in 0.06s
--extern arrayvec=/usr/local/lib/rust/lib/target/release/deps/libarrayvec-cc9c39e9e371e142.rlib
--extern hashbrown=/usr/local/lib/rust/lib/target/release/deps/libhashbrown-106405935e6124e3.rlib
...（中略）...
-L dependency=/usr/local/lib/rust/lib/target/release/deps

## ↑ errorの文字が表示されなければOK
##
## エラーの例
## error: failed to find appropriate path for /arrayvec-0.4.10/

$ echo $?
0   # 0ならOK
```

これでインストール作業は終了です。
