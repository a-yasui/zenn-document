---
title: "strix を さくらのAIを使って使う"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['llm','strix','sakuraのAI']
published: true
---

# strix を さくらのAIを使って使う

これは、strixのLLMホスト指定が面倒な所があるので、それの部分のmemo書き。

strix のインストールはできる事を前提で書いてます。

プロジェクトの脆弱性を探すStrixというのがあります。これはLLMを使ってプロジェクト内の脆弱性を探す物ですが、OpenAIとかAnthropicの課金枠を使ってやると（経済的に）死にそうな気がするので、さくらのAIを使って安く済ませてみます。strix の推奨モデルがさくらのAIにはまだないので、Qweb3.6-35B-A3B を使って見ます。どれが良いのか何もわかってないけど。

- Strix: https://docs.strix.ai/
- さくらのAI : https://ai.sakura.ad.jp/sakura-ai/ai-engine/

## 設定

1. さくらのAI にログイン、アカウントトークンを発行する。

## 実行

```shell
STRIX_LLM="openai/preview/Qwen3.6-35B-A3B" LLM_API_BASE="https://api.ai.sakura.ad.jp/v1" LLM_API_KEY="<アカウントトークン>" strix --target ./
```

## 使用感

軽め（ログインして10個ぐらいのフォーム＋いくつかの外部通信用のAPIをする物）のプロジェクトで動かしました。

pentest: Deep, scope auto,mode interactive で、 260 request, 25,000,000 token ぐらいでした。



