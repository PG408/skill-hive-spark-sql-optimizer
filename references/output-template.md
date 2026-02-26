# Output Template

Use this exact section order in every response.

## 1) SQL Classification
- Workload: `ETL` or `Analytics`
- Complexity: `simple` / `medium` / `high`
- Assumptions: list any schema/statistics assumptions

## 2) Findings

### 2.1 Correctness
- Issue:
- Impact:
- Fix direction:

### 2.2 Logic
- Issue:
- Impact:
- Fix direction:

### 2.3 Performance
- Issue:
- Impact:
- Fix direction:

### 2.4 Readability
- Issue:
- Impact:
- Fix direction:

## 3) Rewritten SQL

### 3.1 Before SQL
```sql
-- original SQL
```

### 3.2 After SQL (Main Plan)
```sql
-- optimized SQL (main)
```

### 3.3 After SQL (Backup Plan, high complexity only)
```sql
-- optimized SQL (backup)
```

## 4) Change Rationale
- Change:
- Why:
- Expected gain:
- Risk level: `low` / `medium` / `high`

## 5) Risk Gate Result
- Semantic risk score (0-5):
- Execution risk score (0-5):
- Expected gain (%):
- Gate decision: `pass` / `downgrade` / `reject`
- Why:

## 6) Validation Plan

### 6.1 Explain Plan Checks
- Scan reduction evidence:
- Shuffle/exchange reduction evidence:
- Join strategy evidence:
- Stage count comparison:

### 6.2 Result Reconciliation
- Row-count comparison:
- Key metric comparison:
- Duplicate/null behavior check:
- Boundary-partition check:

## 7) Optional Spark Environment Suggestions
- Keep as recommendations only.
- Do not write Spark configs into SQL or source code.
