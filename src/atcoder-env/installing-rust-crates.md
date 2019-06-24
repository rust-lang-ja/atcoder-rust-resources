<!-- -*- coding:utf-8-unix -*- -->

# クレート（外部ライブラリ）のビルドとインストール

**TODO**

- 対象となるクレートのソースコードを`/usr/local/lib/rust/lib`にダウンロードする
- クレートをビルドする。
  ビルド済みのクレート（rlibファイル）は`/usr/local/lib/rust/lib/target/release`に出力される

```console
$ sudo -i
# whoami
root

# export RUST_HOME=/usr/local/lib/rust
# git clone https://github.com/rust-lang-ja/atcoder-rust-crates.git ${RUST_HOME}/lib
```

```console
# whoami
root

# cd ${RUST_HOME}/lib
# ./build.sh
```

ビルドに成功すると以下のように表示されます。

```console
TODO

```
