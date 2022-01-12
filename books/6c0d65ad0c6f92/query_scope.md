---
title: "[Laravel] Query Scope"
---

`where` 句で並べて条件を絞ってデータの取得をします。
その特にパターンがあるとき、その `where 文`は使いまわしを頻繁にします。
都度コピペしていると、変更があった時に間違える可能性が高いです。
ですので、カスタマイズをした `where~~` Scope を Eloquent に追加する方法を取るのが良いです。


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

