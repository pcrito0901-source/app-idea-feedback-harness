# App Idea Feedback Harness

**「そのアプリ、本当に作るべきか」を7体のAI Agentで検証し、アプリ案そのものを改善するための Claude Code ハーネス。**

```
                    ┌→ Customer Researcher ──┐
                    │                         │
                    ├→ Product Strategist ────┤
                    │                         │
User → Intake ──────┼→ Growth Strategist ─────┼→ Critic → Idea Chair
                    │     （並列・独立）        │              │
                    ├→ Business Strategist ───┤              ↓
                    │                         │      BUILD / ITERATE /
                    └→ Technical Analyst ─────┘      VALIDATE / PIVOT / KILL
```

---

## 1. これは何をするものか

あなたが「こういうアプリを作ろうと思っている」と一言入力すると、

1. **5体の専門Agent** が、それぞれ別の視点から **独立して** 分析する
2. **Critic（批判担当）** が、その5つの分析を反証する
3. **Idea Chair（議長）** が全部を統合して **意思決定する**
4. 未検証の重要仮説があれば、**あなたが外の世界で実行する検証実験** を設計する
5. あなたが検証結果を持ち帰って入力すると、それを反映して **次のラウンド** が回る

これを繰り返して、アプリ案そのものを育てます。

```
Idea v1 → 検証 → Idea v2 → 検証 → Idea v3 ...
```

**このハーネスはコードを1行も書きません。** 実装は別のハーネスの仕事です（→ 9章）。

## 2. なぜ使うのか

個人開発で最も多い失敗は、**技術的に作れなかったこと** ではありません。

- 作ったけど誰も使わなかった
- 作ったけど誰も知らなかった
- 作ったけど2回目に開かれなかった
- そもそも、その問題は誰も困っていなかった

これらは **作る前に、数日で分かることが多い** です。
このハーネスは、3ヶ月かけて気づくはずだったことを、最初の1週間で突きつけます。

### このハーネスがやらないこと

- ❌ あなたの案を褒める
- ❌ 「良いアイデアですね、頑張ってください」で終わる
- ❌ 機能を追加して案を膨らませる

### このハーネスがやること

- ✅ あなたの案の **間違っている前提** を探す
- ✅ 作らない方がいい理由を積極的に探す
- ✅ **「作らない」（KILL）という結論も普通に出す**

KILL は失敗ではありません。**3ヶ月を節約できた成功です。**

## 3. Agent は何をするのか

| Agent | 何をするか | 特徴 |
|---|---|---|
| **@customer-researcher** | 誰の、どんな問題かを分析する | 「そのユーザーは今どうやって解決しているのか」を最重視する。代替手段がない問題は、たいてい問題ではない |
| **@product-strategist** | 解決策の形と最小のMVPを決める | **機能を削るのが仕事**。「このアプリの核は動詞1つで何か」を絞る |
| **@growth-strategist** | 最初の10人と100人をどこから連れてくるか | 「SNSを活用する」は禁止。**実在するコミュニティ名・キーワード** まで書く |
| **@business-strategist** | 誰が、なぜ、いくら払うか | 「ユーザーが増えてから収益化」を前提にしない。AI APIの原価が単価を超えないかも検算する |
| **@technical-analyst** | 1人で作れて、1人で運用し続けられるか | 「技術的には可能」で終わらない。**もっと小さく作る方法** を必ず提示する |
| **@critic** | 上の5つを **反証する** | 失敗要因TOP3を出す。全員が一致していたら、それは合意ではなく「全員が疑わなかった前提」だと考える |
| **@idea-chair** | 全部を統合して **決める** | 要約禁止。KEEP / CHANGE / REMOVE / VALIDATE / NEXT ACTION と、5つの判定から1つを必ず出す |

### なぜ5体を「独立して」動かすのか

先に他のAgentの意見を見せると、**全員が同じ結論に寄ってしまう** からです。
5体は互いの分析を見ずに書き、揃ってから Critic に渡されます。

## 4. 導入方法

Claude Code が使える環境があれば、それだけで動きます。追加のツールは不要です。

### パターンA: このリポジトリをそのまま使う

```bash
git clone https://github.com/YOUR_NAME/app-idea-feedback-harness.git
cd app-idea-feedback-harness
claude
```

### パターンB: 自分のプロジェクトにコピーする

```bash
cp -r .claude/agents/ /path/to/your-project/.claude/agents/
cp CLAUDE.md /path/to/your-project/CLAUDE.md
cp -r docs/ /path/to/your-project/docs/
```

