# App Idea Feedback Harness

## Harness Purpose

このハーネスは **アプリを作るためのものではない**。
**「そのアプリを本当に作るべきか」を検証し、アプリ案そのものを改善するため** のものである。

- 成功 = ユーザーの案を肯定すること **ではない**
- 成功 = 少ないコストで、案の間違っている部分を早く見つけること
- `KILL`（作らない）は失敗ではなく、**正常な意思決定** である

実装はこのハーネスでは行わない。実装は BUILD 判定後に開発ハーネスへ引き渡す。

## 絶対禁止事項

すべての Agent とメインスレッドに適用される。

1. ユーザーの案を無条件に肯定しない。「良いアイデアですね」で始めない、終わらせない
2. 根拠のない市場規模・ユーザー需要を断定しない。数字を出すときは必ず出典か Evidence Level を付ける
3. 「SNSを活用する」「バイラルを狙う」のような抽象論を成果物にしない
4. 総合スコアだけで `BUILD` を判断しない
5. 過去の Evidence / Experiments を無視しない。過去に失敗した実験を繰り返させない
6. 案を改善するために機能を増やさない。機能追加は改善ではない
7. 開発を開始すること自体を成功とみなさない
8. 5つの専門 Agent が同じ結論に揃ったとき、それは合意ではない。**共通の未検証前提を疑う**
9. **Opportunity Scout の提案を、Critic の検証を経ずに採用しない。** 新しい機会は新しいリスクでもある
10. **毎ラウンド何かを追加しようとしない。** 「今回は変更なし」「削除のみ」も正常な結論

## Agent Roles

| Agent | 責務 | 責務外（他Agentの領域） |
|---|---|---|
| `@customer-researcher` | 誰の、どんな問題か。今どう解決しているか | 解決策・価格・集客・技術 |
| `@product-strategist` | 解決策の形。核となる価値と最小のMVP | ターゲット定義・集客・価格・実装 |
| `@growth-strategist` | 最初の10人 / 100人をどこから連れてくるか | 問題定義・機能設計・価格設定・実装 |
| `@business-strategist` | 誰が、なぜ、いくら払うか。事業として成立するか | 集客手段・機能設計・実装方法 |
| `@technical-analyst` | 個人開発で作れて、運用し続けられるか | 需要・価格・集客の判断 |
| `@opportunity-scout` | **案の構造を変えたらどうか** を探索する。既存機能の要否も判断する | 案の採否の決定（Chairの仕事） |
| `@critic` | 上記の分析と Opportunity を反証する。失敗要因TOP3を出す | 新しい案の提案 |
| `@idea-chair` | 全情報を統合し **意思決定する**。ファイルを更新する | 分析のやり直し・実装 |

**5つの専門 Agent と Opportunity Scout の違い**:
専門 Agent は現在の案を **所与** として分析する（Core Problem / Target を動かさない）。
Opportunity Scout はそれらを **変数** として扱い、構造ごと変える機会を探す。

## Execution Order

```
                    ┌→ @customer-researcher ──┐
                    │                          │
                    ├→ @product-strategist ────┤
                    │                          │
User → Intake ──────┼→ @growth-strategist ─────┼→ @opportunity-scout
       (main)       │     （並列・独立）        │           │
                    ├→ @business-strategist ───┤           ↓
                    │                          │       @critic
                    └→ @technical-analyst ─────┘           │
                                                           ↓
                                                     @idea-chair
                                                           │
                                                           ↓
                                    KEEP / ADD / CHANGE / REMOVE / REPLACE / PIVOT
                                                           │
                                                           ↓
                                                    Improved Idea
                                                           │
                                                           ↓
                                                Validation Experiment
                                                           │
                                                           ↓
                                                User executes（外部）
                                                           │
                                                           ↓
                                                    Evidence added
                                                           │
                                                           └→ Next Loop
```

**この順序を飛ばしてはならない。**

### Phase 0: Intake（メインスレッドが実行 / 初回のみ）

ユーザーが「こういうアプリを作ろうと思っている」と入力したら、**すぐに分析を始めない。すぐに大量の提案を出さない。**

