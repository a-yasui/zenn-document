---
title: "[PG] Use DTOs"
---

極端に引数が多くなると見づらくなり、使う変数が多くなります。
これにより混乱を起こしやすくなるので、構造体を作成してどうにかやりくりをする方法があります。

英語だと Data Transfer Objects と言われるみたいです。

参考: [Use Data Transfer Objects (DTOs). Samuel Štancl
 -- twitter.com](https://twitter.com/samuelstancl/status/1272822466528845825)

# ✗ woozy

```php
public function log(string $message, $url, $route_name, $route_data, $campaign_code, $traffic_source, $referer, $user_id, $visitor_id, $timestamp)
{
	...
}
```

# ○ best

```php
public function log(Visit $visit)
{
	...
}

class Visit {
	public string $message;

	public string $url;
	public ?string $routeName;
	public array $routeData;

	public ?string $campaign_code;
	public array $traffic_source = [];

	public ?string $referer;
	public ?int $user_id;
	public ?int $visitor_id;

	public \DateTime $timestamp;
}

```

