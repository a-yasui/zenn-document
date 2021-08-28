---
title: "Routing に名前をつける"
---

ルーティングは基本的に `Route::get('price', 'HogeController@getPriceList')` といった感じで定義します。HTMLテンプレートやリダイレクトでは `action('HogeController@getPriceList')` とアクションで指定したり、`/price` とURLを直接指定をします。

問題は `HogeController` にある `getPriceList` の名前を `getPrice` とした時や `/price_list` と URL を変更したときに、全検索しなければならなくなり、検索漏れ等があるとリンク切れを起こします。最悪、リダイレクトがうまく行かず不具合を引き起こします。


## 解消法

名前をつけてあげ、その名前を使いまわします。

```php
Route::get('price', [\App\Http\Controller\HogeController::class, 'getPriceList'])->name('price_page');
```

こうしてやり、リンク箇所に `route('price_page')` と置き換えて行きます。

