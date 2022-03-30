---
title: "SCP した時に .-/+oossssoo+/-. と表示されるアレ"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["fish","shell"]
published: true
---

マシンをたくさん持ってるとどれが何かわからないので、ログイン時に neofetch を使って表示しとるのですが、マシン間のファイル転送に scp を使うと `\033[?25l\033[?7l\033[0m\033[31m\033[1m            .-/+oossssoo+/-.` と表示されるんです。

セッション開始時に config.fish にベタに neofetch を実行されるのが原因。mosh 等でも同じことが起きるので、条件分岐を入れないと駄目ぽい。

see: https://fishshell.com/docs/current/faq.html#why-won-t-ssh-scp-rsync-connect-properly-when-fish-is-my-login-shell

[この記事 -- superuser.com](https://superuser.com/questions/1301138/scp-outputs-nonsense-message-and-fails) と現象が全く同じ。

## .config/fish/config.fish の設定

```fish
if type -q neofetch
  if status is-interactive
  command neofetch
  end
end
```

## .zshrc

```zsh

if [[ $- =~ "i" ]]; then
	neofetch
fi
```

