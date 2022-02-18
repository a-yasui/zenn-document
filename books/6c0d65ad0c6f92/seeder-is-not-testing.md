---
title: "[laravel] The Test does not using Seeder"
---

言いたいこと：Seeder にテストデータを入れてはいけません。Seederにあるべきなデータは固定値だけです。

see : https://readouble.com/laravel/8.x/ja/seeding.html

# ✘ wrong

RDBに複数のテーブルに複数のレコードを挿入してテストを実行させる。それぞれが複雑に入り乱れているので、Seeder で直接記入して確認をしており、テストが増える度にSeederも増えていく。

テストのためにと書き加えていくことになり、不要なデータが増える一方になる。そして、本番運用を開始する時に、初期データが存在すると認識して本番デプロイした時に不要なデータを増やす要因になる。

# ○ best

対象テストに都度 Factory でテストデータを挿入する方法が望ましい。

複雑なレコード作成に Eloquent がそのまま使え、他のテストに影響のないデータ構成を作成することができる。Seeder は全環境に同じデータベースの値を保持させるので、全てのデータベースを使うテストに共通して使ってしまうことになる。

see : https://readouble.com/laravel/8.x/ja/database-testing.html#concept-overview
