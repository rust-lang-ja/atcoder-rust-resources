<!-- -*- coding:utf-8-unix -*- -->

# AtCoder運営者向けの情報

本章では以下の内容について説明します。

- Rustツールチェインやクレート（外部ライブラリ）をジャッジサーバへインストールする手順
- 提出されたプログラムのコンパイルおよび実行手順


## Rustツールチェインやクレートの内容など

- Rust 1.36.0
- インストールするクレート：[このページ][crates-2019]を参照

[crates-2019]: https://github.com/rust-lang-ja/atcoder-rust-resources/wiki/Crates-2019


## 内容についてのお問い合わせなど

内容について質問などがありましたら、[GitHub Issue][gh-issue]などでご連絡ください。

なお本章のMarkdownソースファイルはGitHub [rust-lang-ja/atcoder-rust-resources][gh]にあります。

[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues
[gh]: https://github.com/rust-lang-ja/atcoder-rust-resources


## 素のRust環境について

[AtCoder 2019/7 Language Updateのスプレッドシート][spreadsheet]では、従来のようにクレートがインストールされていない「素のRust環境」の併設を望む声もあるようです。

[spreadsheet]: https://docs.google.com/spreadsheets/d/1PmsqufkF3wjKN6g1L0STS80yP4a6u-VdGiEv5uOHe0M/edit

そのような環境を望む理由については、正直な話、よくわかりません。
本章の手順に従ってセットアップした環境が問題なく動作するなら、わざわざ別の環境を用意する必要はないからです。
もしユーザプログラム（選手が提出したプログラム）がクレートを使わないなら、本章の手順でインストールしたクレートはないものと同じになります。
コンパイルにかかる時間やコンパイルによって生成される実行ファイルには影響を与えません。

もしかしたら本章でインストールしたツール（クレート検索パス生成ツール）が何かのタイミングでうまく動かなくなり、コンパイルできなくなるなどの障害が起こることを心配しているのかもしれません。とはいえジャッジサーバの環境は固定されていますので、そういうことは起こりにくいと考えられます。

もしそれでも欲しいという声があるなら、素のRust環境を別途用意することも技術的には可能です。


### 方法1：環境は1つのまま、コンパイルコマンドを2種類用意する

1つ目の方法は、環境自体は本章の手順で1つだけセットアップして、コンパイルコマンドを切り替えることです。

本章でセットアップする環境では、以下のようにクレート検索パス生成ツール（`rustc-dep-option-generator`コマンド）付きでコンパイラを実行します。

```console
$ RUST_LIB=$RUST_HOME/lib
$ rustc --edition=2018 -C opt-level=3 \
    $(rustc-dep-option-generator $RUST_LIB/Cargo.toml $RUST_LIB/target/release/deps) \
    main.rs
```

素の環境、つまりクレートがインストールされていない環境と同じにするには、クレート検索パス生成ツールを使わずにコンパイラを実行します。

```console
$ rustc --edition=2018 -C opt-level=3 main.rs
```

これならクレート検索パス生成ツールが動かなくなる可能性を心配しないですみます。


### 方法2：文字通り2つの環境を用意する

2つ目の方法は、本章の環境に加えて、素のRust環境も用意することです。
シェルの環境変数`RUSTUP_HOME`と`CARGO_HOME`を本章のものとは違う値に設定して（または`unset`して）`rustup`をインストールすれば、本章の環境とは別の場所にRustツールチェインがインストールでされます。

コンパイルの際も環境変数の値を変えることで、本章でセットアップする環境と、素のRust環境を切り替えます。

| 環境変数 | 本章でセットアップする環境（クレートを使用できる環境）を使用する場合 | 素のRust環境を使用する場合 |
|:--|:--|:--|
| `RUSTUP_HOME` | `/usr/local/lib/rust/rustup` | `rustup`をデフォルトの場所にインストールしたのなら`unset RUSTUP_HOME` |
| `CARGO_HOME` |`/usr/local/lib/rust/cargo` | `cargo`をデフォルトの場所にインストールしたのなら`unset CARGO_HOME` |
