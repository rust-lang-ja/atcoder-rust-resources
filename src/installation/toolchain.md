<!-- -*- coding:utf-8-unix -*- -->

# Rust ツールチェインのインストール

**TODO** このページは書きかけです。

> Note: インストール方法は適宜変わる可能性があります。情報が古いまたは不足している場合は、公式の [book](https://doc.rust-lang.org/book/ch01-01-installation.html) にある解説を適宜参照ください (英語) 。

まずは Rust 本体をインストールする必要があります。


## ステップ 1: Rust に必要になる依存関係を追加する

OS によっては Rust を実行するのに追加のプログラムが必要になりますので、まずはそれをインストールしましょう。

- Windows

    (TODO: Visual Studio Build Tools をインストール)

- macOS

    (TODO: Cコンパイラ (に付属するリンカー) のインストール)

- Linux (Windows Subsystem for Linux を含む)  

    (TODO: Cコンパイラ (に付属するリンカー) のインストール)


## ステップ 2: Rustup のインストールスクリプトを実行する

OS によっては標準のパッケージ管理に Rust のパッケージが存在することもありますが、ここでは Rustup という Rust のバージョン管理プログラムを通してインストールすることを前提とします。このプログラムを使うと、最新版へのバージョンアップや特定のバージョンのインストールがコマンド一つで実行できます。また、そのようにしてインストールされた複数のバージョンの Rust を簡単に使い分けることができます。

<https://rustup.rs/> にアクセスすると、お使いのプラットフォームに合わせてインストールスクリプトを実行する方法が表示されます。大きくは Windows と macOS/Linux で分かれます。

- Windows

    `rustup-init.exe` のような実行ファイルのダウンロードリンクが示されていると思いますので、それをダウンロードして実行します。

- macOS / Linux (Windows Subsystem for Linux 含む)

    `curl` を用いてスクリプトをダウンロード・実行する方法が示されていると思います。書かれているコマンドをターミナルへ入力してください。

(TODO: 以下、スクリプトの指示に従ってインストールする方法とインストールを確認する方法を書く)


## ステップ 3: インストールされたことを確認する

インストール終了後、 Windows の方はコマンド プロンプト、 macOS または Linux の方は端末を開き、次のように入力してみましょう。次のようにバージョン情報が表示されればインストールは完了です。もしコマンドが見つからない、のようなエラーメッセージが表示される場合は、環境変数がまだ反映されていない可能性があります。一度再起動をして、もう一度試してみてください。

```console
$ rustc --version
rustc 1.42.0 (b8cedc004 2020-03-09)
```
