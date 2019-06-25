<!-- -*- coding:utf-8-unix -*- -->

# クレート（外部ライブラリ）のビルドとインストール

**TODO** このページは書きかけです。

- 対象となるクレートのソースコードを`/usr/local/lib/rust/lib`にダウンロードする
- クレートをビルドする。
  ビルドの成果物（rlibファイル）は`/usr/local/lib/rust/lib/target/release`に出力される
- インストール時にインターネット接続が必要

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
# ./build.sh
+ git submodule update --init
+ cargo clean
+ cargo update
+ cargo build --release --frozen
Compiling autocfg v0.1.4
...（中略）...

Finished release [optimized] target(s) in 1m 39s
$
```

```console
$ find target/release/deps/ -name '*.rlib' | wc -l
58

$ ./target/release/main
...
```

