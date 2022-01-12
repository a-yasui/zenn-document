---
title: "[Laravel] validate in Request"
---

ユーザが入力した値をチェックする機構としてバリデーションがあります。
バリデーションは `\Validate::make()` で使用する方法がありました。
Laravel5以降では Request クラスを継承したクラスに `rules()` メソッドを実装する事で、コントローラー内に書かず、処理が見やすくなります。

# Better Pattern

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
