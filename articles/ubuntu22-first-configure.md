---
title: "Ubuntu22.04 でよくやる初期設定"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ubuntu22"]
published: false
---

よく忘れる、というか絶対忘れるのでメモ書き。

## Swap を極力させない

sysctl で swappness の数値を 0 にすると、Swap書き出しを極力しないようになる。

```
> sudo sysctl vm.swappiness=0
```

再起動させた場合は 60 に戻るので、起動時の設定は下記のように追加する。

```
> sudo echo 'vm.swappiness=0' >> /etc/sysctl.d/99-sysctl.conf
```

## 日本語環境のインストール

フォントの調節は別記事： https://zenn.dev/at_yasu/articles/change-system-font-ubuntu22

それ以外にも日本語環境をインストールするのに `language-pack-ja` が必要。

```
> apt install language-pack-ja
```

## ufw

基本 http/https/ssh のみ受付可。



## ppa/php

PHP8.1 の enum を使いたいので ppa/php でインストール

```
> sudo add-apt-repository ppa:ondrej/php
> sudo apt install php8.1
```
