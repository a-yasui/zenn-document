---
title: "私がよく使うけど標準に入ってないコマンド"
emoji: "🙆"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['cli']
published: true
---

記述日：2025-07-07

たまにはこういうのを書かないと、自分自身が忘れる。個人的によく使うけど、macOS標準には入っていないターミナルコマンドのリストです。最近のLLM/AI系とは話は別です。

# 私の環境

- macOS 15.5
- Apple M3
- zsh( Apple 標準 )

基本的に標準搭載の物を使っています。マシンを変えた時にインストール祭りの時間を抑えたいのと、設定さえ移せば良いよねって所に持って行きたかった。

# ターミナル補助系

- `Homebrew` https://brew.sh/ja/
  mac に cli や GUI Application のパッケージ管理をするのに使う。
- `OhMyZsh` https://github.com/ohmyzsh/ohmyzsh
  zsh の prompt を良い感じにしたいので使ってる。
- `tmux` https://github.com/tmux/tmux/wiki
  terminal のセッション管理。
- `mosh` https://github.com/mobile-shell/mosh
  ssh だと tcp 接続で回線が切れたら終わるけど、これは回線が切れても復活したら再接続をする。あたしは tmux を併用する。
- `glow` https://github.com/charmbracelet/glow
  markdown を良い感じにターミナルで表示するやつ
- `rg` https://github.com/BurntSushi/ripgrep
  grep の代替え。速いけど、癖あって、バイナリファイル内までは見ない。
- `fd` https://github.com/sharkdp/fd
  find の代替え。速い。
- `autojump` https://github.com/wting/autojump
  cd コマンドでディレクトリ移動履歴を保持する。 `j` コマンドを使えば履歴から一番よく使ったディレクトリにジャンプする。一番使う。
- `glow` https://github.com/charmbracelet/glow
  markdown を cli で良い感じに表示させる

## Zsh の設定 -- add-zsh-hook

cd や autojump で移動した先に、 readme.md があれば表示するようにしてます。意外と便利で、間違えて移動した時とか嫌でも目にするので読んだりします。

```zsh
add-zsh-hook chpwd showReadme
function showReadme() {
  [[ -f readme.md && $(which glow) ]] && glow readme.md;
}

```

## Zsh の alias

マージしたので不要になったブランチを一気に消すコマンドです。

```zsh
alias -- git-cleanup-branches='git branch --merged | grep -vE '^\*|develop|main|master' | xargs git branch -d'
```

## ちょっと微妙だった系

- `atuin`: https://atuin.sh/
  コマンド履歴を共有して、他のマシンでも履歴が同じになるようにする。自身のサーバをたててやるのが理想。複数マシンで作業とかに向いてるけど、個人的にはそこまでだった。

# git系

- `tig` https://jonas.github.io/tig/
  コミット履歴を一覧表示させるのに使ってる。
- `gh` https://cli.github.com/
  Github CLI. claude とかでも使うし、issue 一覧確認するのに使ったりする。

# 開発系

- `bun` https://bun.sh/
  npm の代替えとして使ってる。パッケージのインストールは npm でやって、それ以降は bun で実行とかでやる事が多い。
- `uv` https://docs.astral.sh/uv/
  Python のバージョン管理。Python3.8 や Python3.10 とか混合状況を解消するために使ってる。
- `idea` https://pleiades.io/help/idea/working-with-the-ide-features-from-command-line.html
  IntellJ で該当ファイル・ディレクトリを開くためのコマンド。Jetbrain 製品なら入ってるはず
- `jq` https://jqlang.org/
  JSON Text を良い感じに表示する。query も書けて、フィルタリングや集計処理も可能。
- `rclone` https://rclone.org/
  色んなストレージサーバにあるファイルと同期するためのコマンド。claudeflare R2 や aws s3 とか iCloud とか。

