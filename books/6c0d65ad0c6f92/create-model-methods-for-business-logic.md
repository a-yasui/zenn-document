---
title: "[Laravel] Create model methods for business logic / Create Action classes"
---

コントローラーに記述するコード量は少ないのに越したことはありません。しかし、例えば、注文に応じて請求用のデータを挿入する処理が必要になり、コントローラーに書いてしまうことは避けたいです。

そういうときは注文モデル（Order）にメソッドを追加して処理を書いてしまうことです。

参考 : [Create model methods for business logic -- Samuel Štancl -- twitter.com](https://twitter.com/samuelstancl/status/1272822448145272832)


## ✗ bad

```php

// OrderController::postOrder

DB::transaction(function()use($order){
	$invoice = $order->invoice()->create();
	$order->pushStatus(new AwaitingShipping());
	return $invoice;
});
```


## ○ Good

```php

// OrderController::postOrder
$order->createInvoice();

// Order::createInvoice
if($this->invoice()->exists()){
	throw new OrderAlreadyHasAnInvoice('Order already has an Invoice');
}

$self = $this;
return DB::transaction(function()use($self){
	$invoice = $self->invoice()->create();
	$self->pushStatus(new AwaitingShipping());
	return $invoice;
});


```

## ◎ best

細かいビジネスロジックな処理は、後から要望が増えていきます。そして色んな所から呼び出されたりしますので、そういうのだけ分ける事をします。

```php
// Order
public function createInvoice(): Invoice
{
	if($this->invoice()->exists()){
		throw new OrderAlreadyHasAnInvoice('Order already has an Invoice');
	}
	return app(\App\Action\CreateInvoiceForOrder::class)($this);
}


// App\Action\CreateInvoiceForOrder
class CreateInvoiceForOrder
{

	public function __invoke(Order $order): Invoice
	{
		return DB::transaction(function()use($order){
			$invoice = $order->invoice()->create();
			$order->pushStatus(new AwaitingShipping());
			return $invoice;
		});
	}
}
```