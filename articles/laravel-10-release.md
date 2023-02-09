---
title: "Laravel 10 Release/Upgrade memo"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["php","laravel"]
published: true
---

ほぼ一年ぶりのメジャーアップデートである Laravel10 がリリースされます。2023年の Q1 とのことで現時点（2023/02/09）はまだリリースされてません。[多分来週 -- twitter.com](https://twitter.com/taylorotwell/status/1622997899654176769)とテイラーさんは言っているので、2023/02/10日以降とか…？。

そんなわけで、このメモ書きも気がついたら増えていきます。一応公式ドキュメントは公開されたので、これも公開。

なお [名前的に Laravel X とは呼ばないでね --twitter.com/taylortwell](https://twitter.com/taylorotwell/status/1620807927715217409) って言ってます

そもそも Laravel 9 がリリースされてからマイナーアップデートなの…？ってレベルでアップデートが大量にありますので、Laravel10への対応は楽です。

アップデートとはあまり関係ない気がするけど、Laravel 10 に対した [Bug Hunt Contest -- laravel-news.com](https://laravel-news.com/laravel-10-bug-hunt) があるみたいです。

# see:

- [Upgrade Guid -- laravel.com](https://laravel.com/docs/10.x/upgrade)
- [A Look at What's Coming to Laravel 10 -- laravel-news.com](https://laravel-news.com/laravel-10)
- [Laravel 10 Application Skeleton Code Will Have Native Type Declarations -- laravel-news.com](https://laravel-news.com/laravel-10-type-declarations)

# ざっくり

- [High] composer.json のアップデート
- [Higt] PHP8.0 以下は未対応
- [Medium] `\DB::raw` の仕様変更
- [Medium] Eloquent Model の `$dates` の廃止
- [Medium] Redis Cache tag の対応
- [Medium] ServiceMock の仕様変更（ `expectsEvents`, `expectsJobs`, `expectsNotifications` の廃止の話）
- [Medium] 多言語ディレクトリの変更(新規インストール時の話)
- [Low] Validation Message で Closure 指定したときの変更
- [Low] MonoLog3 対応
- [Low] QueryException の Constructor 引数が変更
- [Low] LateLimit の返り値変更
- [Low] `Relation` クラスの `getBaseQuery` メソッド名変更
- [Low] `Route::home` の廃止
- [Low] `Bus::dispatch` のメソッド名変更
- [Low] ULID カラム名変更

# 大きな変更点

## 1. PHP 8.0

PHP は 8.0 以下は未サポートとなりました。最低でも 8.1 を使ってください。

## 2. Native Type

PHP の Native Type が Laravel Framework レベルで付けるようになりました。

- Return Types -- 返り値の型指定
- Method Arguments -- メソッドの引数の型指定
- Redundant annotations are removed where possible （←これわからん。Eloquentがマシになったってこと…？
- Allow user land types in closure arguments -- クロージャーの引数の型指定
- Does not include typed properties -- 型指定されたプロパティは読み込まない…？readonly 属性のこと？

よくわからん部分があるけど、要は ide_helper 無くても楽になるならありがたいという認識。おそらく ServiceProvider 等の返り値指定が必要になってくるから、そこら辺の変更は必要じゃないかしら。


# 新機能

Unix コマンド等のプロセス実行をするための機能が追加されました。

```
<?php

use Illuminate\Support\Facades\Process;
 
$result = Process::run('ls -la');
 
return $result->output();
```

## Test Performance

テストのパフォーマンスが表示できるようになりました。 `php artisan test --profile` で実行したときに遅いテスト順で表示してくれます。

see : https://laravel-news.com/laravel-10-profile-option

## 実装変更に伴う新機能

`DB::raw` が強化されました。説明が難しいので [該当の pull-request -- github.com](https://github.com/laravel/framework/pull/44784) と [ツイート -- twitter.com](https://twitter.com/tobias_petry/status/1622577764682407936)を見てください。

こんな感じみたいです。

```
// ~9.x
DB::table('table')
    ->when(isPostgreSQL(), fn ($query) => $query->select(DB::raw('coalesce("user", "admin") AS "value"')))
    ->when(isMySQL(), fn ($query) => $query->select(DB::raw('coalesce(`user`, `admin`) AS `value`')))

// 10.x~
DB::table('table')->select(new Alias(new Colalesce(['user', 'admin']), 'count'));
```

# アップグレード

アップグレードをするのに、既存アプリケーションに影響が確実にある事項（Likelihood Of Impact: High）は一つのみ。それ以外は Medium/Low です。

## composer.json のアップデート :: Impact:High

**composer.json** の下記の箇所を変更してください。

1. `"laravel/framework": "^10.0"`
2. `"spatie/laravel-ignition": "^2.0"`

安定リリースを保つため下記も変更します。

1. `"minimum-stability": "stable",`

なおオプションで PHPUnit 10 を使うときは下記のような変更をしてください。

1. **phpunit.xml** の **<coverage>** のパラメータにある *processUncoveredFiles* を削除してください。
2. **composer.json** の `"nunomaduro/collision": "^7.0"`
3. **composer.json** の `"phpunit/phpunit": "^10.0"`


### 他の composer.json の変更点

1. Monolog3 に対応しました。もし個別で使用している場合は追従してください。

## キャッシュ :: Impact:medium

Redisで [cache tag](https://laravel.com/docs/10.x/cache#cache-tags) が対応されました。定期的にパージしてやる必要があります。

```
$schedule->command('cache:prune-stale-tags')->hourly();
```

## Database Expression :: Impact:medium

`DB::raw()` が書き直されました。これに伴い `getValue()` メソッドに `Grammar` 引数が必要となりました。今までだと `__toString()` でキャストすれば値が取れていましたが、それも `getValue(Grammer $grammer)` に書き換えてやる必要があります。

```
use Illuminate\Support\Facades\DB;
 
$expression = DB::raw('select 1');
 
$string = $expression->getValue(DB::connection()->getQueryGrammar());
```

## QueryException constructor :: Impact:Low

`Illuminate\Database\QueryException` のコンストラクターの第一引数が `connection name` に変更されました。もしこのクラスを使用をしている場合は、追従して変更をしてください。


## ULID Column :: Impact:Low

`ulid` カラムを指定時に、カラム名の指定が必須になりました。

## Eloquent Date Property :: Impact:Medium

廃止予定だった Eloquent にある `$date` プロパティは削除されました。（（deprecated 付いてたっけ…？）

これに対応するには `$casts` を使用してください。

```
protected $casts = [
    'deployed_at' => 'datetime',
];
```


## rename the getBaseQuery :: Impact:Very Low

`Illuminate\Database\Eloquent\Relations\Relation` クラスにある `getBaseQuery` メソッドの名前が `toBase` に変更されました。

## Monolog3 :: Impact: Low

MonoLog3 に対応しました。MonoLogを使用している場合は [monolog のアップグレードガイド -- github.com](https://github.com/Seldaek/monolog/blob/main/UPGRADE.md) を確認してください。


## Bus::DispatchNow :: Impact:Medium

廃止予定だった `Bus::dispatchNow` と `dispatch_now` メソッドは削除されました。

代わりに `Bus::dispatchSync` ないしは `dispatch_sync` に変更をしてください。


## Middleware Alias :: Impact:Option

`App\Http\Kernel` にある `$routeMiddleware` プロパティが `$middlewareAliases` と名前が変わりました。既存のアプリケーションで名称変更は推奨されますが、これは必須ではないです。

## RateLimiter::attempt return value :: Impact:Low

`RateLimiter::attempt` を使ったときに第三引数のクロージャーが null や何も返さなかった時は、 `attempt` は `false` を返します。

```
$value = RateLimiter::attempt('key', 10, fn () => ['example'], 1);
 
$value; // ['example']

```

## Redirect::home :: Impact:Very Low

`Redirect::home` メソッドは廃止されました。代わりに `Redirect::route('home')` を使ってください。

## ServiceMock :: Impact:medium

`MocksApplicationServices` が廃止され削除されました。

これにより **expectsEvents**,**expectsJobs**,**expectsNotifications** が使えなくなりました。代わりに **Event::fake**,**Bus::fake**,**Notification::fake** を使用してください。

see : [Mocking -- laravel.com](https://laravel.com/docs/10.x/mocking)

## Closure Validation Rule Messages :: Impact:Very Low

Validation Rule にてクロージャーを渡したときに、第三引数の `$fail` は Object を返すクロージャーになりました。

（この機能自体、初めて知ったからなんだコレ感。）

## 多言語ディレクトリの変更

新規インストール時に **lang** ディレクトリは作成されますが、このバージョンから `php artisan lang:publish` を実行しないと作成されなくなりました。



