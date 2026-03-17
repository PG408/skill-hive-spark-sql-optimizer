# Concise v2 (Four-Section Template)

Use this exact section order in concise mode.
Section order: `TL;DR` first, then the four core sections.

Conciseness guardrails:
- Keep each bullet to one sentence when possible.
- Keep SQL concise and annotate all localizable material rewrites (`Mxx`/`Sxx`).
- Keep optional Spark suggestions to at most two items, and place them under impact only when needed.

## TL;DR
- 主要改动：
- 核心收益：
- 风险结论：

## 1) 修改后的代码

### 1.1 Main Plan SQL
```sql
-- [M01] 中文短句：结构性改动 + 目的
-- [S01] 中文短句：局部改动 + 目的
-- optimized SQL (main)
```

### 1.2 Backup Plan SQL (high complexity only)
```sql
-- [M02] 中文短句：结构性改动 + 目的
-- [S02] 中文短句：局部改动 + 目的
-- optimized SQL (backup)
```

Rendering rules:
- For `simple` or `medium` complexity, output `1.1` only.
- For `high` complexity, output both `1.1` and `1.2`.
- For all localizable rewrites, place `Mxx`/`Sxx` comments at the nearest changed SQL location.
- For non-localizable rewrites, place a header-level summary comment in SQL and explain scope in section `2) 改了什么`.

## 2) 改了什么

Required fields:
- `change_id`: every material rewrite must include `Mxx` or `Sxx`.
- `location`: expression/predicate/window/join/aggregation/CTE responsibility.
- `before -> after`: describe only changed logic.
- `sql_marker_status`: `inline` / `header` / `non-localizable`.
- If `sql_marker_status = non-localizable`, include one-sentence reason and coverage scope.

Recommended compact table:

| change_id | change_type | location | before | after | sql_marker_status |
| --- | --- | --- | --- | --- | --- |
| M01 | major | | | | inline |
| S01 | minor | | | | inline |

## 3) 为什么改

For each `change_id`, provide:
- Motivation category: correctness / logic / performance / readability.
- Intent type: `口径修复` or `纯性能优化` (or both when applicable).
- One-sentence reason with expected technical benefit.

## 4) 有什么影响

Must include:
- Risk-gate summary: semantic risk (0-5), execution risk (0-5), expected gain (%), gate decision (`pass` / `downgrade` / `reject`).
- Validation checklist with at least three checks:
  - Explain plan check
  - Key metric reconciliation check
  - Boundary/date partition check

Optional additions:
- Rollback guidance for medium/high-risk rewrites.
- Spark environment suggestions (recommendation only, never written into SQL/code).
