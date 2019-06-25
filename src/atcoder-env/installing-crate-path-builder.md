<!-- -*- coding:utf-8-unix -*- -->

# クレート検索パス生成プログラムのビルドとインストール

**TODO**


```console
$ sudo -i

# whoami
root

# cargo install --git https://github.com/rust-lang-ja/atcorder-rustc-dep-option-generator.git
```

```console
## 一般ユーザで実行
$ source /usr/local/lib/rust/cargo/env

$ which gen-deps-compile-options
/usr/local/lib/rust/cargo/bin/gen-deps-compile-options

$ export RUST_LIB=/usr/local/lib/rust/lib
$ gen-deps-compile-options ${RUST_LIB}/Cargo.toml ${RUST_LIB}/target/release/deps
...

## ↑ Errorの文字が表示されなければOK

$ echo $?
0
```
