---
title: "[Laravel] 入力値チェックは誰がする？"
---

ユーザが入力した値をチェックする機構としてバリデーションがあります。バリデーションは `\Validate::make()` みたいな使い方があります。それより Request クラスを継承したクラスに `rules()` メソッドを実装する方法が、コントローラー内に余計に書かなくてすむようになります。

# Better Pattern

## コントローラーで処理

```php
namespace App\Http\Controllers;

class BlogController extends Controller {
	public function create(\App\Http\Request\CreateNewArticleRequest $request){
		$article = new \App\Models\Article();
		$article->subject = $request->getSubject();
		throw_if( $article->save() === false, new DatabaseSaveErrorException($article) );
		return redirect()->route('user.blog.write', ['article_id' = $article]);
	}
}
```

Requests はこんな感じ。

```php
namespace App\Http\Request;

class CreateNewArticleRequest extends \Illuminate\Foundation\Http\FormRequest
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
