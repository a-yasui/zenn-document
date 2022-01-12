---
title: "[Laravel] OrderBy CreatedAt ?"
---

Eloquent でソートした状態でモデルデータを取得するのに `orderBy` メソッドを使う。そのときに都合良いのが Eloquent が使う `created_at` でソートをする事だ。しかし場合によっては `create_at` みたいに **Eloquent 標準ではない** カラム名になっている事がある。

テーブルごとに覚えるのも面倒なので、`created_at` レコードを指定する時は `Model::CREATED_AT` 定数で指定するのが良い。

```php
$orders = \App\Models\User::orderBy( \App\Models\User::CREATED_AT, 'desc')->get();
```
