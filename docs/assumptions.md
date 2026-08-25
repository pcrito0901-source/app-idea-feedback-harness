# 重要仮説（Assumptions）

> 「これが偽なら案が成立しない」仮説を管理する。
> `@idea-chair` と Evidence 取り込み時に更新する。**否定された仮説も消さない。**

## 用語

**Importance**

| 値 | 意味 |
|---|---|
| `CRITICAL` | 偽なら案そのものが成立しない。同時に3つ以内が望ましい |
| `HIGH` | 偽なら大幅な方針転換が必要 |
| `MEDIUM` | 偽でも案は生き残る |

**Status**

| 値 | 意味 |
|---|---|
| `UNTESTED` | 未検証 |
| `TESTING` | 検証中（実験が PLANNED / RUNNING） |
| `SUPPORTED` | 検証され、支持された |
| `REFUTED` | 検証され、否定された |
| `PARTIAL` | 一部の条件でのみ支持された |

**Evidence Level**: L0 = AIの推測 / L1 = Web・市場情報 / L2 = ユーザーの発言 / L3 = 行動データ / L4 = 支払い・継続データ

---

## 一覧

| ID | 仮説 | Importance | Evidence | Confidence | Status | 関連実験 |
|---|---|---|---|---|---|---|
| A1 | | CRITICAL | L0 | 20% | UNTESTED | — |
| A2 | | | | | | |
| A3 | | | | | | |

---

## 詳細

### A1: （仮説を一文で）

- **Importance**: CRITICAL
- **偽ならどうなるか**: （案がどう壊れるか）
- **Evidence**: L0 — （根拠。無ければ「AIの推測のみ」）
- **Confidence**: 20%
- **Status**: UNTESTED
- **検証方法**: （EXP-NNN と対応させる）
- **更新履歴**:
  - YYYY-MM-DD: 作成（Round 1）

---

## REFUTED になった仮説（消さない）

> 否定された仮説は資産である。同じ仮説を無自覚に復活させないために残す。

| ID | 仮説 | 否定された根拠 | 日付 |
|---|---|---|---|
