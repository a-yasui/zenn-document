---
title: "[Laravel] OrderBy CreatedAt ?"
---

Eloquent でソートした状態でモデルデータを取得するのに `orderBy` メソッドを使います。
それを使うときに、都合良いのが Eloquent が使う `created_at` でソートをする事です。
しかし場合によっては、別フレームワークでスキーマを作成した等で、 `create_date` みたいに **Eloquent 標準ではない** カラム名になっている事があります。

テーブルごとに作成日用のカラム名を覚えるは面倒なので、`created_at` レコードを指定する時は `Model::CREATED_AT` 定数で指定するのが良いです。

```php
$orders = \App\Models\User::orderBy( \App\Models\User::CREATED_AT, 'desc')->get();
```
