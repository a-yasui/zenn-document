---
title: "JS math.js を使ったメモ"
emoji: "➗"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [ "javascript" ]
published: true
---

浮動小数点の都合でどうしても .0000000002 とかでてしまうアレの対応をするために、PHP だと bcmath 等があるのですが、JSではどうするのかなぁと調べたら math.js とドンピシャな物があります。

かなり高級なライブラリで `math.evaluate("0.1 + 0.2")` や `math.evaluate('12.7 cm to inch')` も計算してくれるみたいです。

- [math.js](https://mathjs.org/)

高機能がちょっと多くて、自分が使う範疇のみメモ書き。そんなに込み入った使い方はしない。

## 0.1 + 0.2 問題

よくある物として `0.1 + 0.2` をすると _0.30000000000000004_ になって丸め誤差がでてきます。うっかりこの状態で切り上げなどをすると変な 1 が出てきたりして、その1で問題を起こすってことが稀にあります。Math.ceil とかで小数点第二位以下を切り捨てとかしたいですが、JS の標準ではできません（たぶん） [Math.ceil() -- developer.mozilla.org](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Math/ceil)

mathjs を使えば `math.ceil(math.add(0.1, 0.2), 2)` とかすれば `0.3` と返してくれてます。そして数値比較にも使えます。

例えば Javascript で `(0.1 + 0.2) === 0.3` は偽になりますが、`math.equal(0.1+0.2, 0.3)` は真を返します。

# 使い方的なメモ

ライブラリを素のまま使う方法？と、数式を解釈する方法と、chain で計算式をメソッドチェーンでつなげていく方法があります。

## Extension

カスタム定数などを入れてゴニョゴニョできるみたいです。

```javascript
math.import({
  taxrate: 0.1,
})

math.evaluate( "1200 * ( 1.0 + taxrate)" ); // 1320
```

# 使い方

基本的なメソッドは https://mathjs.org/docs/reference/functions.html#arithmetic-functions に書いています。JS にある `Math` ライブラリを拡張したような感じで使えます。

## 使い方: 足し算

- `math.add(a, b)`
- `math.chain(a).add(b)`

## 使い方: 引き算

- `math.subtract(1, 2)`
- `math.chain(a).subtract(b).done()`

## 使い方: 割り算

- `math.divide(1,2)`
- `math.chain(a).divice(b)`

## 使い方: 掛け算

- `math.multiply(2,3); // 6`
- `math.chain(2).multiply(3); `

## 使い方: 余り

`4%6 === 2` の計算

- `math.mod(6, 4); // 2`
- `math.chain(6).mod(4); // 2`

## 使い方: 余剰

- `math.pow(3,3); // 27`
- `math.chain(3).pow(3)`

## 使い方: 数式

数式をそのまま流し込んで使うことができます。

```javascript
const subset = { x: 1024 };

math.evaluate("x ^ 2 / x + 12", subset) // 1036
```