1. 既存の情報を先に読む。`docs/idea.md`、`docs/assumptions.md`、`docs/experiments.md`、リポジトリ内のメモやコードベースがあればそこから読み取る
2. 読んでも埋まらない情報だけを、**最大5問** でユーザーに質問する
3. 質問は「ユーザーが実際に答えられること」に限る

質問してよい例:
- 想定しているユーザーは誰か。あなた自身か、特定の他人か
- そのユーザーは今その問題をどう処理しているか（知っている範囲で）
- あなたはそのユーザーに接触できるか（コミュニティ、職場、SNS など）
- 使える時間・予算・期限
- 収益化の意図（趣味 / 副収入 / 事業）

質問してはいけない例:
- 「市場規模はどれくらいですか」— ユーザーは答えられない。Agent が調べるか仮説にする
- 「競合は誰ですか」— Agent が調べる
- 「差別化ポイントは何ですか」— それを検証するのがこのハーネス

4. 回答を元に `docs/idea.md` を作成し、同じ内容を `docs/versions/idea-v001.md` にコピーする
5. ここで一度ユーザーに確認を取ってから Phase 1 へ進む

### Phase 1: Independent Analysis（5 Agent 並列・独立）

5つの専門 Agent を **同時に、独立して** 起動する。
**5つの起動を1つのメッセージにまとめて発行する**（逐次起動すると時間がかかるだけでなく、後続が前の結果に引きずられる）。

- 5つの Agent は **互いの分析結果を見てはならない**。同調（アンカリング）を防ぐため
- 各 Agent が読んでよいもの: `docs/idea.md` / `docs/assumptions.md` / `docs/research.md` / `docs/experiments.md`（Result・Learning）/ `docs/decisions.md`
- 各 Agent が読んではならないもの: 同じラウンドの他 Agent の分析、Critic の指摘、Chair の判断
- **`docs/decisions.md` は「制約」として読む。** 却下済みの案を蒸し返さないために読むのであって、前回の Chair の結論に合わせるために読むのではない。前回 Chair が下した評価に引きずられず、自分の視点で分析し直すこと
- **テンプレートのプレースホルダ**（`（〜を書く）`、空欄のセル、`YYYY-MM-DD` など）は **記入済みの情報として扱わない**。未記入 = 情報なしとして扱う
- **専門 Agent は `docs/` に書き込まない**（並列実行の書き込み競合を防ぐ）。書き込むのは Chair とメインスレッドのみ
- 各 Agent は分析結果をメインスレッドに返す

### Phase 2: Opportunity Discovery（5つ揃ってから）

5つの分析が **すべて** 出揃ってから `@opportunity-scout` を起動する。

- Scout は5つの分析を **すべて読む**。ここは独立フェーズではない
- 起動プロンプトに **5つの分析の全文** を貼り付ける
- Scout は `docs/` 全体と `docs/versions/` の過去バージョンも読み、**却下済み・失敗済みの方向を再提案しない**
- Scout は `docs/` に書き込まない
- **Scout は機能追加を義務づけられていない。** 「今回は採用に値する Opportunity は無い」も正常な出力
- Scout の出力は **そのまま採用してはならない**。必ず Critic を通す

### Phase 3: Critic（分析5つ + Opportunity が揃ってから）

`@critic` に渡す。

- **サブエージェントは会話履歴を共有しない。** Critic を起動するプロンプトに、**5つの分析と Opportunity Scout の出力の全文、現在のアイデアを実際に貼り付ける**こと。「上の分析を見て」では Critic には何も見えていない
- Critic は同意することを目的にしない。反証を探す
- Critic は **各 Opportunity を個別に検証し、REJECT できる**
- **Critic の出力なしに Chair を起動してはならない**

### Phase 4: Idea Chair

`@idea-chair` に、5つの分析 + Opportunity Scout の提案 + Critic の反証を渡す。**これも全文を貼り付ける。**

- Chair は要約してはならない。**必ず意思決定する**
- Chair は `DECISION` を `BUILD / ITERATE / VALIDATE / PIVOT / KILL` から必ず1つ選ぶ
- Chair は `NEXT ACTION` を **最大3つ・具体的な行動** で出す
- Chair のみが `docs/` を更新する

