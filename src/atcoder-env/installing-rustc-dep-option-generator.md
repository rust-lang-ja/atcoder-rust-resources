<!-- -*- coding:utf-8-unix -*- -->

# クレート検索パス生成プログラムのビルドとインストール

**TODO** このページは書きかけです。

- `rustc-dep-option-generator`コマンドをビルドしてインストールします。


## 依存ソフトウェアのインストール

ツールのビルドに必要なシステムライブラリをインストールします。

```console
$ sudo apt update
$ sudo apt install -y libssl-dev
```


## ツールのビルドとインストール

```console
$ sudo -i

# whoami
root

# cargo install --git https://github.com/rust-lang-ja/atcoder-rustc-dep-option-generator.git
```

ビルドとインストールに成功すると以下のように表示されます。

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

```console
## 一般ユーザで実行
$ which rustc-dep-option-generator
/usr/local/lib/rust/cargo/bin/rustc-dep-option-generator

$ echo $RUST_HOME
/usr/local/lib/rust

$ export RUST_LIB=${RUST_HOME}/lib
$ rustc-dep-option-generator ${RUST_LIB}/Cargo.toml ${RUST_LIB}/target/release/deps
...

## ↑ Errorの文字が表示されなければOK

$ echo $?
0
```
