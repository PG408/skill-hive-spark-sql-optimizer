# Output Template (Concise Default)

Use this exact section order in concise mode.

Conciseness guardrails:
- Keep each bullet to one sentence when possible.
- Provide one key point per findings category by default.
- Keep SQL code concise and remove dead columns/expressions.
- Keep optional Spark suggestions to at most two items.

## 1) SQL Classification
- Workload: `ETL` or `Analytics`
- Complexity: `simple` / `medium` / `high`
- Assumptions: list only critical schema/statistics assumptions

## 2) Key Findings
- Correctness: `issue + fix direction`
- Logic: `issue + fix direction`
- Performance: `issue + fix direction`
- Readability: `issue + fix direction`

## 3) Rewritten SQL

### 3.1 After SQL (Main Plan)
```sql
-- optimized SQL (main)
```

### 3.2 After SQL (Backup Plan, high complexity only)
```sql
-- optimized SQL (backup)
```

## 4) Risk Gate Result
- Semantic risk score (0-5):
- Execution risk score (0-5):
- Expected gain (%):
- Gate decision: `pass` / `downgrade` / `reject`
- Why:

## 5) Validation Checklist
- Explain plan check:
- Key metric reconciliation check:
- Boundary/date partition check:

## 6) Optional Spark Environment Suggestions
- Keep as recommendations only.
- Do not write Spark configs into SQL or source code.