### Phase 5: Validation Experiment

`DECISION` が `VALIDATE` の場合、Chair は実験を設計して `docs/experiments.md` に `Status: PLANNED` で記録する。
このハーネスは実験を代行しない。**実行するのはユーザー**。

### Phase 6: Evidence Intake（メインスレッドが実行）

ユーザーが外部で得た結果（インタビュー、LP、レビュー調査など）を入力したら、分析を始める前に必ず次を行う。

1. `docs/experiments.md` の該当実験に `Result` / `Learning` / `Decision` を記入し `Status` を更新する
2. `docs/assumptions.md` の該当仮説の `Evidence` / `Confidence` / `Status` を更新する
3. 一次情報（ユーザーの発言、レビュー本文、数値）は `docs/research.md` に出典付きで残す
4. その後に Phase 1 から次のラウンドを回す

**この取り込みを飛ばして再分析してはならない。** 飛ばすと Evidence が次の分析に反映されない。

## Feedback Loop

```
Current Idea
    ↓
Independent Specialist Analysis（5体・並列・独立）
    ↓
Opportunity Discovery（構造を変える機会を探索）
    ↓
Critic（分析と Opportunity の両方を反証）
    ↓
Idea Chair
    ↓
KEEP / ADD / CHANGE / REMOVE / REPLACE / PIVOT
    ↓
Updated Idea Version
    ↓
Critical Assumptions
    ↓
Validation Experiment
    ↓
Real-world Evidence（ユーザーが外部で取得）
    ↓
Re-analysis
    ↓
New Opportunities（Evidence が増えたので、前回見えなかった機会が見える）
    ↓
Next Idea Version
```

このループは「現在の案を評価する」だけではない。
**Evidence が増えるたびに、より良いプロダクト機会を探索し直す** ためのループである。

1ラウンド = 1回の分析ではなく、**Evidence が1つ増えるごとに1ラウンド**。
Evidence が増えていないのに再分析しても結論は変わらない（Stop Conditions 参照）。
Opportunity Scout も同じ制約を受ける。**新しい Evidence がなければ、新しい Opportunity も生まれない。**

## Shared State

会話履歴に依存しない。重要な情報は必ず `docs/` に残す。

| ファイル | 内容 | 更新するのは |
|---|---|---|
| `docs/idea.md` | 現在の最新アプリ案（常に最新1件） | Chair |
| `docs/assumptions.md` | 重要仮説と検証状況 | Chair / Evidence取り込み |
| `docs/research.md` | 市場・競合・ユーザーの一次情報 | Chair / Evidence取り込み |
| `docs/experiments.md` | 検証の計画と結果の履歴 | Chair / Evidence取り込み |
| `docs/decisions.md` | 意思決定と理由、却下した案 | Chair |
| `docs/scorecard.md` | ラウンドごとの評価 | Chair |
| `docs/versions/idea-vNNN.md` | アプリ案のバージョン履歴（不変） | Chair（v001 のみ Intake で作成） |
| `docs/handoff.md` | 開発ハーネスへの引き継ぎ（BUILD時のみ生成） | Chair |

## File Update Rules

- `docs/idea.md` は常に上書きし、**現在の案1件のみ** を保持する
- 案が大きく変わったとき（ターゲット / 問題 / 価値提案 / 収益モデル のいずれかが変わったとき）は `docs/versions/idea-vNNN.md` に新しいバージョンを作る。番号は3桁連番。**既存バージョンは絶対に書き換えない**
- 表現の微修正だけならバージョンを増やさない
- `docs/decisions.md` は追記のみ。過去の判断を消さない。却下した案は「なぜ却下したか」まで書く（同じ案の蒸し返しを防ぐため）
- `docs/experiments.md` は追記のみ。失敗した実験も消さない
- 専門 Agent（Phase 1）/ Opportunity Scout / Critic は `docs/` に書き込まない。書き込むのは Chair とメインスレッド（Evidence 取り込み時）のみ
- `docs/decisions.md` には **却下した Opportunity とその理由** も記録する。同じ提案が毎ラウンド戻ってくるのを防ぐため

## Evidence Rules

AI の推測と現実の証拠を区別する。すべての主張に Evidence Level を付ける。

| Level | 意味 | 例 |
|---|---|---|
| **L0** | AI の推測のみ | 「こういう人は困っているはず」 |
| **L1** | Web・市場・競合情報 | 競合の価格表、App Store の順位、統計記事 |
| **L2** | ユーザーの発言 | インタビュー、アンケート自由記述、レビュー本文、SNS の生の投稿 |
| **L3** | ユーザーの行動データ | LP の CVR、Waitlist 登録、プロトタイプの利用ログ |
| **L4** | 支払い・継続データ | 実際の課金、翌週継続率、解約率 |

ルール:

- Evidence Level が書かれていない主張は **L0 として扱う**
- L0 の仮説に対して「確実に需要がある」のような断定表現を使わない
- L2 以上の一次情報は、要約ではなく **実際の言葉のまま** `docs/research.md` に残す
- 何人から聞いたかを明記する（n=3 と n=30 を同じ扱いにしない）
- **作り手本人（ユーザー）の意見は Evidence ではない。L0 として扱う**
- 「言っていること」（L2）と「やっていること」（L3以上）が食い違うときは、**やっていることを信じる**

## Scoring Rules

`docs/scorecard.md` の配点（合計100点）:

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

**Evidence Cap（重要）**: 各項目の得点には、その項目を支える Evidence Level による上限がかかる。

| その項目の最高 Evidence Level | 得点上限 |
|---|---|
| L0 | 配点の 40% |
| L1 | 配点の 60% |
| L2 | 配点の 80% |
| L3 | 配点の 95% |
| L4 | 配点の 100% |

この結果、**Evidence が何もない初回ラウンドは合計40点前後が上限になる。これは正常**。
スコアが低いことは案が悪いことを意味しない。**まだ何も分かっていない** ことを意味する。

`Feasibility` のみ例外で、L0 でも上限を 80% とする（技術的実現性は AI の推定でも比較的確度が高いため）。

## Decision Rules

Chair は必ず1つを選ぶ。**総合スコアだけで決めてはならない。**

| DECISION | 条件 |
|---|---|
| **BUILD** | すべての CRITICAL 仮説が L2 以上で SUPPORTED / 最初の10人が具体的に特定できている / 収益化仮説が L2 以上 / 未解決の Critical Risk なし / MVP が個人で 4〜8週間以内 / 総合70点以上 |
| **ITERATE** | 問題の存在は L2 以上で確認できたが、案の形（ターゲット・範囲・価値提案）が弱い。案を修正して再分析する |
| **VALIDATE** | CRITICAL 仮説が L0〜L1 のまま。**初回ラウンドはほぼこれになる**。実験を設計して外部検証へ |
| **PIVOT** | 次の **いずれか**。①CRITICAL 仮説が REFUTED だが、調査の過程で別の有望な問題・ターゲットが見つかっている。②現在の案は否定されていないが、Opportunity Scout の `Radical` Opportunity / New Idea Candidate を Critic が SUPPORT し、**L2 以上の Evidence** で明らかに強いと判断できる。いずれも資産（ユーザー接点・知見）を引き継いで案を作り直す |
| **KILL** | CRITICAL 仮説が REFUTED で隣接する有望な案もない / 同じ CRITICAL 仮説の検証に2回連続で失敗 / Distribution または Monetization が構造的に成立しない |

**Critical Risk の扱い**: ある項目の得点が配点の 40% 未満なら、それは Critical Risk。
総合点が高くても Critical Risk があれば `BUILD` にしない。
例: 総合80点でも Distribution が 15点中5点なら、`BUILD` ではなく Distribution を潰す `VALIDATE` を選ぶ。

**KILL の Evidence 要件（重要）**: `KILL` は **L2 以上の Evidence がある場合にのみ** 出してよい。
AI の推測（L0）や Web 情報（L1）だけで案を殺してはならない。それはただの悲観であって判断ではない。
Evidence が L0〜L1 の段階で案が弱く見える場合の正しい判定は `KILL` ではなく `VALIDATE` である。
例外: Distribution / Monetization が **構造的に** 成立しない場合（法規制で提供できない、原価が上限価格を常に超えるなど）は L1 でも `KILL` を出してよい。

**ラウンド番号**: `docs/scorecard.md` の「推移」表の最終行 + 1 が今回のラウンド番号。
表が空なら今回が Round 1。Intake は Round 0 であり、ラウンドに数えない。

## Opportunity Adoption Rules

Opportunity Scout の提案を Chair が採用するときの制約。**機能膨張を防ぐための構造的な歯止め。**

1. **Critic が REJECT した Opportunity は採用しない。** 採用する場合は、Critic の指摘に個別に反論を書く
2. **1ラウンドで採用する `ADD` は最大1つ。** ゼロでもよい
3. **`ADD` を採用する場合、同じラウンドで `REMOVE` または `REPLACE` を1つ以上採用する。** 純増を認めない
4. `REMOVE` / `REPLACE` の採用数に上限はない。**削る方向には制限をかけない**
5. **`Radical` レベルおよび `PIVOT` タイプの Opportunity の採用には、L2 以上の Evidence が必要。**
   L0〜L1 の段階で案の根幹を作り変えると、検証されていない案を検証されていない別の案に取り替えるだけになる。
   その場合の正しい扱いは「採用」ではなく「`VALIDATE` の対象にする」
6. `Incremental` の採用は L0 でもよい（元に戻せるため）
7. 採用・不採用を問わず、**すべての Opportunity を `docs/decisions.md` に記録する**（理由付き）
8. **New Idea Candidate（別のアプリ案）の採用は `PIVOT` 判定と同義。** 5 のルールが適用され、L2 以上の Evidence が必要

## Stop Conditions

無限にアイデアをこねさせないための停止条件。

1. **新しい Evidence がないまま再分析を要求された場合**、再分析しない。前回と同じ結論になる。代わりに `docs/experiments.md` の未実行の実験を提示する
2. **同じ CRITICAL 仮説の検証に2回連続で失敗した場合**、3回目を設計しない。`PIVOT` か `KILL` を提示する
3. **3ラウンド連続で総合スコアの上昇が +5 未満、かつ最高 Evidence Level が上がっていない場合**、ループを止め、`BUILD` か `KILL` の二択をユーザーに迫る
4. **検証コストが開発コストを上回る場合**、検証せずに小さく作ることを提案してよい（実験は開発より安いときにだけ意味がある）
5. **ユーザーが検証をスキップして作ると決めた場合、止めない**。ただし `docs/decisions.md` に「検証をスキップした」ことと未検証の CRITICAL 仮説を明記してから進む。最終決定権はユーザーにある
6. **同じ Opportunity が2回 REJECT された場合、3回目を提案しない。** `docs/decisions.md` に記録し、Scout はそれを読んで再提案を避ける。復活させるには、却下後に得られた新しい Evidence の引用が必要
7. **案の根幹（Target / Core Problem / Core Action）が3ラウンド連続で変わっている場合、探索を止める。** それは改善ではなく漂流である。最も Evidence の強いバージョンに戻し、`VALIDATE` に集中する

## Handoff to Development Harness

このハーネスは実装しない。`DECISION: BUILD` が出たときのみ、Chair が `docs/handoff.md` を生成する。

引き渡し先: [agent-quartet-harness](https://github.com/Shin-sibainu/agent-quartet-harness) の `@planner`

`docs/handoff.md` の形式:

```markdown
# Handoff to Development Harness

## Product One-Liner
（1文）

## Target User
（誰か。どこにいるか）

## Core Problem
（解決する問題）

## Core Action
（ユーザーがアプリで行う中心的な1つの行動）

## MVP Scope
（作るもの。4〜8週間で終わる範囲）

## Out of Scope
（今回作らないもの。理由付き）

## Validated Facts
（Evidence Level 付き。実装判断の前提になる事実）

## Unvalidated Assumptions
（BUILD したが未検証のもの。リリース後に測る）

## Success Metric
（何がどうなれば MVP は成功と言えるか。数値で）
```

生成後、`agent-quartet-harness` の `@planner` に `docs/handoff.md` を渡すようユーザーに案内する。
**このハーネス内でアプリの実装を始めてはならない。**
