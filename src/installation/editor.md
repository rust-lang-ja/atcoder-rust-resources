<!-- -*- coding:utf-8-unix -*- -->

# ソースコードエディタの準備

Rust プログラムのソースコードは単純なテキストファイルです。編集にはテキストエディタというプログラムを使います。単純にテキストを読み書きするという機能に限ればテキストエディタは OS にも付属していますが、よりプログラミングに特化した機能を多く揃えるテキストエディタが無料で簡単に使える時代ですので、ぜひ活用するべきです。それらのエディタが持つ機能には例えば次のようなものがあります。 (一例です)

- コードハイライト  
    文法構造に合わせてソースコードを色付けして表示する機能です。たとえば型名や関数名などに色がつきます。この機能はコードを視覚的に把握するのに役立ちます。
- 入力補完  
    変数名や関数名の最初の何文字かを入力するだけでそれから始まる要素を列挙して表示してくれます。目当てのものがあればキー一つで残りの部分を補完してくれます。
- エラーハイライト  
    入力するのと同時にリアルタイムに内容をチェックし、エラーがあればそれをエディタ上に赤線を引くなどの形で表示してくれます。コンパイラを直接実行する手間もエラーメッセージを読み解く手間もなくエラーに気づくことができるメリットがあります。
- ドキュメント (説明書き) の表示  
    久しぶりに使う関数の引数の数や順番がわからなくなることはよくあることですが、こういうときにマウスカーソルを関数に合わせるだけでその関数のシグネチャやドキュメントを表示してくれます。
- 定義へのジャンプ  
    その関数や型が定義されている場所を開いてくれます。構造体の中身を少し忘れてしまったり、関数の処理の中身を確認したりといったときに簡単にソース上を移動できます。

こういった機能は Language Server Protocol という共通の仕組みの上に実装されているので、これをサポートするエディタであればこれらの機能を享受できます。 Language Server Client を実装しているエディタは無数にあり、どれを使うかは完全に好みです。

本説明はできるだけエディタによらないように進めていくつもりですが、エディタに特有の内容が現れるときは [Visual Studio Code](https://code.visualstudio.com/) を想定して説明します。それ以外のエディタを使われる方は適宜読み替えてお読みください。次節で Rust のコーディング支援機能を導入する方法を説明します。

(TODO: Visual Studio Code のインストールと Rust (rls) 拡張機能のインストールを説明する)
