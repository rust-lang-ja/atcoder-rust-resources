<!-- -*- coding:utf-8-unix -*- -->

# Rustツールチェインのインストール

**TODO** このページは書きかけです。

- Rustツールチェインを`/usr/local/lib/rust`配下にインストール
- インストール時はインターネット接続が必要


## 依存ソフトウェアのインストール

Rustツールチェインのインストールに先立って、Rustプログラムのリンクやクレートのビルドに必要なソフトウェアをインストールします。

- Rustツールチェインやクレートのダウンロードに必要なもの：curl, git-core
- Rustプログラムのリンク時に必要なもの：gcc, binutils
- 一部のRustクレートや関連ツールのビルドに必要なもの：libssl-dev, pkg-config

```console
$ sudo apt update
$ sudo apt install -y curl git-core gcc binutils libssl-dev pkg-config
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
    sh -s -- --default-toolchain "${RUST_TOOLCHAIN}" --no-modify-path
```


## コマンド検索パスの設定

Rust使用させたいLinuxユーザのシェルの初期設定ファイル（例`.bashrc`）に以下のコマンドを追加します。

```bash
source /usr/local/lib/rust/cargo/env
```

これにより`/usr/local/lib/rust/cargo/bin`がコマンド検索パス（`PATH`）に追加されます。


## インストール結果の確認

```console
## 一般ユーザで実行
$ source /usr/local/lib/rust/cargo/env

$ rustc -V

$ cargo -V

$ rustup -V

```
