---
title: "Laravel Pint のススメ"
emoji: "🔖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["laravel", "PHP", "PHP CS Fixer"]
published: true
---

[Laravel のバージョン 9.3 -- github](https://github.com/laravel/laravel/releases/tag/v9.3.0) から [**laravel/pint**](https://github.com/laravel/pint) が標準インストールされるようになりました。 **laravel/pint** は PHP-CS-Fixer を Laravel 用にカスタマイズした物です。これを導入すれば、わざわざ `.php-cs-fixer.dist.php` を書かなくても、Laravel の書き方になるように fixer が動きます。やったね。

See : [https://laravel.com/docs/9.x/pint](https://laravel.com/docs/9.x/pint)

ただ内部で動くのは PHP-CS-Fixer なので、PHP のソースコードをごっそり書き換えます。テストがない怖いプロジェクトではちょっと怖いかも。

# 導入

PHP が 8.0.2 以上の環境であれば、Laravel のバージョンを問わずに導入できます。

```shell
> composer require laravel/pint --dev
```

# 使い方

* `./vendor/bin/pint` を動かしたら PHP コードの fixer を動かして書き換えます。
* `./vendor/bin/pint -v` および `./vendor/bin/pint -vv` はログを冗長出力
* `./vendor/bin/pint --test` は Fixer で変更するファイルを表示します。

## coding-style を変更

標準では `{"preset": "laravel"}` という状態です。これを変更したい時は `./vendor/bin/pint --config pint.json` という形で変更して実行します。

**preset** のみ変更したい時は `./vendor/bin/pint --preset psr12` という形になります。

tikaratukita

