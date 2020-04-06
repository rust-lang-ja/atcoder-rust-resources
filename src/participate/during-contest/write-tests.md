<!-- -*- coding:utf-8-unix -*- -->

# テストケースの作成

Rust には組み込みでテストをするための機構が備わっています。これらの様式に則ってテストを書くと、 `cargo test` コマンドを実行するだけでテストを実行することができます。これを競プロでもサンプルケースが通るかどうかを確かめるのに利用することができます。この機能の使い方の例として、生成したテンプレートの `tests/sample_inputs.rs` には [AtCoder Beginner Contest 086 C - Traveling](https://atcoder.jp/contests/abc086/tasks/arc089_a) のサンプルケースが書かれています。

> Note: 直接手と目で確認する場合や、別のコンテスト支援ツールを利用する場合などはこの手順は必要ありません。また `cargo test` を利用しない場合、サンプルのテストファイルは削除しても良いですし、しなくても特に問題はありません。

なんとなく見れば分かるように、 `output_with_stdin()` の引数にサンプル入力を渡し `assert_eq!(output.stdout_str(), <expected output>);` で期待する出力と一致するかどうかを確かめます。一致しない場合、テストは失敗します。

```rust,ignore
use cli_test_dir::*;

const BIN: &'static str = "./main";

#[test]
fn sample1() {
    let testdir = TestDir::new(BIN, "");
    let output = testdir
        .cmd()
        .output_with_stdin(r#"2
3 1 2
6 1 1
"#)
        .tee_output()
        .expect_success();
    assert_eq!(output.stdout_str(), "Yes\n");
    assert!(output.stderr_str().is_empty());
}

#[test]
fn sample2() {
    let testdir = TestDir::new(BIN, "");
    let output = testdir
        .cmd()
        .output_with_stdin(r#"1
2 100 100
"#)
        .tee_output()
        .expect_success();
    assert_eq!(output.stdout_str(), "No\n");
    assert!(output.stderr_str().is_empty());
}

#[test]
fn sample3() {
    let testdir = TestDir::new(BIN, "");
    let output = testdir
        .cmd()
        .output_with_stdin(r#"2
5 1 1
100 1 1
"#)
        .tee_output()
        .expect_success();
    assert_eq!(output.stdout_str(), "No\n");
    assert!(output.stderr_str().is_empty());
}
```

標準入力を書く場合、通常のダブルクオートでも改行を含めることはできますが、特別な文字 (`\` や `"` など) を含む場合に備えて、サンプルのように `r#"........"#` のような形式を使うとよいかもしれません。

> Note: `r"..."` は基本的には普通の文字列ですが、文字列内の `\` をエスケープしません。また `"` そのものを含めたいときに備えて `r####"...."####` のように `#` を任意個挟むことができます。

なお、この方法では出力を単純に文字列同士の比較によって判定しています。浮動小数点数のように出力に誤差を認める形式や「グラフを一つ出力せよ」のように解が複数あり得る問題のテストにはそのままでは利用できません。それでもテストには任意のコードがかけるわけですから (実際にそこまでするかどうかは別としても) 浮動小数点数やグラフをパースしてチェックするというような使い方も可能です。
