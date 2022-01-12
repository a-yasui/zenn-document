---
title: '[Laravel] Make a base class'
---

プロジェクト作成時に `App\Http\Controllers` に `Controller` クラスが作成されます。作成する Controller クラスはそのクラスを基底として使います。同じように `App\Http\Requests` は `Requests` クラスを作成して、全ての Requests クラスはそれを基底として使うほうが良いです。

基底とするクラスは、 Laravel の仕様変更の都合でメソッド追加等といった、仕様変更時のみ使用します。

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

/**
 * Base on the All Request Class.
 */
abstract class Request extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    abstract public function authorize();

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [];
    }

}
```
