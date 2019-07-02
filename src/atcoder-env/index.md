<!-- -*- coding:utf-8-unix -*- -->

# AtCoder運営者向けの情報

本章では以下の内容について説明します。

- Rustツールチェインやクレート（外部ライブラリ）をジャッジサーバへインストールする手順
- 提出されたプログラムのコンパイルおよび実行手順


## Rustツールチェインやクレートの内容など

- Rust 1.35.0
- インストールするクレート：[このページ][crates-2019]を参照

[crates-2019]: https://github.com/rust-lang-ja/atcoder-rust-resources/wiki/Crates-2019


## 素のRust環境について

[AtCoder 2019/7 Language Updateのスプレッドシート][spreadsheet]では、従来のようにクレートがインストールされていない「素のRust環境」の併設を望む声もあるようです。

[spreadsheet]: https://docs.google.com/spreadsheets/d/1PmsqufkF3wjKN6g1L0STS80yP4a6u-VdGiEv5uOHe0M/edit

本章の内容に従って環境をセットアップした場合に、ユーザプログラム（選手が提出したプログラム）が従来のようにクレートを使用しないなら、インストールしたクレートはないものと同じになります。
使用しないクレートはユーザプログラムの実行ファイルになんら影響を与えません。
このことから素のRust環境を別途用意する必要はなさそうに思えます。

もしそれでも心配という声があるようなら、素のRust環境を別途用意することも可能です。
その場合は以下の環境変数の値を変える（または`unset`する）ことで、本章でセットアップする環境と、素のRust環境を切り替えることができます。

| 環境変数 | 本章でセットアップする環境（クレートを使用できる環境）を使用する場合 | 素のRust環境を使用する場合 |
|:--|:--|:--|
| `RUSTUP_HOME` | `/usr/local/lib/rust/rustup` | `rustup`をデフォルトの場所にインストールしたのなら`unset RUSTUP_HOME` |
| `CARGO_HOME` |`/usr/local/lib/rust/cargo` | `cargo`をデフォルトの場所にインストールしたのなら`unset CARGO_HOME` |


## 内容についてのお問い合わせなど

内容について質問などがありましたら、[GitHub Issue][gh-issue]などでご連絡ください。

なお本章のMarkdownソースファイルはGitHub [rust-lang-ja/atcoder-rust-resources][gh]にあります。

[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues
[gh]: https://github.com/rust-lang-ja/atcoder-rust-resources
