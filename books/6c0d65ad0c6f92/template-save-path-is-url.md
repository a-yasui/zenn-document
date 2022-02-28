---
title: "[laravel] HTML テンプレートとURLは合わせたほうが良い"
---

言いたいこと：HTML テンプレートとURLは合わせたほうが良い

一人開発でも起きますが、blade/htmlテンプレートのパスと URL がミスマッチしていると、編集したいページと見ているテンプレートファイルの違いに混乱してしまいミスが出やすくなります。

極力、合わせたほうが良いです。

## ✗ bad

```php

Route::view('/admin/user',           "admin.user_index");
Route::view('/admin/user/{user_id}', "user.admin");
Route::view('/admin/user/{user_id}/entries', "user.entries.admin");

Route::view('/user/entries', "user.entries.index_top");
Route::view('/user/entries/{entry_slug}/comment/{comment_id}', "entry.comment");

Route::view('/entries',              "entry.top_page");
Route::view('/entries/{entry_slug}', "entry.sub_page");

```

## ○ Good

```php

Route::view('/admin/user',           "admin.user.index");
Route::view('/admin/user/{user_id}', "admin.user.view");
Route::view('/admin/user/{user_id}/entries', "admin.user.entries");

Route::view('/user/entries', "user.entries.index");
Route::view('/user/entries/{entry_slug}/comment/{comment_id}', "user.entry.comment");

Route::view('/entries', "entry.index");
Route::view('/entries/{entry_slug}', "entry.view");

```

