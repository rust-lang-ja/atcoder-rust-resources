<!-- -*- coding:utf-8-unix -*- -->

# クレート検索パス生成ツールのインストール

前のページで以下のことを説明しました。

> ジャッジの際には、`cargo`を使用せず、`rustc`に適切なオプション（ライブラリ検索パスなど）を与えて実行することで、事前にコンパイルしておいた`rlib`ファイルとリンクさせます。

このページでは`rustc`のコマンドライン・オプションを生成するツールである`rustc-dep-option-generator`のインストール手順を説明します。
このツールはRustで書かれています。


## 依存ソフトウェアのインストール

ツールが依存するシステムライブラリをインストールします。

```console
$ sudo apt update
$ sudo apt install -y libssl-dev
```

なお`libssl-dev`はツールが依存しているクレートの1つが要求しているためにインストールしますが、このツールではその機能は使いません。
ツール実行時のネットワーク・アクセスは不要です。


## ツールのインストール

`cargo install`コマンドでインストールします。
以下のコマンドを実行すると、GitHubのリポジトリからツールのソースコードがダウンロードされ、ビルドが実行されます。
ビルドに成功すると、生成された実行ファイルが`$CARGO_HOME/bin`配下にインストールされます。

```console
$ sudo -i

# whoami
root

# cargo install --git https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git
```

インストールの成功時は以下のように表示されます。

```console
# cargo install --git https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git
    Updating git repository `https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git`
  Installing rustc-dep-option-generator v0.1.0 (https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git#...)
    Updating crates.io index
  Downloaded failure v0.1.5
   ...（中略）...

   Compiling cargo v0.35.0
   Compiling rustc-dep-option-generator v0.1.0 (https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git#...)
    Finished release [optimized] target(s) in 4m 28s
  Installing /usr/local/lib/rust/cargo/bin/rustc-dep-option-generator
```


## インストール後の動作確認

ツールが動作することを確認しましょう。
以下のコマンドを実行します。

```console
## 一般ユーザで実行
$ which rustc-dep-option-generator
/usr/local/lib/rust/cargo/bin/rustc-dep-option-generator

$ echo $RUST_HOME
/usr/local/lib/rust

$ RUST_LIB=${RUST_HOME}/lib
$ rustc-dep-option-generator ${RUST_LIB}/Cargo.toml ${RUST_LIB}/target/release/deps
--extern arrayvec=/usr/local/lib/rust/lib/target/release/deps/libarrayvec-cc9c39e9e371e142.rlib
--extern hashbrown=/usr/local/lib/rust/lib/target/release/deps/libhashbrown-106405935e6124e3.rlib
...（中略）...
-L dependency=/usr/local/lib/rust/lib/target/release/deps

## ↑ Errorの文字が表示されなければOK
##
## エラーの例
## Error: StringError("failed to find appropriate path for /arrayvec-0.4.10/")

$ echo $?
0   # 0ならOK
```

これでインストール作業は終了です。
