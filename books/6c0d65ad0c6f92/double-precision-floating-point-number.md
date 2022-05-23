---
title: "[PG] 任意精度数学関数 と 倍精度浮動小数点"
---

小数点以下のビット演算がややこしくて把握しとらん馬鹿者です、こんにちは。

[IEEE 754 -- wikipedia](https://ja.wikipedia.org/wiki/IEEE_754) での小数点の扱いに関する事です。知っているけど把握しとらんのよなぁ…

# 小数点の扱い

標準関数の `round` などで小数点以下の計算しているとわかるのですが、小数点がある計算をコンピューターでするのは注意が必要です。

例えば `(0.1 + 0.7) * 10` の小数点以下切り捨てをすると `8` を期待します。しかし PHP では  `floor((0.1+0.7)*10) === 7.0` が真になります。Javascript でも `Math.floor((0.1+0.7)*10) === 7.0` が真になります。

類似として `0.1 + 0.2 !== 0.3` があります。


## PHP での対策


対策として PHP では以下の方法が取られます。

1. BCMath を使う。
	
	任意精度数学関数（bcmath）は、内部は数字ですが、PHP側では文字列でやり取りするので期待する数値になります。ならない時は、有効所数点の位置を間違えている等があります。

	使う時は文字列に変換をする必要がありますが、ネイピア数の表記には未対応だったり、Locale によっては小数点がコンマになって上手く処理できない等があるので注意が必要です。

	see : https://www.php.net/manual/ja/intro.bc.php


## JS での対策

わからない…

# 参考

see 

- [コンピューターの基礎 -- www.iwata-system-support.com](http://www.iwata-system-support.com/CAE_Computer/computer_basic.htm)
- [いまさら聞けないIT用語集　浮動小数点演算の単精度と倍精度って？ -- ascii.jp](https://ascii.jp/elem/000/001/713/1713959/)
- [IEEE 754 -- wikipedia](https://ja.wikipedia.org/wiki/IEEE_754)
- [倍精度浮動小数点数 -- wikipeida](https://ja.wikipedia.org/wiki/%E5%80%8D%E7%B2%BE%E5%BA%A6%E6%B5%AE%E5%8B%95%E5%B0%8F%E6%95%B0%E7%82%B9%E6%95%B0)
- [Math -- www.php.net](https://www.php.net/manual/ja/intro.math.php)
- [BC Math -- www.php.net](https://www.php.net/manual/ja/intro.bc.php)
- [JavaScriptの数値型完全理解 -- qiita.com](https://qiita.com/uhyo/items/f9abb94bcc0374d7ed23)

