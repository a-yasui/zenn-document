---
title: "[Laravel] NameSpaceはあらかじめ変更しておく"
---

Laravel のバージョンアップで Namespace が少し変わることがある。例えば Eloquent の保存場所が `App\\` だったのに対して、laravel 5.6 あたりからは `App\\Model\\` となった。

問題として、モデルとして使う場合においている事がある。この場合のモデルは、データとメソッドを合わせたい時に使うもので、データソースが RDB の Eloquent だけに限らない。例えば JSON レスポンスや JSON/CSV に書き出し出すためのクラスなどを置く場所等がある。この、すでに使っている Namespace に対して機能追加がされた時に対応はできるがゴチャゴチャになってしまう事である。

## Example

あらかじめアプリケーション名を Namespace のトップ名にしておき、Laravel部分はすべて `App` 以下に置く。

例えば Hoge アプリケーションとした時 `composer.json` の `psr-4` で namespace を追加する方法がある。

```json
{
	"autoload": {
		"psr-4": {
			"App\\": "app/",
			"Hoge\\": "Hoge/"
		}
	}
}
```
