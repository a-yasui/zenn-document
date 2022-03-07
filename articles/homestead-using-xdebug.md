---
title: "Homestead で PHP8.1 の XDebug を使う方法"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['php']
published: false
---

基本的には [Laravel Homestead + PhpStormでXdebugを有効化する -- qiita.com](https://qiita.com/_hiro_dev/items/07e87a7d95bdaea98ad4) の手順で動きます。

ただ、PHP8 からは、 xdebug の設定プロパティが変更されていて、少し手こずったのでメモ書き。

## ホスト設定

Vagrant上で実行します。

```
# PHP8.1 に変更します。変更済みの時は何も出力されません。
vagrant@homestead:~$ php81
update-alternatives: using /usr/bin/php8.1 to provide /usr/bin/php (php) in manual mode
update-alternatives: using /usr/bin/php-config8.1 to provide /usr/bin/php-config (php-config) in manual mode
update-alternatives: using /usr/bin/phpize8.1 to provide /usr/bin/phpize (phpize) in manual mode

# Xdebugの設定ファイルを確認
vagrant@homestead:~$ php --ini | grep xdebug
# デフォルトでモジュールが無効のため、何も表示されない

# モジュールを有効化
vagrant@homestead:~$ sudo phpenmod xdebug

# もう一度確認
vagrant@homestead:~$ php --ini | grep xdebug
/etc/php/8.1/cli/conf.d/20-xdebug.ini, # ← 設定ファイルが追加された

# 設定ファイルを編集
vagrant@homestead:~$ sudo vim /etc/php/8.1/cli/conf.d/20-xdebug.ini
```

以下の内容に書き換える。

xdebug.remote_portが9000だと動かなかったので、9001にする。php-fpmのデフォルトポートが9000なので、それと競合するっぽい。

そして PHP7.x と PHP8.x では XDebug のオプションが変更されていますので、PHP7.x の設定をそのまま 8.x に移すことはできません。


`sudo vi /etc/php/8.1/cli/conf.d/20-xdebug.ini` とかで編集をしてください。

```
zend_extension=xdebug.so
xdebug.idekey = PHPSTORM
xdebug.show_error_trace = 1
xdebug.max_nesting_level = 512
xdebug.client_host = 10.211.55.2
xdebug.client_port = 9001
xdebug.mode = debug
xdebug.start_with_request = yes
xdebug.log = /tmp/xdebug.log
```

再起動して設定を反映します。

```
vagrant@homestead:~$ sudo service php8.1-fpm restart
```

以上で動くようになりました。


