---
title: "[Laravel] NameSpaceはあらかじめ変更しておく"
---

Laravel のバージョンアップで Namespace が少し変わることがあります。
例えば Eloquent の保存場所が `App\\` だったのに対して、laravel のバージョン 5.6 以降は `App\\Model\\` となっています。

これの問題として、自身が追加した `App\\Model` と競合してしまう事です。
起きてしまったことは仕方がないとして、起きないためには、初めから別の namespace で作業する事です。

## composer.json での設定

まず Laravel 部分はすべて `App` 直下に置きます。そして `app` ディレクトリと同じ階層に、任意の名前のディレクトリを作成して、composer.json の psr-4 に追加してやります。

例えば Hoge アプリケーションとした時 `composer.json` の `psr-4` で namespace を追加する方法がある。

```json:composer.json
{
	"autoload": {
		"psr-4": {
			"App\\": "app/",
			"Hoge\\": "Hoge/"
		}
	}
}
```
