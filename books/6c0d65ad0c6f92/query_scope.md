---
title: "[Laravel] Query Scope"
---

`where` 句で並べて条件を絞ってデータの取得をする。その特にパターンがあるときに使いまわしを頻繁にする。都度コピペしていると、いつか間違えるだろうからカスタマイズをした `where~~` Scope を Eloquent に追加する。

参考 : [Create query scopes for complex where()s. -- Samuel Štancl -- twitter.com](https://twitter.com/samuelstancl/status/1272826431609933825)

## Old

```php
$new_gmail_users = User::where('email', 'like', '%@gmail.com')
	->where('created_at','>','2020-01-01 00:00:00')
	->orderBy(User::CREATED_AT, 'desc')
	->get();
```

## New

```php
/**
 * @method static \Illuminate\Database\Eloquent\Builder|User whereNewGmailUsers()
 */ 
class User extends Authenticatable
{
	public function scopeWhereNewGmailUsers(\Illuminate\Database\Eloquent\Builder $builder){
		$builder->where('email', 'like', '%@gmail.com')
		        ->where('created_at','>','2020-01-01 00:00:00');
	}
}


$new_gmail_users = User::whereNewGmailUsers()
	->orderBy(User::CREATED_AT, 'desc');
```

