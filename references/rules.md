# Hive + Spark 3.2 SQL Rules

## Table of Contents
- Company Rules and Conventions
- Correctness Rules
- Logic Rules
- Performance Rules
- Readability Rules
- Rewrite Visibility Rules
- Risk-Gating Rules

## Company Rules and Conventions

1. `${DATE}` is a date string in `YYYY-MM-DD` format.
2. `${date}` is a date string in `YYYYMMDD` format.
3. Table partitions use `${date}` format, while `activation_date` uses `${DATE}` format.

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
7. Prefer `IF` for simple binary branching; use `CASE WHEN` for multi-branch logic or when it is materially clearer.
8. Keep CTE count minimal; avoid single-use pass-through CTEs without semantic value.
9. Remove unused columns and redundant projections as early as possible.
10. Use `AS` when aliasing tables or columns is needed for clarity or disambiguation.
11. Prefer shorthand type conversion like `int(expr)` and `string(expr)` when supported; use `CAST` only if shorthand is unavailable or harms clarity.

## Rewrite Visibility Rules

1. Extract rewrite units at expression/predicate/window/join/CTE-responsibility granularity.
2. Assign unique identifiers to all units:
- Major rewrites use `M01`, `M02`, ...
- Minor rewrites use `S01`, `S02`, ...
3. Classify rewrite units in strict order:
   - If structural rewrite exists (join/aggregation/window/union/dedup/CTE responsibility re-layout), classify as major.
   - Else if semantic risk score is `>= 2`, classify as major.
   - Else if expected gain is `>= 20%`, classify as major.
   - Else classify as minor.
4. Keep report and SQL traceability aligned:
- Any major rewrite must appear in `Major Rewrite Map`.
- Any minor rewrite must be commented in SQL with `-- [Sxx] <中文短句：改动点 + 目的>`.
5. Minor SQL comments must be one-sentence, pre-change comments and should explain business/execution intent instead of obvious syntax actions.
6. If no major rewrite exists and no proposal-level major plan is needed, omit `Major Rewrite Map` and explicitly explain the no-major reason in `Rewrite Visibility Summary`.
7. If risk gate downgrades or rejects aggressive rewrites, still provide a proposal-level major map draft to avoid hidden rewrite intent.
8. Do not fabricate rewrites:
- If no material rewrite was applied, use explicit downgrade language and keep change sections minimal.
- Never create fake `change_id` entries for unchanged logic.

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
