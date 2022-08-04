---
title: "Laravel DB Migration 12 Tips"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["laravel"]
published: true
---

[Laravel Migrations: 12 Useful Tips in 12 Minutes -- youtube](https://www.youtube.com/watch?v=bSQcmcu6yHc) のメモ書き

laravel の6あたりで知識が止まってて、意外と migration や db まわりに変更があったのに今更知ったので、それのピックアップ。

:::message
Laravel 9 系のことを書いています。上記の youtube は Laravel8 ぐらいの時の話なので、ここで書いてる事とは微妙に違いがあります。
:::

# DB リレーション

外部キーを張る時に `foreign()->references('id')->on('books')` としていたのが `foreignId('book_id')->constrained()` ですむようになった。

see : [Foreign Key Constraints -- laravel.com](https://laravel.com/docs/9.x/migrations#foreign-key-constraints)

## 旧コード

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

Schema::table('posts', function (Blueprint $table) {
    $table->unsignedBigInteger('user_id');
 
    $table->foreign('user_id')->references('id')->on('users');
});
```
## foreignId を使ったコード

書き方はいくつかあります。

ただ constrained を使う時は、それだけを使った時は unsigned bigint のカラムが追加されるだけっぽいです。バージョンによって微妙に挙動が違うかもしれない。

1. テーブル名はカラム名の先頭句にあるので指定しないパターン

```php
Schema::table('posts', function (Blueprint $table) {
    $table->foreignId('user_id')->constrained();
});
```

1. テーブル名は明示的に指定する

```php
Schema::table('posts', function (Blueprint $table) {
    $table->foreignId('user_id')->constrained('user');
});
```

1. 関連付けに update/delete 成約をつける。

`->constrained()` のみでは成約はつかないっぽいので、update/delete に cascade 制約をつける。

```php
Schema::table('posts', function (Blueprint $table) {
    $table->foreignId('user_id')
      ->constrained('user')
      ->onUpdate('cascade')
      ->onDelete('cascade');
});
```

1. nullable化

これには注意が必要で、 **必ず `constrainted` より前に実行させる** 必要がある。

```php
Schema::table('posts', function (Blueprint $table) {
    $table->foreignId('user_id')
      ->nullable()
      ->constrained();
});
```

## autoincrement が UnsignedBigInteger に変更

`$table->increments()` で Auto_Increment が入ります。Laravel5.8 あたりまではカラムの型は `UnsignedInteger` でしたが、6.0 以降は `BigUnsignedInteger` になります。 


# Migration の順番

:::message
マイグレーションの順番は Laravel5 の頃からの仕様の話です。しかしそれ以外は最近の Laravel です（多分7以降…？）
:::

migration の順番はファイル名で決まります。
__migrations/__ ディレクトリに先頭はタイムスタンプ（ __YYYY_MM_dd_ss__　といった形）になっています。 このタイムスタンプを書き換えて、順番を操作してください。

## Migration のチェック

Migration の状態はコマンド `migrate:status` を実行して確認ができます。手元で試した限りでは、表示形式はバージョンによって異なるみたいです。

```shell
> php artisan migrate:status
+------+-------------------------------------------------------------------+-------+
| Ran? | Migration                                                         | Batch |
+------+-------------------------------------------------------------------+-------+
| Yes  | 2019_08_07_074124_development_schema_running                      | 1     |
...
```


# Timestamp 型のデフォルト値

`$table->timestamp('review_date')->default('now')` は動きません。 `NOW()` は MySQL 等では動かないからです。
`$table->timestamp('review_date`)->useCurrent()` を使うと良いみたいです。

もし更新した時を保持する時は `useCurrentOnUpdate()` を使うと良いみたいです。

see : [column-modifiers -- laravel.com](https://laravel.com/docs/9.x/migrations#column-modifiers)

# 作られたばかりの Migration File を変更する

**stub** を変更する事になる。 `php artisan stub:publish` を実行して、 `stubs/migration_create.stub` ファイルが作成されます。

デフォルトでは下記のような感じのファイルです。これを変更して保存し、再度 `php artisan make:migration` を実行すれば変更された内容を元に migration ファイルを作成します。


:::message
なお `stubs` ディレクトリできるファイルは各種 `make:` コマンドを実行してファイルを作成するときに使うファイルです。 `stubs` にないファイルを使おうとした時は、元から laravel が持っているファイルを使用します。必要なファイルだけ残して他のファイルを消すのが良いかもしれません。
:::

```php:stubs/migration_create.stub
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class {{ class }} extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('{{ table }}', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('{{ table }}');
    }
}

```

# Single SQL File

個人的には、あまりつかわない。

`php artisan schema:dump` を実行すれば `schema/~.dump` ファイルができます。少し古いバージョンのときは `databases/schema/~-schema.dump` になります。

これにはテーブルのスキーマーと、db migration のデータのみ書き出されます。


# Drop Multi Column

`$table->dropColumn()` の引数は配列にでき、複数カラムを削除する時はそれらを引数の配列に入れて削除することができます。 


# Rollback or refresh x step

`migrate:rollback --step=3` は過去三回のマイグレーションをロールバックする処理を実行します。一方で `migrate:refresh --step=3` は過去三回のマイグレーションをロールバックする処理をした後、通常のマイグレーションを実行します。もし開発中で単にマイグレーションをやり直したい時は `migrate:refresh` の方が良いです。


# AutoIncrement の開始値を指定

`$table->id()->from(100)` とすれば、100 番から開始が始まります。

# Make Migration underscore vs space

実は `php aritsan migrate add_name_column_to_aqualium` の実行結果と `php artisan migrate "add name column to aqualium"` は同じ実行結果になります。