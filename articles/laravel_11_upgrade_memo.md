---
title: "Laravel 11 Upgrade Memo"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['laravel']
published: true
---

# Laravel 11

リリースが毎年2月末あたりだったのが、今年は伸びてます。なんかあったんかね。現時点（2024/03/10）で来た Laravel-news のメールには火曜日リリース！って書いてたので、多分 2024/03/13 にリリースです。

結構、[bootstrap/app.php など根本的な部分がごっそり変わってる部分 -- github.com](https://github.com/laravel/laravel/compare/10.x...11.x#diff-10bc2462d34ebc59a5d482a1faca04ce74f6df89fcc2ef8bfb17b939647ea518)が多く、かつデータベース周りの変更がガッツリあるので、アップグレードする場合はしっかりテストは書いておきましょう。

- see : [https://laravel-news.com/laravel-11](https://laravel-news.com/laravel-11)
- document: [https://laravel.com/docs/11.x](https://laravel.com/docs/11.x)

------------

# [High] Composer の依存関係

1. PHP to >= 8.2
2. nunomaduro/collision to ^8.1

## Laravel Packages

もし cashier-stripe/passport/sanctum/spark-stripe/telescope を使っている場合は、それらをアップグレードして、 `vendor:publish` を実行してください。

1. laravel/cashier-stripe to ^15.0
1. laravel/passport to ^12.0
1. laravel/sanctum to ^4.0
1. laravel/spark-stripe to ^5.0
1. laravel/telescope to ^5.0

```shell
php artisan vendor:publish --tag=cashier-migrations
php artisan vendor:publish --tag=passport-migrations
php artisan vendor:publish --tag=sanctum-migrations
php artisan vendor:publish --tag=spark-migrations
php artisan vendor:publish --tag=telescope-migrations
```

最後に `doctrine/dbal` を使用していない場合は、パッケージから消しても問題なくなりました。

# [High][任意][New] アプリケーションの構造変更

ガラッと変わりました。AppServiceProviderなどはそのままですが、Laravel として初期から存在しているファイルが軒並み変更ですが、一応、公式にはそのままでも使えるとは書いています。書いてるけどよくわからん。

ざっと変更差分を見た感じだと、`config/app.php` にあった providers が `bootstrap/providres.php` に行ったりと、追加変更削除が多いです。

## [High][任意][変更] `public/index.php`

Laravel 10 以前からの、アプリケーションファイルの変更をしたくない場合は、この変更をしないほうが良いと思われます。

```php
<?php

use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Foundation\Configuration\Middleware;

return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware) {
        //
    })
    ->withExceptions(function (Exceptions $exceptions) {
        //
    })->create();

```

## [High][任意][作成] `bootstrap/app.php`

Routing まわりもごっそり変更です。

```php
<?php

use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Foundation\Configuration\Middleware;

return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware) {
        //
    })
    ->withExceptions(function (Exceptions $exceptions) {
        //
    })->create();
```

## [High][任意][作成] `bootstrap/providers.php`

プロバイダー周りの設定

```php
<?php

return [
    App\Providers\AppServiceProvider::class,

    // EventProvider もここに書いていく
];

```

# [High] DB の Floor Point 変更

Migration における話です。

カラムの型指定において、`double/float` を指定する場合に、第二引数 `total` (ビット数)および第三引数 `places` (小数点以下の有効桁数)指定が無くなりました。

今までの `$table->double('amount', total: 8, places: 2)` 相当のものは `$table->decimal('amount', total: 8, places: 2)` もしくは `$table->double('amount')` もしくは `$table->double('amount', precision: 53)` になりました。



# [High] DB Migration のカラム変更

カラム変更時に属性を指定する必要があります。以前までは変更の必要があるものだけを指定していましたが、このバージョンからは unsigned, default などを指定してください。

例えば下記のマイグレーションで user テーブルに vote カラムを追加したとします。

```php
\Schema::create('users', function (Blueprint $table) {
    $table->integer('votes')->unsigned()->default(1)->comment('The vote count');
});

```

今までのバージョンのように変更を単純変更をした場合だと、期待通りのカラムにはなりません。

```php
Schema::table('users', function (Blueprint $table) {
	// これだけだと、votes から unsigned/default:1 が外れます!
    $table->integer('votes')->nullable()->change();
});
```

laravel10 で期待通りに変更させるには、下記のように絶対指定する必要があります。

```php
Schema::table('users', function (Blueprint $table) {
    $table->integer('votes')
        ->unsigned()
        ->default(1)
        ->comment('The vote count')
        ->nullable()
        ->change();
});
```

ただし、Index は今まで通り外れません。

# [High] SQLite Support Minimum Version

初期状態で SQLite を使うようになりました。それに伴い SQLite の最低バージョンが 3.35.0 になりました。

# [High] Sanctom Upgrade

Laravel Sanctum 3.x のサポートをしなくなっていきますので、4.x に上げましょう。

# [Medium] Carbon3

Laravel 11 は Carbon2 と Carbon3 をサポートしています。

- see: [https://github.com/briannesbitt/Carbon/releases/tag/3.0.0](https://github.com/briannesbitt/Carbon/releases/tag/3.0.0)

# [Medium] Password ReHashing

Laravel 11は、パスワードが最後にハッシュ化されてからハッシュ化アルゴリズムの "work factor "が更新された場合、認証時にユーザーのパスワードを自動的に再ハッシュ化します。

ですので、再ハッシュ化をしたくない時は `config/hashing.php` の下記の変数を変更してください。

```php
'rehash_on_login' => false,
```

# [Medium] Per-Second Rate Limit

Trottle などで分指定した数値が、秒に変更されました。指定数値を `* 60` などをする必要があります。

# [Low] Doctrine DBAL Removal

Laravel が Doctrine に依存しなくなりました。これに伴い、引数等の型指定の変更があります。

# [Low] Eloquent Model `casts` Method

Eloquent Model に `casts` メソッドが増えました。メソッド名の衝突に注意してください。

# [Low] Spatial Types

DBへの接続周りが書き直されました。それにともない、`point`, `lineString`, `polygon`, `geometryCollection`, `multiPoint`, `multiLineString`, `multiPolygon`, `multiPolygonZ` といったメソッドが無くなり、代わりに `geometry` もしくは `geography` になりました。

# [Low] Spatie Once Package

once 関数を Laravel は実装しました。 `spatie/once` パッケージを使っている時は衝突を起こすので、パッケージを削除してください。

# [Low] The `Enumerable` Contract

`dump` メソッドの引数が Enumerable になりました。何かしら変更している時は追従で変更をしてください。

# [Low] The `UserProvider` Contract

`Illuminate\Contracts\Auth\UserProvider` に `rehashPasswordIfRequired` が新しく追加されました。もしそのインターフェースを使ったクラスがある場合は `rehashPasswordIfRequired` を `Illuminate\Auth\EloquentUserProvider` クラスを参考ないしは同名メソッドを呼び出す実装をしてください。


# [Low] The `Authenticatable` Contract

`Illuminate\Contracts\Auth\Authenticatable` に `getAuthPasswordName` メソッドが追加されました。もしそのインターフェースを使ったクラスがある場合は下記のような実装をしたら良いです。

```php
public function getAuthPasswordName()
{
    return 'password';
}

```

`User` モデルは `Illuminate\Auth\Authenticatable` トレイトを使用しているため、改めて実装する必要はありません。


# [Low][New] LogStack の環境変数化と配列化

今まで `logging.php` にあった `stack` は環境によって追加などをしていました。Laravel 11 からは `LOG_STACK` 環境変数に `,` 区切りで任意に変更できます。 `LOG_STACK="daily,rollbar"` といった感じ。

- [https://github.com/laravel/laravel/compare/10.x...11.x#diff-9ba917899def7f26cdd2c34da749863fbb2797a40aed5e41a1166a022b10d7df](https://github.com/laravel/laravel/compare/10.x...11.x#diff-9ba917899def7f26cdd2c34da749863fbb2797a40aed5e41a1166a022b10d7df)
