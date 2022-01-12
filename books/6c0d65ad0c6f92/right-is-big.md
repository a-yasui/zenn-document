---
title: "[PG] Right is big"
---

ただの経験則な事。

ほぼ全てのキーボードの数字は、左側に「1」があり、右に向かって数が大きくなります。数値の比較演算子を使う時は「右大なり」を使うほうが良い。

100 <= $my_money <= 200

# ✗ wrong

せめて、左右どちらが大きいくなるのか決めたほうが良い

```php
( $my_money >= 100 && $my_money <= 200);
```

# ○ good

```php
// php
( 100 <= $my_money && $my_money <= 200);
```

```python
# Python
if 100 <= my_money <= 200:
	print "ok"
```
