---
title: "[PG] Nested Return Value"
---

Nest が深くなった if 文の場合は、早めに返り値を返すようにリファクタリングをしたほうが良い

参考 : [Try to avoid unnecessary nesting by returning a value early. -- Samuel Štancl -- twitter.com](https://twitter.com/samuelstancl/status/1272822441392377856)

# ✗ wrong

```php
if($notificationSent){
	$notify = false;
} else {
	if($isActive){
		if($total > 100){
			$notify = true;
		} else {
			$notify = false;
		}
	} else {
		if($canceled){
			$notify = true;
		} else {
			$notify = false;
		}
	}
}
return false;
```

# ○ best

```php
if($notificationSent){
	return false;
}

if($isActive && 100 < $total){
	return true;
}

if(!$isActive && $canceled){
	return true;
}

return false;
```
