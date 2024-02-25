# 私とPythonとインターフェース

はじめまして、OOC2024 スピーカー（「ソフトウェアを作りたかった私へ」）のnikkieです。
皆さま、OOCいかがですか？ 引き続き楽しみましょうね！

この記事はトークとは別トピック。
**Pythonでオブジェクト指向を会得していくうえで私が大きく躓いた点**を共有していきます。

躓いたのは、*インターフェース*という概念です。
Pythonを使っている読者の皆さま、どうか俺みたいになるな！

## Pythonとインターフェース

Pythonの言語仕様にはインターフェースがありません。
Javaなどの言語における`interface`に相当するキーワードがないのです。
Pythonにあるのは**クラス（`class`）だけ**です。

クラスしかないPythonですが、**クラスをインターフェースのように使う道**はあります。
その目的で使われるのは、標準ライブラリabc。
この名前はabstract base classから来ていて、日本語では**抽象基底クラス**です。

## 抽象基底クラスをインターフェースとして使う

例で示しましょう。

```python
from abc import ABCMeta, abstractmethod

class ShapeInterface(metaclass=ABCMeta):
    @abstractmethod
    def area(self):
        """面積を返す"""
```

長方形の抽象基底クラス`ShapeInterface`を定義しました。
これをインターフェースとして使っていきます。

`@abstractmethod`（デコレータ）は**抽象メソッド**を定義します。
このインターフェースを*実装*したクラスには、抽象メソッドと同名のメソッドを用意（オーバーライド）する必要があります。

Pythonの言語仕様にはインターフェースを実装するという概念がないので、**抽象基底クラスを継承**します。

```python
class Rectangle(ShapeInterface):
    def __init__(self, width, height):
        self._width = width
        self._height = height

    def area(self):
        return self._width * self._height
```

具象クラス`Rectangle`を示しました。
`ShapeInterface`を継承しています。
**具象クラスで`area()`を実装して初めて**、その具象クラスが**インスタンス化**できます。
この点は他の言語のインターフェースのように見えますね。

## インターフェースとして使う中で、あれ？

こうして抽象基底クラスをインターフェースとして使っていくわけですが、振り返ると私はどうもインターフェースとして使えていなかったように思われます。

例えば、（インターフェースとして使いたい）抽象基底クラスに**インスタンス変数を持たせてしまった**ことがありました。

```diff
class ShapeInterface(metaclass=ABCMeta):
+     def __init__(self, width, height):
+         self._width = width
+         self._height = height
```

`ShapeInterface`はインターフェースですから、インスタンス変数をもたせるのは変です。
他の言語のインターフェースには持たせられないでしょう。

ところが、Pythonでは（抽象基底）クラスをインターフェースとして使っているので、こういった誤った使い方が起こりえます。
クラスでできることがすべてインターフェースでも適切とは限りません。
クラスでできることを把握したうえで**取捨選択**して、**インターフェースとしてあるべき使い方をする**必要があります。

別の例を挙げると、（インターフェースとして使いたい）抽象基底クラスに**共通処理をメソッドとして実装してしまった**こともありました。
これも他の言語では実装できないでしょう。

メソッドを実装した後、さらに、デザインパターンのテンプレートメソッドのような状態にまで*進化*させてしまったこともあります。
これ以上のしくじりの共有は、もう勘弁してください（恥ずかしすぎて死にそうです）。

言いたいこととしては、インターフェースとして使うつもりであるにもかかわらず、Pythonの**プログラム上ではクラスなので、実装者の誤りで使い方を簡単に間違えて**しまえます。

## 抽象を押さえよう

Pythonで抽象基底クラスをインターフェースとして使う中で私が犯してきた過ちを共有してきました。
インターフェースとして使いたいのに、インスタンス変数やメソッドを持たせてしまっているというのは、いま振り返ると **「なんのためにインターフェースを使うのか」という点が押さえられていなかったからではないか**と思われます。
インターフェースをPythonでどう実装するかという点に関心を置きすぎていたのでしょう。

なんのためにインターフェースを使うのか、いまの私の理解では、具体（詳細）を知りすぎず、抽象的な**使い方**を表すためです。
またインターフェースという抽象によって、*具象をあることにできます*（参考：『ちょうぜつソフトウェア設計入門』）。
インターフェースはSOLID原則のD、依存性逆転の原則の鍵ですよね。

使い方を表すものという視点で見直すと、インターフェースとして使う抽象基底クラスにインスタンス変数や実装されたメソッドという詳細があるのはおかしいですよね。
抽象的なはずなのに、具体的な要素に染まりつつあるのです。
Pythonの文法的にはまったくもって正しいのですが、**インターフェースのあり方としては間違えていた**わけです。

まとめに代えて、Pythonでオブジェクト指向に関心がある方にメッセージです：
インターフェースが言語仕様にないPythonではインターフェースの概念を掴むのに苦労するかもしれません。
そんなときはPythonの外に出て、**インターフェースを使わざるを得ない言語でいくらか素振り**して、インターフェースという概念を掴むのがオススメです。
インターフェースは使い方を表すだけのもの、それを実装（Pythonでは継承）したクラスには**どんなメソッドがあるか**（＝使い方）だけを示せば十分なのです。

## 参考文献

* 『エキスパートPythonプログラミング 改訂3版』（17.3.1）
* 『ちょうぜつソフトウェア設計入門』（3章、5章）