---
title: "Git Clone メモ"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['git']
published: true
---


Git リポジトリを Clone するのにいくつか方法がある。それのメモ書き

元: [GIT のアップデートから考える開発手法の潮流 -- speakerdeck.com](https://speakerdeck.com/yuukiyo/trends-in-development-methodology-from-the-latest-git-updates?slide=23)

- [git-scalar -- git-scm.com](https://git-scm.com/docs/scalar)


# Scalar

Git コマンドを速くさせるやつ。遅い理由は、リポジトリにあるファイルが大きすぎるため Clone に時間がかかる、ファイル数が多いので git status に時間がかかる、などといった感じで遅くなる。

遅さの対処として、Repository Clone は **Blobless Clone 機能** を使って、オブジェクトの初期取得範囲を絞っている、Clone後も **Sparse-checkout 機能** を使って必要なディレクトリを絞ったり、予めGit管理下にあるファイルを **File System Monitor 機能** により事前に計算している。

# Clone

普通に開発する分には Full-Clone で、過去の分を含めた全件を取得してくる。だが、リポジトリ内容がでかい、CIで動かすので過去の分は不要、などといった理由で HEAD しか使わないときもある。開発で使うが、でかいので時間がかかる場合は Git2.38 で入った新しい機能の scalar を使うのがいいっぽい

## Full Clone

`> git clone <repo>`

全部取得。普通に使うやつ。

## Blobless Clone

`> git clone --filter=blob:none <repo>`

HEAD のオブジェクトを取得し、Commit とそれらの Tree も取得するが、Head以外のBlob （ファイルの中身）は取得しない。

ScalarでCloneすると、これが使われるっぽい。

## Treeless Clone( Partial CLone )

`> git clone --filter=tree:0 <repo>`

HEAD のオブジェクトは全部取得して、Commitも取得するが、**過去のツリーとブロブは取得しない**。開発で使うのは非推奨。

## Shallow Clone

`> git clone --depth=1 --single-branch --branch=main <repo>`

この git-clone だと **HEAD のオブジェクトのみ** 取得する。Github で zip ダウンロードした感じ。利用は非推奨。