`.claude/agents/` の7ファイルと `CLAUDE.md`、`docs/` が揃っていれば動きます。

> アプリ案ごとにフォルダを分けることを推奨します。`docs/` の内容が案ごとに独立するためです。

## 5. 新しいアプリ案をどう入力するか

Claude Code を起動して、**普通に一言書くだけ** です。

```
AIを使った習慣管理アプリを考えている
```

すると、いきなり分析は始まりません。まず **最大5つの質問** が返ってきます。

> - 想定しているユーザーは誰ですか。あなた自身ですか、特定の他人ですか
> - その人は今その問題をどう処理していますか
> - あなたはそのユーザーに接触できますか
> - 使える時間・予算はどれくらいですか
> - 収益化の意図はありますか

答えると `docs/idea.md` が作られ、確認後に **Round 1** が始まります。

分析が終わると、こういう出力が返ってきます。

```
DECISION: VALIDATE
スコア: 34/100（Evidence が無いため上限40点。これは正常）

Biggest Risk: この問題を「解決したい」と思っている人がいる証拠がゼロ

NEXT ACTION
1. 習慣化に挫折した人3人に20分ずつ話を聞く（今週中）
   → 場所: 「朝活」系のDiscordサーバー #雑談
2. 競合アプリの★1〜2レビューを30件読む（2時間）
3. LP を作るのは、1と2が終わってから
```

## 6. Feedback Loop の回し方

**このハーネスの本体はここです。** 1回の分析で終わりません。

```
アイデアを入力
    ↓
Round 1（5体 → Critic → Chair）
    ↓
Chair「重要仮説が未検証。VALIDATE です」
    ↓
検証実験を受け取る（docs/experiments.md）
    ↓
★ あなたが外の世界で実行する ★   ← ここだけAIはやりません
    ↓
結果をチャットに貼る
    ↓
Round 2（Evidence を反映して再分析）
    ↓
Idea v2 へ更新
    ↓
（繰り返し）
```

### 重要なルール

**Evidence が増えていないのに再分析しても、結論は変わりません。**
このハーネスは、新しい材料なしに再分析を求められたら断り、未実行の実験を提示します。
アイデアをこね続けるのではなく、**外に出て確かめる** ためのハーネスです。

## 7. 検証結果をどう入力するか

外で調べてきたことを、**そのまま貼るだけ** で構いません。

```
EXP-001をやってきた。5人に聞いた結果：

・Aさん（27歳・営業）「習慣アプリは3個試したけど全部1週間で消した。
  記録するのが面倒くさい」
・Bさん（31歳・エンジニア）「そもそも習慣にしたいことが無い」
・Cさん（25歳・学生）「continueってアプリ使ってる。課金もしてる」
・Dさん、Eさんは「特に困ってない」

LPも作った。100人来て登録は2人。
```

すると、分析の前に次の処理が自動で行われます。

1. `docs/experiments.md` の EXP-001 に Result / Learning / Decision が記入される
2. `docs/assumptions.md` の仮説の Status が更新される（この例なら REFUTED 寄り）
3. `docs/research.md` に **発言が原文のまま** 記録される
4. その後 Round 2 が始まる

### Evidence Level（証拠の強さ）

このハーネスは、AIの推測と現実の証拠を厳密に区別します。

| Level | 意味 | 例 |
|---|---|---|
| **L0** | AIの推測だけ | 「困っている人はいるはず」 |
| **L1** | Web・市場・競合情報 | 競合の価格表、レビュー数 |
| **L2** | ユーザーの発言 | インタビュー、レビュー本文 |
| **L3** | ユーザーの行動 | LPのCVR、Waitlist登録数 |
| **L4** | 支払い・継続 | 実際の課金、継続率 |

**L0 のままでは、スコアは40点以上になりません。** これは仕様です。
スコアを上げる唯一の方法は、外に出て L2 以上の証拠を持ち帰ることです。

> ちなみに、**あなた自身の「絶対いける」という確信は L0 です。** n=1 の推測として扱われます。

## 8. BUILD / ITERATE / VALIDATE / PIVOT / KILL とは

Idea Chair は毎回、必ずこの5つから1つを選びます。

