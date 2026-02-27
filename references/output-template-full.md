# Output Template (Detailed Mode)

Use this exact section order in detailed mode.

Detailed-mode guardrails:
- Keep each bullet focused and non-redundant.
- Keep SQL concise even in detailed mode.
- Keep optional Spark suggestions to at most two items.

## 1) SQL Classification
- Workload: `ETL` or `Analytics`
- Complexity: `simple` / `medium` / `high`
- Assumptions: list schema/statistics assumptions

## 2) Rewrite Visibility Summary
- Major rewrite count:
- Minor rewrite count:
- Major trigger basis:
- Major map mode: `applied` / `proposal` / `none`
- No-major reason (required when major count is 0):

## 3) Major Rewrite Map (show when major count > 0, or when gate is downgraded/rejected with major proposal)

| change_id | change_type | location | before | after | reason | expected_gain | risk | validation_hook | rollback_hint |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| M01 | | | | | | | | | |

## 4) Findings

### 4.1 Correctness
- Issue:
- Impact:
- Fix direction:

### 4.2 Logic
- Issue:
- Impact:
- Fix direction:

### 4.3 Performance
- Issue:
- Impact:
- Fix direction:

### 4.4 Readability
- Issue:
- Impact:
- Fix direction:

## 5) Rewritten SQL

### 5.1 After SQL (Main Plan)
```sql
-- [S01] 中文短句：改动点 + 目的
-- optimized SQL (main)
```

### 5.2 After SQL (Backup Plan, high complexity only)
```sql
-- [S01] 中文短句：改动点 + 目的
-- optimized SQL (backup)
```

## 6) Risk Gate Result
- Semantic risk score (0-5):
- Execution risk score (0-5):
- Expected gain (%):
- Gate decision: `pass` / `downgrade` / `reject`
- Why:

## 7) Validation Plan

### 7.1 Explain Plan Checks
- Scan reduction evidence:
- Shuffle/exchange reduction evidence:
- Join strategy evidence:
- Stage count comparison:

### 7.2 Result Reconciliation
- Row-count comparison:
- Key metric comparison:
- Duplicate/null behavior check:
- Boundary-partition check:

## 8) Optional Spark Environment Suggestions
- Keep as recommendations only.
- Do not write Spark configs into SQL or source code.
