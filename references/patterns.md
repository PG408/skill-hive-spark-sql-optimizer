# Rewrite Patterns for Hive + Spark 3.2

Use these patterns when they pass correctness and risk-gating rules.

## Pattern Matrix

| Anti-Pattern | Rewrite Pattern | Expected Benefit | Typical Risk |
| --- | --- | --- | --- |
| Late filtering after wide joins | Push selective filters into source CTEs before joins | Lower scan and shuffle volume | Low |
| Joining detail tables before aggregation | Pre-aggregate high-cardinality side, then join | Smaller join inputs | Medium |
| Repeated subqueries with same logic | Extract shared CTE and reuse | Better maintainability and fewer repeated scans | Low |
| `UNION` without business need for dedup | Replace with `UNION ALL` | Remove expensive dedup shuffle | Medium |
| `SELECT *` from wide tables | Project only required columns | Reduced I/O and memory | Low |
| Function on partition column in predicate | Rewrite predicate to keep partition pruning | Partition skip and faster scans | Low |
| Single-path join on skewed key | Split hotspot keys or use salting strategy | Reduce skewed tasks and tail latency | High |
| Heavy dedup done multiple times | Consolidate to one deterministic dedup step | Fewer shuffles and simpler plan | Medium |
| Unnecessary global `ORDER BY` in intermediate steps | Remove intermediate global sort | Eliminate expensive sort stages | Low |

## High-Complexity Dual-Plan Strategy

When complexity is high, return:
1. Main plan: maximize gain under medium-constraint gate.
2. Backup plan: lower-risk alternative with smaller semantic footprint.

Use a different optimization path in backup plan, for example:
- Main plan uses pre-aggregation + join reorder.
- Backup plan keeps join order but pushes filters and prunes columns.

## Safe Rewrite Heuristics

1. Keep business filters and join constraints explicit and auditable.
2. Preserve output schema and column semantics unless user requests changes.
3. If null or duplicate behavior may shift, mark risk and provide reconciliation checks.
4. Prefer deterministic dedup keys and ordering.
5. Avoid hint overuse; explain expected impact for every hint.

## Validation Hooks Per Pattern

For each applied pattern, attach at least one concrete check:
- Row-count delta by partition/date window.
- Key metric aggregates before vs after (sum, count distinct, etc.).
- Duplicate-rate checks on primary business keys.
- `EXPLAIN` evidence: scan nodes, exchange/shuffle nodes, join strategies, stage count.
