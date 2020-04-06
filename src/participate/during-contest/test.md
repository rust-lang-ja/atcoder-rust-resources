# プログラムのテスト

**TODO** このページは書きかけです。

一通り書き終わったと思ったら、提出前にまずは一度サンプルケースで答えが合うかどうかを確認してみましょう。コマンド プロンプトまたは端末を開いてプロジェクトフォルダに移動して、次のようなコマンドを実行します。

```console
$ cargo test
   Compiling abc000 v0.1.0 (C:\Users\dicen\workspace\daily\2020\0406\abc000)
    Finished dev [unoptimized + debuginfo] target(s) in 0.52s
     Running target\debug\deps\main-1ecbd097f851d76e.exe

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target\debug\deps\sample_inputs-05a0de7d755e5cc1.exe

running 3 tests
No
test sample3 ... ok
Yes
test sample1 ... ok
No
test sample2 ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

すると、このようにテスト結果が表示されます。このように最後に `test result: ok.` となっていれば、すべてのテストに成功しているということになります。一方で、もし成功しないテストがあれば、次のようになります。

```console
$ cargo test
   Compiling abc000 v0.1.0 (C:\Users\dicen\workspace\daily\2020\0406\abc000)
    Finished dev [unoptimized + debuginfo] target(s) in 0.68s
     Running target\debug\deps\main-1ecbd097f851d76e.exe

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target\debug\deps\sample_inputs-05a0de7d755e5cc1.exe

running 3 tests
No
test sample2 ... ok
Yes
No
test sample1 ... FAILED
test sample3 ... ok

failures:

---- sample1 stdout ----
thread 'sample1' panicked at 'assertion failed: `(left == right)`
  left: `"Yes\n"`,
 right: `"No\n"`', tests\sample_inputs.rs:16:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace.


failures:
    sample1

test result: FAILED. 2 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out

error: test failed, to rerun pass '--test sample_inputs'
```

最後の `test result: FAILED.` により失敗したことが示されています。その横には成功と失敗の個数が示されています。そして上には失敗したときのコマンドの出力も示されています。 `assertion failed` 以降から、 `No` と出力すべきところを `Yes` と出力してしまったことがわかります。

> Note: メッセージに示される `left:` と `right:` はマクロ `assert_eq!(left, right);` に渡される引数の左右です。用意したテストコードでは実際の出力を左、期待される出力を右として `assert_eq!(output.stdout_str(), "No\n");` としていたことから、本来は `No` と出力すべきところを `Yes` と表示した、ということがわかります。
