<!-- -*- coding:utf-8-unix -*- -->

# プロジェクトを生成する

**TODO** このページは書きかけです。

まずは AtCoder サーバーと同じ環境のプロジェクトを手元に準備します。

まずは、今から参加するコンテストのソースコードを置くフォルダを準備してください。

TODO: フォルダを作成してそこに cd する方法を説明する

では、実際にプロジェクトを生成していきます。

## `cargo-generate` をインストールしている場合

`cargo generate` コマンドをインストールしている場合は、端末に次のように打ち込むことで、 AtCoder での環境と同様の環境のプロジェクトが生成できます。

```console
$ cargo generate --git https://github.com/rust-lang-ja/atcoder-rust-base --branch ja
Project Name: abc000
Creating project called `abc000`...
Done! New project created /path/to/project/abc000
```

これで表示されているパスにプロジェクトが生成されました。

## `cargo-generate` をインストールしていない場合

まずは空のプロジェクトを作成します。

```
$ cargo init abc000
     Created binary (application) package
```

そして `abc000/Cargo.toml` の `[dependencies]` 以下に AtCoder で利用できるライブラリを追加してください。 [atcoder-rust-base](https://github.com/rust-lang-ja/atcoder-rust-base) の [ja ブランチ](https://github.com/rust-lang-ja/atcoder-rust-base/tree/ja) に含まれている Cargo.toml の雛形を参考にできます。ただしこれは `cargo-generate` 用の雛形のためプロジェクト名など一部の項目がプレースホルダになっており、そのまま使えるものではないことに注意が必要です。もし完全な形の Cargo.toml をお探しなら、 [ja-all-enabled ブランチ](https://github.com/rust-lang-ja/atcoder-rust-base/tree/ja-all-enabled) に有効な形の Cargo.toml が含まれていますので、こちらを利用することもできます。
