---
title: "VeraPDF Validation Profiles のバージョン番号っぽいやつの意味"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["verapdf", "pdf"]
published: true
---

PDF の内部データに変なのが無いか確認する OSS として [VeraPDF -- verapdf.org](https://verapdf.org/) / (VeraPDF GitHub)[https://github.com/veraPDF/] があります。

何に基づいてどういうerrorを出しているのか Validation の物が [Validation Profile -- github.com](https://github.com/veraPDF/veraPDF-validation-profiles) にあるのですが、 PDF/A-1a といった形でバージョンっぽい感じでパート分けされてます。こんなの覚えてられねぇってな訳でmemo書き。


| 表記           | 意味                 | ざっくり                             |
| ------------ | ------------------ | -------------------------------- |
| **PDF/A-1b** | Part 1 + Level B   | PDF 1.4 ベース。古くて制約が強い             |
| **PDF/A-2b** | Part 2 + Level B   | PDF 1.7 ベース。透明・JPEG2000・レイヤー等に対応 |
| **PDF/A-2u** | Part 2 + Level U   | 2b + Unicode マッピング必須             |
| **PDF/A-2a** | Part 2 + Level A   | 2b/2u より構造・タグ要求が強い               |
| **PDF/A-3b** | Part 3 + Level B   | 2b 相当 + 任意ファイル添付を許可              |
| **PDF/A-4**  | Part 4             | PDF 2.0 ベース。A/B/U レベルは廃止         |
| **PDF/A-4f** | Part 4 + f profile | PDF/A-4 で任意ファイル添付を許可             |
| **PDF/A-4e** | Part 4 + e profile | Engineering 用。3D/RichMedia 等     |

- see: https://docs.verapdf.org/cli/validation/

