<!-- -*- coding:utf-8-unix -*- -->

# コンパイルコマンドと実行コマンド

このページではジャッジサーバでユーザプログラムをコンパイルし、実行するためのコマンドを説明します。


## ファイルレイアウトとコンパイルの流れ

選手がRustプログラムを提出すると、その内容がジャッジサーバ上のファイルシステムに書き出されます。
ジャッジサーバがどのような設計になっているかわからないので、ここでは仮にLinuxユーザのホームディレクトリ配下に`WORKAREA`という作業用の一時ディレクトリができるものとします。

Rustプログラムは`WORKAREA`の`main.rs`に書き出すようにジャッジサーバを設定してください。

```console
$HOME             # ユーザのホームディレクトリ
└-- WORKAREA      # ジャッジ用の一時ディレクトリ
    └-- main.rs   # ユーザプログラム（提出されたプログラム）のソースコード
```

`WORKAREA`に`cd`で移動して、後述のコンパイルコマンドを実行します。
すると以下のことが行われ`main`という名の実行可能ファイル（バイナリ）が生成されます。

1. プログラムがアセンブリコードに変換されオブジェクトファイルが作られる
1. オブジェクトファイルがRustの標準ライブラリや`${RUST_MOME}/lib/target/release/deps`配下に作成しておいた`rlib`とリンクされる

```console
$HOME
└-- WORKAREA
    |-- main.rs
    └-- main      # コンパイルとリンクの成果物。実行可能ファイル（バイナリ）
```


## コンパイルコマンド

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

コンパイルコマンドは以下のとおりです。

```console
$ cd ${HOME}/WORKAREA
$ RUST_LIB=${RUST_HOME}/lib
$ rustc --edition=2018 -C opt-level=3 \
    $(rustc-dep-option-generator ${RUST_LIB}/Cargo.toml ${RUST_LIB}/target/release/deps) \
    main.rs
```

これにより`${HOME}/WORKAREA`ディレクトリに`main`という名のバイナリが生成されます。


### コンパイルオプションについて

**TODO**


## 実行コマンド

バイナリを実行するコマンドは以下のとおりです。

```console
$ cd ${HOME}/WORKAREA
$ ./main < 入力データファイル
```
