<!-- -*- coding:utf-8-unix -*- -->

# `cargo-generate` のインストール

AtCoder で利用できる Rust のバージョンやライブラリを指定したプロジェクトを簡単に生成するために、 [atcoder-rust-base の ja ブランチ](https://github.com/rust-lang-ja/atcoder-rust-base/tree/ja)にプロジェクトの雛形を用意しています。この雛形は`cargo generate`というコマンドを使って展開するため、先にこのコマンドをインストールする必要があります。

`cargo generate` サブコマンドを利用できるようにするには `cargo-generate` が必要です。端末 (Windows ユーザーの方はコマンド プロンプト) を開いて `cargo install cargo-generate` コマンドを実行してください。これだけでインストールは完了です。

```console
$ cargo install cargo-generate
    Updating crates.io index
Downloaded cargo-generate v0.5.0
(...中略...)
    Compiling cargo-generate v0.5.0
    Finished release [optimized] target(s) in 5m 46s
Installing ...
    Installed package `cargo-generate v0.5.0` (executable `...`)
```

実際に `cargo generate --help` として実行できるかどうかを確認しましょう。

```console
$ cargo generate --help
cargo-generate 0.5.0
Ashley Williams <ashley666ashley@gmail.com>
cargo, make me a project

USAGE:
    cargo generate [FLAGS] [OPTIONS] --git <git>

FLAGS:
    -f, --force      Enforce to create a new project without case conversion of project name
    -h, --help       Prints help information
    -V, --version    Prints version information
    -v, --verbose

OPTIONS:
        --branch <branch>
        --git <git>
    -n, --name <name>
```
