---
title: "NTFSでフォーマットしたUSBメモリーをMacで読み書きする"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['mac']
published: true
---

最近の外部SSDが小さくて便利です。安いものから高いものまで末広がりだけど、まぁメーカー依存な部分もあります。それはともかく、Windows で NTFS でフォーマットした外部SSDをMacで読み取ることは、標準機能で可能です。しかし書き込みはできません。今回は書き込みもしたかったですので、それのインストールしたメモ書きです。

私の環境:

- macOS 14.2.1
- Mac Book Air M1, 2020
- ディスクは一つのボリュームに入れている。
- インストール日：2024-01-07

# 概要

1. Mac Fuse + NTFS-3G で書き込み可
2. Mac Fuse インストール後、再起動にコツいるかも
3. Mac のセキュリティの都合でこの文章も古くなるかも。

# インストール

mac で ntfs を読み取るソフトウェアはたくさんあるみたいですが、コマンドだけでしれっとやりてぇなぁという思いがあるので HomeBrew だけを使います。

いくつか注意点

1. homebrew-fuse の tap は必要
2. ntfs-3g-mac を指定しないとだめ。 ntfs-3g だけだとlinux用を入れようとしてコケる。 
3. 詳しくは [issue#818 github.com/osxfuse](https://github.com/osxfuse/osxfuse/issues/818) を読んで。

```shell
> brew tap gromgit/homebrew-fuse
> brew install --cask macfuse
> brew install ntfs-3g-mac
```

# 使い方

**インストール直後ではなく**、初めて使ったときは「システム設定」の「プライバシーとセキュリティ」のアラートが出るはずです。機能上、どうしても再起動が必須となります。

再起動後、kernel Extension が入っているか確認します。私の環境のときはこんな感じでした。

```shell
> kextstat | grep fuse
Executing: /usr/bin/kmutil showloaded
No variant specified, falling back to release
  250    0 0                  0x18b0     0x18b0     io.macfuse.filesystems.macfuse (2128.20) BBB320C0-222A-3582-85BE-3C47BA8FAD36 <7 5 4 3 1>
```

そして外部SSDを接続したとき、下記のような状況になります。 *read-only* 属性がついているので、コマンドで一度アンマウントしてから再度マウントします。

```shell
> mount | grep ntfs
/dev/disk4s2 on /Volumes/SSPS-US (ntfs, local, nodev, nosuid, read-only, noowners, noatime)

# 一度アンマウント
> sudo umount /dev/disk4s2
Password:

# マウントポイントを作成
> sudo mkdir /Volumes/SSPS-US

# Fuse を使ってマウント
> sudo mount_ntfs -o noappledouble /dev/disk4s2 /Volumes/SSPS-US

# Fuse を使ったマウントになるので、NTFSの表記がなくなる
> mount | grep disk4s2
/dev/disk4s2 on /Volumes/SSPS-US (macfuse, local, synchronous, noatime)
```

シェルスクリプトを作って `mount_ntfs` を作ったほうが良いかもしれない。こんな感じのもの : [ntfs-3g.rb github.com/fasterthanlime/homebrew-mingw](https://github.com/fasterthanlime/homebrew-mingw/blob/master/Library/Formula/ntfs-3g.rb#L49)

マウントポイントを指定したら再マウントする感じのほうが楽かもなぁ

```shell
#!/bin/bash

VOLUME_NAME="${@:$#}"
VOLUME_NAME=${VOLUME_NAME#/Volumes/}
USER_ID=0
GROUP_ID=0
LOG_FILE="/var/log/mount-ntfs-3g.log"

if [ `/usr/bin/stat -f %u /dev/console` -ne 0 ]; then
	USER_ID=`/usr/bin/stat -f %u /dev/console`
	GROUP_ID=`/usr/bin/stat -f %g /dev/console`
fi

/opt/homebrew/sbin/mount_ntfs \
-o volname="${VOLUME_NAME}" \
-o local \
-o noappledouble \
-o negative_vncache \
-o auto_xattr \
-o auto_cache \
-o noatime \
-o windows_names \
-o user_xattr \
-o inherit \
-o uid=$USER_ID \
-o gid=$GROUP_ID \
-o allow_other \
"$@" >> "${LOG_FILE}" 2>&1

exit $?;
```

# 参考

- [https://github.com/osxfuse/osxfuse/wiki/NTFS-3G](https://github.com/osxfuse/osxfuse/wiki/NTFS-3G)
- [issue #818 github.com/osxfuse](https://github.com/osxfuse/osxfuse/issues/818#issuecomment-985739918)
