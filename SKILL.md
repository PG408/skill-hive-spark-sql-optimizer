---
name: hive-spark-sql-optimizer
description: Optimize and rewrite Hive + Spark 3.2 SQL with correctness checks, logic refinement, performance tuning, and readability improvements. Use when asked to review or optimize SQL (for example, "optimize this SQL", "优化这个 SQL"), especially for ETL pipelines, analytics queries, slow jobs, correctness risks, or when rewritten SQL with risk/benefit analysis is required.
---

# Hive Spark SQL Optimizer

## Overview

Follow a staged workflow to diagnose and rewrite SQL while balancing quality and performance.
Prioritize decisions in this order: correctness, explainability, performance gain, rewrite scope.

## Load References

Read only what is needed:
- Use `references/rules.md` for baseline rules and gating.
- Use `references/patterns.md` for anti-pattern rewrites.
- Use `references/output-template.md` for the default concise report format.
- Use `references/output-template-full.md` only when the user explicitly requests a detailed report.

## Workflow

1. Normalize SQL formatting without changing semantics.
2. Classify the query as ETL or Analytics.
3. Score complexity as simple, medium, or high.
4. Run diagnostics in this order: correctness, logic, performance, readability.
5. Generate a main rewrite candidate.
6. Generate one backup candidate when complexity is high.
7. Apply risk/benefit gate before allowing medium/high-risk changes.
8. Route output mode and render results with the corresponding template.

## Output Mode Routing

Default mode:
- Always use concise mode unless the user explicitly requests detailed output.
- Concise mode must use `references/output-template.md`.

Detailed mode triggers:
- Use detailed mode only when the user clearly asks for "detailed", "full", "verbose", "复杂版", "详细版", or equivalent wording.
- Detailed mode must use `references/output-template-full.md`.

Conflict handling:
- If user intent is ambiguous, keep concise mode by default.
- If user explicitly asks for both speed and full detail, prioritize user-stated detail requirement.

## Complexity Rules

Mark SQL as `high` when any condition is true:
- Join count is 4 or more.
- Nested subquery depth is 3 or more.
- SQL contains multi-branch `UNION` or `UNION ALL`.
- SQL couples window functions with deduplication or heavy aggregation.
- SQL shows a clear data-skew risk (hot keys or extreme key imbalance).

When SQL is high complexity, return:
- One main rewrite plan.
- One backup rewrite plan with a different optimization path.

## Risk and Benefit Gate

Use medium-constraint semantics protection:
- Allow medium/high-risk rewrites only when expected benefit is at least 20%.
- Downgrade to conservative edits when expected benefit is below 20%.
- If semantics uncertainty remains high, provide suggestion-only output and explain why.

Use this decision priority:
1. Correctness first.
2. Explainability second.
3. Performance gain third.
4. Rewrite aggressiveness fourth.

## Hard Constraints (Summary)

- Keep correctness as the first priority in all rewrite decisions.
- Apply the risk gate defined in `## Risk and Benefit Gate`; keep conservative or suggestion-only output when the gate is not met.
- Never write Spark configuration into SQL or code; provide Spark tuning as optional environment recommendations only.
- Always use concise output by default with `references/output-template.md`.
- Switch to `references/output-template-full.md` only when the user explicitly requests detailed output.
- For detailed rewrite rules (correctness, logic, performance, readability), follow `references/rules.md` as the single source of truth.
- For anti-pattern conversion strategies, follow `references/patterns.md`.

## Maintenance Convention

- `SKILL.md` owns workflow, complexity routing, and hard-constraint summaries only.
- `references/rules.md` owns detailed rewrite conventions and style/performance rules.
- `references/patterns.md` owns anti-pattern rewrite strategies.
- When detailed rule text changes, update reference files first and keep `SKILL.md` summary-level to avoid duplication drift.

## Trigger Examples

- "Optimize this SQL."
- "优化这个 SQL。"
- "Review this ETL SQL and rewrite it for Spark 3.2 performance."
- "Find logic/correctness issues in this query and provide an optimized version."
- "This join-heavy SQL is slow, give me a main plan and a backup plan."
