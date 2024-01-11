---
title: "Laravel で old factory を別ディレクトリにする"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['laravel']
published: true
---

Laravel 6 以前の昔からあるアプリケーションを 8 以上にした時、使っていた factory が laravel/legacy-factories になるのです。それは良いとして、新 Factory （クラス化されている方）と同じディレクトリにあると、シッチャカメッチャカになるので分けたいなぁってのがあります。その分ける方法のメモ書き。どうやったか自分でも覚えてなかった。

【メモ】古いFactory が既にある状態で新しいクラス化されたFactoryと共存は可能：[Laravelのモデルファクトリ 新旧が共存することが可能か検証してみた -- zenn.dev](https://zenn.dev/naopusyu/articles/e3e8d010c4e245)


# 方法

`composer.json` の `autoload.classmap` と `autoload.psr-4` で切り分ける形。

1. composer.json の書き換え。主に `"Database\\Factories\\": "database/factories/"` と `"database/legacy-factories"` を加える。

```json
{
	"autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Seeders\\": "database/seeders/",
            "Database\\Factories\\": "database/factories/"
        },
        "classmap": [
            "database/seeds",
            "database/legacy-factories"
        ]
    },
}
```

2. ディレクトリを変更後、空のディレクトリを作成。

	`database/factories` を使っており古いFactoryが溜まってるはずです。それを `database/legacy-factories` に変えてやります。新しいクラス化されてる Factory が `database/factories` に追加されてく形なはずです。空のディレクトリを git add はできないので、 `.keep` とか置いておくといいと思います。

3. AppServiceProvider に次の数行を追加。これ無いとコケる。忘れてた。

```php
use Faker\Generator;
use Illuminate\Database\Eloquent\Factory;

class AppServiceProvider extends ServiceProvider
{
	public function boot()
	{
        // 古い方の factories は legacy-factories に移動したので、追従変更させる
        $this->app->singleton(Factory::class, function ($app) {
            return Factory::construct(
                $app->make(Generator::class), $this->app->databasePath('legacy-factories')
            );
        });
    }
}
```

3. 書き換えた後 `composer install --dev` とかをすればいいと思います。