| 判定 | 意味 | 次にやること |
|---|---|---|
| **BUILD** | 重要仮説が検証済み。作ってよい | 開発ハーネスへ移行（→9章） |
| **ITERATE** | 問題は実在するが、案の形が弱い | ターゲットや範囲を修正して再分析 |
| **VALIDATE** | まだ何も確認できていない | 実験を実行して証拠を取ってくる |
| **PIVOT** | 前提は否定されたが、別の有望な問題が見つかった | 案を作り直す。ゼロには戻らない |
| **KILL** | 前提が否定され、隣接する案もない | **やめる。これは正常な結論** |

**初回はほぼ必ず VALIDATE になります。** 落ち込む必要はありません。
Round 1 の時点で BUILD が出るとしたら、それは検証をサボっている証拠です。

### KILL について

**KILL は L2 以上の証拠（実際のユーザーの声や行動）がある場合にしか出ません。**
AIが「なんとなく厳しそう」と思っただけで案を殺すことはありません。その場合は必ず VALIDATE になります。
（例外は、法規制で提供できない・原価が上限価格を常に超えるといった構造的な不成立のみ）

そのうえで、このハーネスは KILL を出すことをためらいません。

- 「作らない」判断ができた = **3ヶ月と数万円を節約できた**
- KILL の過程で見つかった「別の問題」が、次の案の起点になることも多い

`docs/decisions.md` に理由が残るので、**同じ失敗を繰り返さずに次の案へ進めます。**

### それでも作りたい場合

止めません。最終決定権はあなたにあります。
ただし `docs/decisions.md` に「何を未検証のまま進んだか」だけは記録されます。

## 9. 開発ハーネスへの移行

このハーネスは **実装しません**。責務を分けています。

| ハーネス | 責務 |
|---|---|
| **App Idea Feedback Harness**（これ） | **何を作るべきか** を検証・改善する |
| [**Agent Quartet Harness**](https://github.com/Shin-sibainu/agent-quartet-harness) | **決まったものを実装する** |

`DECISION: BUILD` が出ると、Idea Chair が `docs/handoff.md` を生成します。

```markdown
# Handoff to Development Harness
## Product One-Liner / Target User / Core Problem / Core Action
## MVP Scope / Out of Scope
## Validated Facts（Evidence Level 付き）
## Unvalidated Assumptions
## Success Metric
```

これを Agent Quartet Harness の `@planner` に渡せば、そのまま仕様策定 → 実装 → デザイン → QA のパイプラインに乗ります。

```
@planner docs/handoff.md の内容で仕様を作って
```

## ファイル構成

```
app-idea-feedback-harness/
├── CLAUDE.md                      # オーケストレーションルール（Claude が読む）
├── README.md
├── LICENSE
│
├── .claude/agents/
│   ├── customer-researcher.md     # 誰の、どんな問題か
│   ├── product-strategist.md      # 解決策の形とMVP
│   ├── growth-strategist.md       # どうやってユーザーを集めるか
│   ├── business-strategist.md     # 誰が、なぜ、いくら払うか
│   ├── technical-analyst.md       # 1人で作れて運用できるか
│   ├── critic.md                  # 上の5つを反証する
│   └── idea-chair.md              # 統合して意思決定する
│
└── docs/
    ├── idea.md                    # 現在の最新アプリ案
    ├── assumptions.md             # 重要仮説と検証状況
    ├── research.md                # 市場・競合・ユーザーの一次情報
    ├── experiments.md             # 検証の計画と結果
    ├── decisions.md               # 意思決定と却下理由
    ├── scorecard.md               # ラウンドごとの評価
    └── versions/                  # アプリ案のバージョン履歴（実行時に生成される）
        ├── idea-v001.md
        └── idea-v002.md
```

`docs/` は会話履歴に依存しないための保存先です。
**セッションを閉じても、次に開いたときに続きから再開できます。**

## スコアカード

毎ラウンド、同じ基準で100点満点の採点が行われます。

| 項目 | 配点 |
|---|---|
| Problem Strength | 20 |
| Customer Clarity | 10 |
| Differentiation | 15 |
| Distribution | 15 |
| Retention Potential | 15 |
| Monetization | 10 |
| Feasibility | 10 |
| Evidence Quality | 5 |

**ただし総合点だけで BUILD は判断されません。**
どれか1項目でも配点の40%を下回ると Critical Risk として扱われ、
たとえ総合80点でも「そこを潰す検証」が優先されます。

## 前提条件

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) が使える環境
- Web検索が使えると、競合・市場調査の精度が上がります（必須ではありません）

## ライセンス

MIT
