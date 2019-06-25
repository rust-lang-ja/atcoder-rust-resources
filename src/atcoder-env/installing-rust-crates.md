<!-- -*- coding:utf-8-unix -*- -->

# Rustクレート（外部ライブラリ）のビルドとインストール

本ページではAtCoderのジャッジサーバにRustの外部ライブラリであるクレートをインストールする手順を説明します。


## インストールするクレートについて

今回インストールの対象となるクレートは、Slack上のRust日本語コミュニティ「rust-jp」のメンバーによって選定されました。

選定にあたって、以下の点を重視しました。

- 他のいくつかの言語の標準ライブラリにあって、Rustにない機能
  - 例：C++の`lower-bound`や`BitSet`に相当する機能
- 競技プログラミングの面白さを損なわない範囲（ズルにならない範囲）で便利になるもの
  - 例：Rustでは入出力関連の記述が煩雑になりがちなので、それらを簡潔に記述できるマクロ
- セキュリティなどの理由から高速性が犠牲になっている機能の置き換え
  - 例：標準ライブラリのハッシュ関数はDoS攻撃を避けるために暗号強度があり、計算量が多い。
    競技プログラミングでは暗号強度は求められず、より計算量が少ないハッシュ関数で十分

対象クレートの一覧は このページ（**TODO**） にあります。
導入して問題がないか、インストール前にレビューをお願いいたします。

質問などがありましたら、[GitHub Issue][gh-issue]などでご連絡ください。

[gh-issue]: https://github.com/rust-lang-ja/atcoder-rust-resources/issues


## ファイルレイアウトとクレートの事前コンパイル

一般的な`cargo`を使用した開発では、`Cargo.toml`という設定ファイルに依存クレートの情報を記述します。
`cargo build`コマンドを実行すると、`cargo`はそれらのクレートのソースコードをダウンロードし、適切なオプションと共に`rustc`を実行することでクレートをコンパイルします。
クレートのソースコードは`$CARGO_HOME/registory/cache`にダウンロードされ、コンパイル済みのクレート（`rlib`ファイル）は`Cargo.toml`が置かれたディレクトリを起点とする相対ディレクトリ`./target/release/deps`配下に出力されます。

**一般的な開発時のファイルレイアウト**

```console
$HOME   # ユーザのホームディレクトリ
|-- $CARGO_HOME (~/.cargo)
|   └-- registory
|       └-- cache
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
            └-- main        # コンパイル、リンク済みの実行可能ファイル（バイナリ）
```

この方法ではユーザプログラムをコンパイルする際にクレートもコンパイルします。
もしジャッジサーバでこの方法をとると、ジャッジの際のサーバ負荷が増えてしまいます。

そこで今回は各ファイルを以下のように配置し、クレートのコンパイルは導入時に済ませておくことにします。
またダウンロードしたクレートのソースコードはコンパイル後は不要ですので削除します。

**AtCoderジャッジサーバでのファイルレイアウト（導入時に配置するもの）**

```console
$RUST_HOME (/usr/local/lib/rust)
|-- $CARGO_HOME (/usr/local/lib/rust/cargo)
|   └-- registory
|       └-- cache
|           └-- クレートのソースコード　（コンパイル後に削除する）
└-- lib
    |-- Cargo.toml          # 設定ファイル。依存クレートの情報が書かれている
    └-- target
        └-- release
            └-- deps
                |-- X.rlib  # コンパイル済みのクレート
                └-- Y.rlib
```

そしてジャッジの際には、`cargo`を使用せず、`rustc`に適切なオプション（ライブラリ検索パスなど）を与えて実行することで、事前にコンパイルしておいた`rlib`ファイルとリンクさせます。

**AtCoderジャッジサーバでのファイルレイアウト（ジャッジの際に作成する）**

```console
$HOME             # ユーザのホームディレクトリ
└-- WORKAREA      # ジャッジ用の一時ディレクトリ
    |-- main.rs   # ユーザプログラム（提出されたプログラム）のソースコード
    └-- main      # コンパイル、リンク済みの実行可能ファイル（バイナリ）
```


## クレートのコンパイルに使用するCargoパッケージのダウンロード

それではインストールしましょう。
クレートの事前コンパイルに使用するCargoパッケージをGitHubからダウンロードし、`/usr/local/lib/rust/lib`に配置します。
このパッケージには`Cargo.toml`ファイルなどが含まれており、対象となるクレートがあらかじめ設定されています。
以下のコマンドを実行します。

```console
## rootユーザで作業する
$ sudo -i
# whoami
root

# echo $RUST_HOME
/usr/local/lib/rust

## Cargoパッケージをgit cloneする
# git clone https://github.com/rust-lang-ja/atcoder-rust-base.git \
    --branch ja-full --single-branch \
    ${RUST_HOME}/lib
```

依存クレートの情報は`Cargo.toml`ファイルの`[dependencies]`セクションに書かれています。


## クレートのコンパイル

クレートをコンパイルしましょう。
以下のコマンドを実行します。

```console
# cd ${RUST_HOME}/lib
# cargo build --release
```

コンパイルに成功すると以下のように表示されます。

```console
# cargo build --release
Compiling autocfg v0.1.4
...（中略）...

Finished release [optimized] target(s) in 1m 39s
$
```

`*.rlib`ファイルが作られていることを確認します。
以下のコマンドを実行します。

```console
$ find target/release/deps/ -name '*.rlib' | wc -l
58   # この数字を確認
```

なお、`$CARGO_HOME/registory/cache`の内容は、次のページの作業が終わった後に削除します。
