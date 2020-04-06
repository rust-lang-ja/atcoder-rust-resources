<!-- -*- coding:utf-8-unix -*- -->

# Rust Language Server のインストール

**TODO** このページは書きかけです。

Rust Language Server (RLS) は、ソースコード編集中の様々な支援機能を提供するためのプログラムです。例えば、プログラムを編集しながらリアルタイムでエラー箇所をハイライトしたり、コード補完機能を提供したり、定義にジャンプしたり、簡単なドキュメントを表示したりといった様々な機能があります。使い慣れたエディタとともにこうした支援機能が使えることが、ローカルに Rust の環境を作る大きなメリットです。

なお、こうしたコーディング支援機能が不要だと考える方はインストールをする必要はありません。

> Note: Visual Studio Code を利用している方は、拡張機能が自動的にインストールしてくれるため、ここで直接インストールする必要はありません。

さて、 RLS は Rust 本体と同時に配布されているため、 RLS のインストールをする前に対応するバージョンの Rust をインストールしなければなりません。現在 AtCoder で使える Rust のバージョンは 1.42.0 ですので、コマンド プロンプトあるいは端末を開いて次のようにコマンドを実行します。

```console
$ rustup install 1.42.0
info: syncing channel updates for '1.42.0-x86_64-pc-windows-msvc'
info: latest update on 2020-03-12, rust version 1.42.0 (b8cedc004 2020-03-09)
info: downloading component 'cargo'
info: downloading component 'clippy'
info: downloading component 'rust-docs'
 12.0 MiB /  12.0 MiB (100 %)   5.9 MiB/s in  1s ETA:  0s
info: downloading component 'rust-std'
info: downloading component 'rustc'
 35.6 MiB /  35.6 MiB (100 %)  28.2 MiB/s in  1s ETA:  0s
info: downloading component 'rustfmt'
info: installing component 'cargo'
info: installing component 'clippy'
info: installing component 'rust-docs'
 12.0 MiB /  12.0 MiB (100 %)   2.4 MiB/s in  4s ETA:  0s
info: installing component 'rust-std'
info: installing component 'rustc'
 35.6 MiB /  35.6 MiB (100 %)  10.7 MiB/s in  3s ETA:  0s
info: installing component 'rustfmt'

  1.42.0-x86_64-pc-windows-msvc installed - rustc 1.42.0 (b8cedc004 2020-03-09)

info: checking for self-updates
```

続けて RLS をインストールします。なお必要なコンポーネントは変わるかもしれませんので、適宜 [RLS のリポジトリ](https://github.com/rust-lang/rls)も参照してください。

```console
$ rustup component add --toolchain 1.42.0 rls rust-analysis rust-src
info: downloading component 'rls'
info: installing component 'rls'
info: downloading component 'rust-analysis'
info: installing component 'rust-analysis'
info: downloading component 'rust-src'
info: installing component 'rust-src'
```

両方の実行が完了すれば、 RLS のインストールは終了です。
