# ラウンドの作業記録（rounds）

> 1ラウンド分の分析の**生データ**を置く場所。
> `docs/` の他のファイルが「結論」を残すのに対し、ここは「結論に至る前の材料」を残す。

## なぜ必要か

`@customer-researcher` などの専門 Agent は、分析結果を会話の中で返すだけで、
どこにも保存されない設計だった。そのためセッションが切れたり文脈が圧縮されたりすると、
`@idea-chair` に渡す材料が消えて、5体の分析をやり直すことになっていた。

ここに保存することで、**途中で中断しても続きから再開できる**。

## 構成

```
docs/rounds/
└── round-1/
    ├── customer-researcher.md
    ├── product-strategist.md
    ├── growth-strategist.md
    ├── business-strategist.md
    ├── technical-analyst.md
    ├── opportunity-scout.md
    └── critic.md
```

- 保存するのは **メインスレッド**（専門 Agent 自身は書き込まない。並列実行時の競合を防ぐため）
- 各 Agent が返した本文を **そのまま** 保存する。要約しない
- 過去ラウンドのファイルは**書き換えない**
