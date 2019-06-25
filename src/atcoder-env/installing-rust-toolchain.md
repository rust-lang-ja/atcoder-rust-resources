<!-- -*- coding:utf-8-unix -*- -->

# Rustツールチェインのインストール

**TODO** このページは書きかけです。

- Rustツールチェインを`/usr/local/lib/rust`配下にインストール
- インストール時にインターネット接続が必要


## 依存ソフトウェアのインストール

Rustツールチェインのインストールに先立って、Rustプログラムのリンクやクレートのビルドに必要なソフトウェアをインストールします。

- Rustツールチェインやクレートのダウンロードに必要なツール：curl, git-core
- Rustプログラムのリンク時に必要なツール：gcc, binutils
- 一部のRustクレートや関連ツールのビルドに必要なツール：make, pkg-config
- 一部のRustクレートや関連ツールのビルドに必要なライブラリ：libssl-dev

```console
$ sudo apt update
$ sudo apt install -y curl git-core gcc binutils make pkg-config libssl-dev
```


## Rustツールチェインのインストール

```console
$ sudo -i

# whoami
root

# export RUST_TOOLCHAIN=1.35.0
# export RUST_HOME=/usr/local/lib/rust
# export RUSTUP_HOME=${RUST_HOME}/rustup
# export CARGO_HOME=${RUST_HOME}/cargo

# mkdir -p ${RUST_HOME}
# curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | \
    sh -s -- -y --default-toolchain "${RUST_TOOLCHAIN}" --no-modify-path
```

```console
info: downloading installer
info: syncing channel updates for '1.35.0-x86_64-unknown-linux-gnu'
info: latest update on 2019-05-23, rust version 1.35.0 (3c235d560 2019-05-20)
info: downloading component 'rustc'
 88.4 MiB /  88.4 MiB (100 %)  10.9 MiB/s in 10s ETA:  0s
...（中略）...

   1.35.0 installed - rustc 1.35.0 (3c235d560 2019-05-20)


Rust is installed now. Great!

To get started you need Cargo's bin directory (/usr/local/lib/rust/cargo/bin)
in your PATH environment variable.

To configure your current shell run source /usr/local/lib/rust/cargo/env
```

## コマンド検索パスの設定

Rustを使用させたいLinuxユーザのシェルの初期設定ファイル（例`.bashrc`）に以下のコマンドを追加します。

```bash
source /usr/local/lib/rust/cargo/env
```

これにより`/usr/local/lib/rust/cargo/bin`がコマンド検索パス（`PATH`）に追加されます。


## インストール結果の確認

```console
## 一般ユーザで実行
$ source /usr/local/lib/rust/cargo/env

$ which rustc
/usr/local/lib/rust/cargo/bin/rustc

$ rustc -V
rustc 1.35.0 (3c235d560 2019-05-20)

$ cargo -V
cargo 1.35.0 (6f3e9c367 2019-04-04)

$ rustup -V
rustup 1.18.3 (435397f48 2019-05-22)
```
