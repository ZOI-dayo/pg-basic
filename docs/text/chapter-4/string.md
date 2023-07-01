# 4.4. string ②

string は、実際には文字の配列であると捉えると、文字列に対する操作を行うことができる。

一度、アルファベットと数字・一部の記号だけ、いわゆる「**1バイト文字**」のみで考えよう。ひらがなやカタカナについては後で説明する。

:::warning
ひらがな・カタカナ・漢字などの文字を使うと、以下の説明が成り立たなくなってしまうことがある。
:::

配列と同じようにして、長さを取得したり、1文字ずつ取得したりすることができる。

```cpp:line-numbers
string s = "traP";
cout << s.size() << endl; // 配列と捉えて配列の要素数を取得する。
cout << s.length() << endl; // length() によって文字列の長さを取得する

for (int i=0; i<s.size(); i++) {
  cout << s[i];
}
```

## 4.4.1. バイト・ビット

**ビット**(bit) は、1ビットは `0` か `1` かの情報のみを持つ。

**バイト**(Byte) は、パソコンが一般的に処理するデータの単位。基本的に 8bit = 1Byte。  
よくスマホの通信量とかの「キロバイト」等のバイトはこれを指している。

8bit なので、1バイトで扱える情報は `0` から `255` の256（$=2^8$）種類である。

## 4.4.2. char 型

文字1文字を扱う時は、 `char` 型を用いる。

```cpp:line-numbers
string s = "Hello";
char c = s[1];
cout << c << endl; // output: e
```

1文字を扱う時は、`''` （**シングル**クォーテーション）で囲う。

```cpp:line-numbers
string s = "traP";
s[3] = 'Q';

cout << s << endl; // output: traQ
```

## 4.4.3. ASCII

各文字には、文字コード（ASCIIコード）と呼ばれる番号が定められていて、内部的に char 型には数値が格納されている。

```cpp:line-numbers
char c = 'e';
int a = c;
cout << a << endl; // output: 101
```

ASCII コード表：https://e-words.jp/p/r-ascii.html
ここで、アルファベットは順番に並んでいるという事実は覚えておくと良い。

逆に、char に数値を代入によって文字を代入することもできる。

```cpp:line-numbers
char c = 81;
cout << c << endl; // output: Q
```

## 4.4.4. 文字の比較

4.4.3. によれば、1文字1文字は、実際は数値として扱われているのであった。よって、数値と同じように扱えるということは、以下のように比較できるということである。

```cpp:line-numbers
cout << ('A' < 'B') << endl;
```

ここで、アルファベットはASCIIコード上で順番に並んでいるということを思い返すと、
`('A' <= x && x <= 'Z')` で「文字$x$が大文字アルファベットかどうか」が判定できる。
更にこれと for 文をうまく使えば、（アルファベットの）辞書順でどちらが先かを実装することができるだろう。

::: tip

実際には、文字列の辞書順で先かどうかは、`<` `>` の不等号演算子で簡単に実装できる。

```cpp:line-numbers
string s = "Hello";
string t = "Bello";
cout << (s < t) << endl;
```

```
[output]
0
```

string 型同士で比較するときに、1文字目を比較→同じなら2文字目を比較→… という処理を行って比較されている。

:::

## 4.4.5. マルチバイト文字

::: tip
完全なおまけセクションです。講習の進行状況次第で飛ばします…
:::

ところで、ASCIIコード表には日本語特有の文字（ひらがな・カタカナ・漢字）が存在しないことに気づくだろうか。

これは1バイトで扱える情報が 256 通りしかないから、日本語は1バイトだけでは扱うことができない。
それゆえ、日本語（ひらがなカタカナ等）はマルチバイト文字で表現されている（複数バイトで1文字を表す方式）。

ここで、`char` 型は、正確には「1バイトの整数型」である。よって、`char`
型ではひらがな等の字を扱うことができない。実際に試してみると、5文字なのに長さは15と表示されてしまうし、1文字目を出力してみようと思ってもうまく出力されない。例えば「こ」の場合は、3文字出力してようやく「こ」が表示されるのである。

```cpp:line-numbers
string dame = "こんにちは";
cout << dame.length() << endl;
cout << dame[0] << endl;
cout << dame[0] << dame[1] << dame[2] << endl;
```

```
[output]
15
�
こ
```

多バイト文字を扱える型も存在するが、非常に扱いが面倒なのでここでは扱わない。
C++ 以外の言語でも大体は文字列を扱えるが、マルチバイト文字に対する扱いは言語によってまちまちだったりするので、よく調べること。

::: tip

### 4.4.5.1. 文字化け

ここで横道に逸れてしまうが、せっかくの機会なので「文字化け」についても触れておこう。

**歴史的な経緯**

ASCII では日本語文字を扱えないのは明らかだったので、1文字で2バイトを使う文字コード体系が考案された。2バイトであれば
$2^{16}=65536$ 文字を扱えるので、これだけあれば十分だという事である。
しかし、結果としては国内で文字コードが乱立し（Shift-JIS, EUC-JP
等…）、様々衝突するようになった上、国内にとどまらず各国で独自の文字コードが作成されてしまい、混乱を極めてしまったのである（ASCII
で表せないのは日本語だけではなく、アラビア文字・簡体字・ギリシャ文字など様々…）。

最終的には、世界の全文字を扱う文字コードとして Unicode (≒ UTF-8)
が策定され、今日ではこれが普及している。しかし完全に普及している訳でもなく、よって、今日見かける文字化けはもっぱら「UTF-8 か
UTF-8 以外か」の文字コードを使って発生しているパターンである。

**CJK 問題**

また、日本語の漢字が中国語のフォントで表示されている、という謎の現象に遭遇したこともあるかもしれない。これは、Unicode
を制定した際に日本語・中国語（簡体字・繁体字）・韓国語の漢字について、似ている漢字を全て一緒くたにしてしまった事が原因である。 → [Your Code Displays Japanese Wrong](https://heistak.github.io/your-code-displays-japanese-wrong/)

**横道**

（横道の横道はもう獣道では…？）
日本国内は2バイト文字コードの覇権を争うために揉めていたが、その間ヨーロッパでは自国専用の1バイト文字が乱立していて同じように揉めていたらしい。
:::

::: tip
`int` 型は 4バイトの整数型である。扱える情報は $2^{32}$ で、整数は負と正の値を取るから非負整数の部分にに $2^{31}$
個を割り当てる。$2^{31}=2147483648
$ なのだが、実はこの値は既にテキストで一度出現している。さてどこだろうか？
:::
