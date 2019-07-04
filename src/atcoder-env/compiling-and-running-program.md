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
すると以下のことが行われ`main`という名の実行ファイルが生成されます。

1. プログラムがアセンブリコードに変換されオブジェクトファイルが作られる
1. オブジェクトファイルがRustの標準ライブラリや`${RUST_MOME}/lib/target/release/deps`配下に作成しておいた`rlib`とリンクされる

```console
$HOME
└-- WORKAREA
    |-- main.rs
    └-- main      # コンパイルとリンクの成果物。実行ファイル
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
$ cd $HOME/WORKAREA
$ RUST_LIB=$RUST_HOME/lib
$ rustc --edition=2018 -C opt-level=3 \
    $(rustc-dep-option-generator $RUST_LIB/Cargo.toml $RUST_LIB/target/release/deps) \
    main.rs
```

これにより`$HOME/WORKAREA`ディレクトリに`main`という名の実行ファイルが生成されます。


### コンパイルオプションについて

`rustc`コマンドに渡すコンパイルオプションは、`cargo`でデフォルトのreleaseプロファイルを使用した時に、`cargo build --release`で生成される、ごく一般的なものを使用します。
たとえばバイナリの実行速度を最適化するためのオプションとしては`-C opt-level=3`だけを指定しています。

もし実行速度を追求するなら`-C target-cpu=native`のようなオプションを追加することもできます。
しかしAtCoder運営者様から以下のようなお話があったため、今回の言語アップデートでは使用しないことにします。

[https://twitter.com/chokudai/status/1138677406984691712](https://twitter.com/chokudai/status/1138677406984691712)

> もうすぐジャッジアップデートの話を出すけれども、ジャッジの目的は「正解と不正解を分けること」であって、「最速を目指すこと」ではないので、十分に早い言語（C++, Rustなど）では、過剰な最適化とかを含めた提案はRejectすることがあるかも。

[https://twitter.com/chokudai/status/1138677959059943424](https://twitter.com/chokudai/status/1138677959059943424)

> といいつつ、言語間の差を減らすために「C++はO2とか付けない！」みたいな滅茶苦茶なことを言い出すつもりは全くなくて、「AtCoder環境がちょっと変わると動かなくなる」みたいな最適化は受け入れない、くらいの話です。

通常形式のコンテスト（アルゴリズム系）では最速を目指すことには意味がありません。

一方、マラソン系のコンテストでは、たとえ数パーセントでも速度が向上するなら嬉しいことも多くあります。
AtCoderでは将来マラソン系のコンテストもRatedにする可能性があり、その際にコンパイルオプションを追加するか検討できるかもしれません（[参考][twitter-20190619]）

[twitter-20190619]: https://twitter.com/tatsuya6502/status/1140049804082311168


### コンパイルコマンドが動作することの確認

コンパイルコマンドが動作することを確認しましょう。
クレートのインストールに使用したCargoプロジェクトには、それらのクレートを使用する簡単なサンプルコードが含まれています。

以下の方法でコンパイルできます。

```console
## 一般ユーザで実行

## 作業用のディレクトリを作成する

$ mkdir $HOME/WORKAREA
$ cd $HOME/WORKAREA

## サンプルコードの入ったソースファイル（2つある）をコピーする

$ RUST_LIB=$RUST_HOME/lib

$ cp -p $RUST_LIB/src/main.rs .
$ cp -p $RUST_LIB/tests/test_jemallocator.rs .

## サンプルコードをコンパイル、リンクする

$ rustc --edition=2018 -C opt-level=3 \
    $(rustc-dep-option-generator $RUST_LIB/Cargo.toml $RUST_LIB/target/release/deps) \
    main.rs

$ rustc --edition=2018 -C opt-level=3 \
    $(rustc-dep-option-generator $RUST_LIB/Cargo.toml $RUST_LIB/target/release/deps) \
    test_jemallocator.rs
```

コンパイルとリンクに成功すると実行ファイルが作られます。

```console
$ file main
main: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV),
  dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0,
  BuildID[sha1]=50373af3038c60f2a3e7a162b310e5226b08c8a9,
  with debug_info, not stripped

$ file test_jemallocator
test_jemallocator: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV),
  dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0,
  BuildID[sha1]=31b718908ebf067f4b1161b1dc5cf679f012ad7f,
  with debug_info, not stripped
```


## 実行コマンド

実行ファイルを実行するコマンドは以下のとおりです。

```console
$ cd $HOME/WORKAREA
$ ./main
```


### 実行コマンドが動作することの確認

実行コマンドが動作することを確認しましょう。
先ほど作成した実行ファイルを実行します。

```console
## 一般ユーザで実行

$ cd $HOME/WORKAREA

$ ./main
...

## いろいろなメッセージが表示されるが、テスト用の出力なので内容は確認しなくてよい
## 終了コード0で終了したことだけを確認すればOK

$ echo $?
0

$ ./test_jemallocator
...

## こちらも終了コード0で終了したことだけを確認すればOK
$ echo $?
0
```

AtCoder運営者向けの情報は以上となります。
内容について質問などがありましたら、[GitHub Issue][gh-issue]などでご連絡ください。

[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues
