# スコアカード（Scorecard）

> 毎ラウンド同じ基準で採点し、推移を見る。更新するのは `@idea-chair`。
> **総合点だけで BUILD を判断してはならない。**

## 配点

| 項目 | 配点 | 見るもの |
|---|---|---|
| Problem Strength | 20 | 問題の頻度・深刻度・実在性 |
| Customer Clarity | 10 | ターゲットが状況で定義され、接触可能か |
| Differentiation | 15 | 既存の代替手段より明確に良い理由があるか |
| Distribution | 15 | 最初の100人を具体的に獲得できるか |
| Retention Potential | 15 | 2回目・1ヶ月後に開く理由があるか |
| Monetization | 10 | 誰がなぜいくら払うかが説明できるか |
| Feasibility | 10 | 個人が作り、運用し続けられるか |
| Evidence Quality | 5 | CRITICAL 仮説がどのレベルの証拠で支えられているか |
| **合計** | **100** | |

## 採点の手順

1. **素点** を付ける — Evidence の強さを一旦無視し、「主張されている内容がどれだけ強いか」で採点する
2. その項目を支える **最高 Evidence Level** を判定する
3. **Cap** を計算する（配点 × 下表の割合、小数は切り捨て）
4. **最終点 = min(素点, Cap)**

## Evidence Cap

各項目の得点には、その項目を支える最高 Evidence Level による上限がかかる。

| Evidence Level | 得点上限 |
|---|---|
| L0（AIの推測） | 配点の 40% |
| L1（Web・市場情報） | 配点の 60% |
| L2（ユーザーの発言） | 配点の 80% |
| L3（行動データ） | 配点の 95% |
| L4（支払い・継続データ） | 配点の 100% |

`Feasibility` のみ例外で、L0 でも上限 80%。

> **Evidence が何もない初回は合計40点前後が上限になる。これは正常。**
>
> ### スコアの読み方（最も誤解されやすい点）
>
> **このスコアは案の出来を測っていない。あなたがどれだけ確かめたかを測っている。**
>
> Evidence Cap がある以上、**Round 1 では優れた案も凡庸な案もほぼ同じ点数になる。**
> 初回の34点は「この案は34点の出来」ではなく「**まだほとんど何も確かめていない**」という意味である。
>
> スコアを上げる唯一の方法は、案を良くすることではなく、**外に出て Evidence を取ってくること**。

## Critical Risk

配点の **40% 未満** の項目は Critical Risk。
Critical Risk がある限り `BUILD` は出さない。総合点が高くても同じ。

---

## （記入例）Round N — YYYY-MM-DD

> **これは書き方の見本であり、実際の採点結果ではない。**
> `@idea-chair` は毎ラウンド、この節を複製して `## Round 1 — 2026-08-27` のように記入する。
> この見本の節自体は消さずに残しておいてよい。

| 項目 | 配点 | 素点 | Evidence | Cap | 最終点 | メモ |
|---|---|---|---|---|---|---|
| Problem Strength | 20 | | L0 | 8 | | |
| Customer Clarity | 10 | | L0 | 4 | | |
| Differentiation | 15 | | L0 | 6 | | |
| Distribution | 15 | | L0 | 6 | | |
| Retention Potential | 15 | | L0 | 6 | | |
| Monetization | 10 | | L0 | 4 | | |
| Feasibility | 10 | | L0 | 8 | | |
| Evidence Quality | 5 | | L0 | 2 | | |
| **合計** | **100** | | | | **—** | |

**Critical Risk**:
-

**DECISION**:

---

## 推移

> **この表は、実際にラウンドが終わるまで空にしておく。** 行を追加するのは `@idea-chair` のみ。
> 今回のラウンド番号は「この表の最終行 + 1」で決まるため、記入例の行を残すと番号がずれる。

| Round | 日付 | 合計 | 最高 Evidence Level | Critical Risk 数 | DECISION |
|---|---|---|---|---|---|

> 3ラウンド連続で合計の上昇が +5 未満、かつ最高 Evidence Level が上がっていない場合、
> ループを止めて `BUILD` か `KILL` の二択に進む（`CLAUDE.md` Stop Conditions）。
