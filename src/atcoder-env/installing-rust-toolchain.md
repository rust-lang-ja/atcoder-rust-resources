<!-- -*- coding:utf-8-unix -*- -->

# Rustツールチェインのインストール

このページではAtCoderのジャッジサーバにRustツールチェインをインストールする手順を説明します。


## Rustバージョン

今回の言語アップデートでは2019年11月7日にリリースされた1.39.0をインストールします。

Rustの安定版（stable版）は6週間ごとにリリースされますので、既にバージョン1.40.0が12月19日にリリースされています。ただ、安定性のためには世に出てからある程度の時間が経っているバージョンを選ぶ方が望ましいと考えられますので、ここでは1つ前のバージョンをインストールします。様子を見る期間については、現在のところはおよそ次期バージョンがリリースされるまでを1つの目安としています。

ツールチェインのインストールには`rustup`というRustプロジェクト公式のコマンドラインツールを使います。
これにより特定のバージョンのRustをインストールすることが可能になります。


## ツールチェインの内容とインストール先

今回インストールするRustツールチェインには以下のものが含まれています。

- Rustコンパイラである`rustc`コマンド
- Rustのビルドツール兼パッケージマネージャである`cargo`コマンド
- Rustの標準ライブラリとAPIドキュメント

実際のコンパイルにもCargoを利用する場合はもちろんですが、運用時には`rustc`に必要なオプションを付けてコンパイルする方法をとる場合でも、依存ライブラリをまとめてコンパイルするためにCargoを利用します。具体的にはこのページに続く数ページをご覧ください。

Rustツールチェインはデフォルトでは`rustup`を実行したLinuxユーザのホームディレクトリ配下（`~/.rustup/toolchains`）にインストールされます。
しかし今回はジャッジサーバ上の全てのLinuxユーザから使えるよう、`/usr/local/lib/rust`配下にインストールします。
このディレクトリは一般のLinuxユーザからは書き込みができないように設定し、ジャッジの際にツールチェインがユーザプログラムによって変更されないよう保護します。


## 依存ソフトウェアのインストール

Rustツールチェインをインストールする前に、Rustプログラムのリンクやクレートのビルドに必要なソフトウェアをインストールしましょう。
必要なソフトウェアは以下のとおりです。

- Rustツールチェインやクレートのダウンロードに必要なツール：curl, git-core
- ユーザプログラムのリンク時に必要なツール：gcc, binutils
- 一部のRustクレートのビルドに必要なツール：make, pkg-config

以下のコマンドを実行します。

```console
$ sudo apt update
$ sudo apt install -y curl git-core gcc binutils make pkg-config
```


## Rustツールチェインのインストール

`rustup`とRustツールチェインをインストールしましょう。
以下のコマンドを実行します。

```console
$ sudo -i

# whoami
root

# RUST_TOOLCHAIN=1.39.0
# export RUST_HOME=/usr/local/lib/rust
# export RUSTUP_HOME=${RUST_HOME}/rustup
# export CARGO_HOME=${RUST_HOME}/cargo

# mkdir -p $RUST_HOME
# chmod 0755 $RUST_HOME

## rustupをインストールし、同時に指定したバージョンのRustツールチェインをインストールする
# curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | \
    sh -s -- -y --default-toolchain "${RUST_TOOLCHAIN}" --no-modify-path
```

インストールに成功すると以下のように表示されます。

```console
info: downloading installer
info: syncing channel updates for '1.39.0-x86_64-unknown-linux-gnu'
info: latest update on 2019-11-07, rust version 1.39.0 (4560ea788 2019-11-04)
info: downloading component 'rustc'
info: downloading component 'rust-std'
info: downloading component 'cargo'
...（中略）...

  1.39.0 installed - rustc 1.39.0 (4560ea788 2019-11-04)


Rust is installed now. Great!

To get started you need Cargo's bin directory (/usr/local/lib/rust/cargo/bin)
in your PATH environment variable.

To configure your current shell run source /usr/local/lib/rust/cargo/env
```


## 環境変数の設定

rootユーザと一般のLinuxユーザがRustツールチェインを使えるように設定しましょう。
それぞれのユーザのシェルの初期設定ファイルに以下のコマンドを追加します。
（`bash`の例です）

```bash
# .bashrcファイルに以下のコマンドを追加する
export RUST_HOME=/usr/local/lib/rust
export RUSTUP_HOME=${RUST_HOME}/rustup
export CARGO_HOME=${RUST_HOME}/cargo
source ${CARGO_HOME}/env
```

最後の`source`コマンドは、コマンド検索パス（`PATH`）に`/usr/local/lib/rust/cargo/bin`を追加します。


## インストール後の動作確認

Rustツールチェインが正しくインストールできたか確認しましょう。
一般のLinuxユーザで以下のコマンドを実行します。

```console
## 一般ユーザで実行

$ source ~/.bashrc

$ echo $RUSTUP_HOME
/usr/local/lib/rust/rustup

$ which rustc
/usr/local/lib/rust/cargo/bin/rustc

## バージョンなどを確認
$ rustc -V
rustc 1.39.0 (4560ea788 2019-11-04)

$ cargo -V
cargo 1.39.0 (1c6ec66d5 2019-09-30)

$ rustup -V
rustup 1.21.1 (7832b2ebe 2019-12-20)

$ rustup show
Default host: x86_64-unknown-linux-gnu

1.39.0-x86_64-unknown-linux-gnu (default)
rustc 1.39.0 (4560ea788 2019-11-04)

## Rustプログラムのビルドと実行ができることを確認
$ cd /tmp
$ cargo new hello && cd $_
$ cargo run
Hello, world!   # このように表示されればOK

$ cd
$ rm -rf /tmp/hello
```
