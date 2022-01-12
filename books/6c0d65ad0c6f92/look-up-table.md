---
title: '[PG] Look up Table'
---

文字列比較のみの `if~elseif~elseif~`が続く処理や、switch文での処理は冗長になりやすいです。
複数行の elseif を使う時は、連想配列でテーブルを使用するほうが、後で条件を追加しやすくなり良くなります。


参考 : [Instead of writing repetitive `else if` statements -- Samuel Štancl -- twitter.com](https://twitter.com/samuelstancl/status/1272822439689555969)

## ✗ Wrong

```php
if(    $order->prodcut->option->type === 'pdf') $type = 'book';
elseif($order->prodcut->option->type === 'epub') $type = 'book';
elseif($order->prodcut->option->type === 'license') $type = 'license';
elseif($order->prodcut->option->type === 'artwork') $type = 'creative';
elseif($order->prodcut->option->type === 'song') $type = 'creative';
elseif($order->prodcut->option->type === 'physical') $type = 'physical';

if    ($type === 'book') $downloadable = true;
elseif($type === 'license') $downloadable = true;
elseif($type === 'creative') $downloadable = true;
elseif($type === 'physical') $downloadable = false;

```


## ○ best

```php
$type = [
	'pdf'  => 'book',
	'epub' => 'book',
	'license' => 'license',
	'artwork' => 'creative',
	'song' => 'creative',
	'physical' => 'physical'
][$order->prodcut->option->type];

$downloadable = [
	'book' => true,
	'license' => true,
	'creative' => true,
	'physical' => false
][$type];
```

