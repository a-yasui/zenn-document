---
title: "Laravel Eloquent の　wasRecentlyCreated / wasChanged / isDirty"
emoji: "🐥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['laravel']
published: true
---


Eloquent の wasChanged/isDirty は DBへ保存後・保存前に値が変更されているのかを確認するのに使いますが、そもそも初めてDBへレコードを追加したかどうかを Model だけで判別するのはどうやるんだっけなというアレ。

ドキュメント載ってない気がする。一応 [5.1](https://github.com/illuminate/database/blob/5.1/Eloquent/Model.php#L1576)〜[8.x](https://github.com/illuminate/database/blob/8.x/Eloquent/Model.php#L1108) で使える機能で、 Model に `wasRecentlyCreated` プロパティがあるので、それを見れば良いみたい。


## 新規作成時

```
>>> $u = new \App\Models\User();
=> App\Models\User {#4529}
>>> $u->email = 'hoge+1212@example.com';
=> "hoge+1212@example.com"
>>> $u->name ='hoge';
=> "hoge"
>>> $u->password = bcrypt('test');
=> "$2y$10$regfMOYODprgdCczcDE4.ebgu65kDhlGWes.zMH0ULJZHJ9oyzRtO"
>>> $u->save();
=> true
>>> $u->id
=> 162
>>> $u->wasRecentlyCreated;
=> true
>>> $u->wasChanged();
=> false
>>> $u->isDirty();
=> false
```

## 更新時

```
>>> $u = \App\Models\User::find(162);
=> App\Models\User {#4530
     id: 162,
     name: "hoge",
     email: "hoge+1212@example.com",
     email_verified_at: null,
     last_login: null,
     deleted_at: null,
     created_at: "2021-07-30 16:28:36",
     updated_at: "2021-07-30 16:28:36",
   }
>>> $u->password = bcrypt('hogewer123');
=> "$2y$10$i9q7cQ0XQvTXyWbTefu59u.6S2Y3KuOIuEa/RvmsaXgOjWhtnzu/a"
>>> $u->save();
=> true
>>> $u->wasRecentlyCreated
=> false
>>> $u->wasChanged();
=> true
>>> $u->isDirty();
=> false
>>> 
```



