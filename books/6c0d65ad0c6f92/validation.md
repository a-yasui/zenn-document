---
title: "[Laravel] 入力値チェックは誰がする？"
---

ユーザが入力した値をチェックする機構としてバリデーションがあります。バリデーションは `\Validate::make()` みたいな使い方があります。それより Request クラスを継承したクラスに `rules()` メソッドを実装する方法が、コントローラー内に余計に書かなくてすむようになります。

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

	public function getSubject()
	{
		return (string)($this->input('subject'));
	}
}
```
