---
title: "Laravel 12 Upgrade Memo"
emoji: "🔖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["laravel", "php"]
published: true
---

Laravel 12 Upgrade Memo.

記述日：2025/02/24

個人的に全く追っかけていなかったため、何が変わったのかわからないまま書いてます。

- see : https://laravel.com/docs/12.x/upgrade
- see : https://github.com/laravel/laravel/releases/tag/v12.0.0
- see : https://laravel-news.com/laravel-12

# 変更点

今回からのスターターキットには、これらが含まれます。

- Social authentication
- Passkey authentation
- Email-based auth
- SSO with WorkOS

## PHP8.2 以上

PHP8.2 ~ PHP8.4 が動作環境となります。これは Laravel 11 から変わりません。

## メンテナンス期間

- Bugfix: August 13th, 2026
- Security Fix: February 24th, 2027

# Upgrade 方法

_composer.json_ の `laravel/framework: '^12.0'` にしてください。

## Carbon3

Laravel12 からは Carbon2.x は終了し、Carbon3.x がサポートされます。

# Low Impact Changes

## Concurrency

Concurrency::run の返り値が変わります。

```php
$result = Concurrency::run([
    'task-1' => fn () => 1 + 1,
    'task-2' => fn () => 2 + 2,
]);
 
// ['task-1' => 2, 'task-2' => 4]

```

## Requests

`mergeIfMissing` メソッドが追加されました。デフォルト値を取っておきたいときに使用します。

## Validation

`image` Rule に `svg` が追加されましたが、その場合はオプション `allowSvg:true` が必須になります。

## Tailwind CSS v4.0

Tailwind CSS が v4.0 を使うようになりました。アップグレード時は既存のままで良いと思われます。

## public/index.php の更新

type hints が付いただけなので、それほど重要ではなく、現行のままでも使えますが、更新はあります。


