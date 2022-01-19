---
title: "[Laravel] DO NOT use env function in Code"
---

プロジェクト内で `env()` を使わず、`config` を使って変数の値は取得しましょう。

唯一 `env()` を使って良いのは configディレクトリ以下にある設定ファイルです。

参考: [Configuration Caching -- laravel.com/docs/5.8/](https://laravel.com/docs/5.8/configuration#configuration-caching)

# woozy

```php
$ajax_end_point = env('APP_URL');
$mail_from = env('MAIL_FROM_ADDRESS');
```

# GOOD

```php
$ajax_end_point = config('app.url');
$mail_from = config('mail.from.address');
```

## One More

この例の `ajax_end_point` は、ほしいのがURLなので、Routing を使う方法がなお良い。

```php
$ajax_end_point = url('/');
```


