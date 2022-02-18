---
title: "[Laravel] validate in Request"
---

ユーザが入力した値をチェックする機構としてバリデーションがあります。
バリデーションは `\Validate::make()` で使用する方法がありました。
Laravel5以降では Request クラスを継承したクラスに `rules()` メソッドを実装する事で、コントローラー内に書かず、処理が見やすくなります。


# ○ good

- [良い点] 古いLaravel（5.1以前）でも動く
- [悪い点] Controller が肥大化し、読みにくくなる

```php
public function index (Request $request) {
	$validator = \Validate::make($request->all(), [
		'subject'=> ['required', 'string'],
	]);

	if ($validator->fails()) {
		return redirect('/errorpage')
		    ->withErrors($validator)
		    ->withInput();
	}

}
```

# ○ Better Pattern

- [良い点] Validationが全てRequestクラス内で処理されるので、Controllerに余計な処理を書かずに良くなる
- [良い点] getSubject などのメソッドで入力値の型キャストなどができる
- [面倒な点] Requestクラスを都度作ることになる。


```php
class CreateNewArticleRequest extends Request
{
	public function rules(){
		return [
			'subject' = ['required', 'string'],
		];
	}

	public function messages(){
		return [
			'subject.required' => 'タイトルを入力してください',
			'subject.string' => 'タイトルを入力してください',
		];
	}

	public function getSubject(): string
	{
		return (string)($this->input('subject'));
	}
}
```
