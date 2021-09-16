---
title: "IntelliJ の Live Templateを作成する"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['intellij', 'php']
published: false
---

[Mockery -- packagist.org](https://packagist.org/packages/mockery/mockery) というPHPでモックを書いているのですが、いかんせん書き方を何故か忘れてドキュメントを見る羽目になります。

ですので、ほぼほぼ定形文句な物なので、こんな感じのコードで打てるようライブテンプレートにします。

see : [Live templates -- jetbrains.com](https://www.jetbrains.com/help/idea/using-live-templates.html)

## 作り方

`Preferences` を開き `Editor` → `Live Templates` の画面を開きます。その中から `PHP` を選択し、一番右にある `+` を押して `Live Template` をクリックします。

新規テンプレートが作成されますので `Abbreviation` に `mockeryClass`, `Description` にはわかりやすいよう `Mockery generate Template` としておきます。

テンプレートテキストは下記のような感じにします。 `$~~$` がミソみたいで、任意の変数やカーソル移動させやすいようにするみたいです。 `$END$` は特殊で最終的に移動させるポイントに鳴るみたいです。

```php
$mock = \Mockery::mock( $CLASSNAME$::class, function ( \Mockery\MockInterface $m ) {
	$m->shouldReceive( '$METHOD$' )
		->andReturn([]);
}

```

`Edit variables` では書き込んだテンプレートにある変数、上記の中でいう `$CLASSNAME$` や `$METHOD$` などの初期値やカーソルスキップするかどうかを設定します。

`Reformat accoding to style` にチェックを入れます。これを入れると `mockeryClass` と打ち込んでテンプレートを展開したときに、タブ幅やコードシンタックスをテンプレートに当てフォーマットしてから書き込んでくれます。

最後に `Apply` ボタンを押して保存します。

