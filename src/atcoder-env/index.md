<!-- -*- coding:utf-8-unix -*- -->

# AtCoder運営者向けの情報

本章では以下の内容について説明します。

- Rustツールチェインやクレート（外部ライブラリ）をジャッジサーバへインストールする手順
- 提出されたプログラムのコンパイルおよび実行手順


## Rustツールチェインやクレートの内容など

- Rust 1.35.0
- インストールするクレート：[このページ][crates-2019]を参照

ユーザプログラム（選手が提出したプログラム）がクレートを使用しない場合、インストールしたクレートはユーザプログラムの実行ファイルになんの影響も与えません。
素のRust環境を別途用意する必要はありません

[crates-2019]: https://github.com/rust-lang-ja/atcoder-rust-resources/wiki/Crates-2019


## 内容についてのお問い合わせなど

内容について質問などがありましたら、[GitHub Issue][gh-issue]などでご連絡ください。

なお本章のMarkdownソースファイルはGitHub [rust-lang-ja/atcoder-rust-resources][gh]にあります。

[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues
[gh]: https://github.com/rust-lang-ja/atcoder-rust-resources
