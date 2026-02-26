# Hive + Spark 3.2 SQL Rules

## Table of Contents
- Correctness Rules
- Logic Rules
- Performance Rules
- Readability Rules
- Risk-Gating Rules

## Correctness Rules

1. Preserve row-grain semantics unless user explicitly accepts behavior changes.
2. Verify join keys for completeness and cardinality assumptions.
3. Keep join predicates and filter predicates explicit and separate.
4. Check null semantics for `NOT IN`, `COUNT(col)`, equality, and anti-joins.
5. Keep group-by keys aligned with all non-aggregated selected columns.
6. Keep window ordering deterministic when deduplicating (define stable tie-breakers).
7. Avoid implicit type coercion in critical predicates and join keys.
8. Keep date/time operations explicit about boundaries and timezone assumptions.
9. Mark assumptions whenever source schema details are unknown.

## Logic Rules

1. Remove redundant layers (nested projections, repeated subqueries, duplicate CTEs).
2. Push selective filters as early as possible when semantics are unchanged.
3. Pre-aggregate before large joins when keys and grain allow safe reduction.
4. Replace repetitive expressions with named CTE blocks.
5. Remove unnecessary `DISTINCT` when upstream uniqueness is guaranteed.
6. Prefer `UNION ALL` unless deduplication is required by business logic.
7. Isolate each transformation step to one responsibility per CTE.

## Performance Rules

1. Keep partition filters sargable; avoid wrapping partition columns in functions.
2. Minimize scan width by selecting only required columns.
3. Reduce shuffle by filtering and projecting before wide joins.
4. Use broadcast strategy only when small-side size is safely bounded.
5. Reorder joins to reduce intermediate explosion when semantics allow.
6. Detect skew risks and propose skew-aware rewrites (salting/splitting hotspots).
7. Remove unnecessary global sort operations.
8. Prefer incremental dedup patterns to repeated full-table dedup passes.
9. Use approximate aggregations only when acceptable for business semantics.

## Readability Rules

1. Keep SQL keywords uppercase and identifiers lowercase snake_case.
2. Write one selected column per line.
3. Avoid `SELECT *` in production rewrites.
4. Use semantic aliases (for example, `fact_orders`, `dim_user`) instead of `a`, `b`, `c`.
5. Keep CTE order aligned with transformation flow.
6. Keep comments concise and only for non-obvious logic.

## Risk-Gating Rules

Use three dimensions:
- Semantic risk (0-5): chance of result change.
- Execution risk (0-5): chance of unstable runtime/resource profile.
- Expected gain (%): estimated runtime or cost improvement.

Decision policy:
1. If semantic risk >= 4 and expected gain < 20%, reject aggressive rewrite.
2. If semantic risk is 2-3 and expected gain >= 20%, allow rewrite with explicit `risk: medium/high`.
3. If semantic risk <= 1, prefer acceptance when correctness checks pass.
4. If confidence is low due to missing metadata, downgrade to suggestion-only.

Mandatory output:
- Explain why each risky change passed or failed the gate.
- Include clear rollback guidance for medium/high-risk rewrites.
- Keep Spark config advice in recommendation text only, never in SQL/code edits.
