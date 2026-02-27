# hive-spark-sql-optimizer

面向 Codex 新手的 Hive + Spark 3.2 SQL 优化技能。目标是让使用者在 3-5 分钟内开始稳定使用，并清晰识别 SQL 改写差异。

## 30 秒理解

该 skill 按固定流程执行 SQL 诊断与改写，决策优先级如下：
1. 正确性
2. 可解释性
3. 性能收益
4. 改写激进程度

当前版本支持 `major/minor` 改写可视化：报告中可追踪大改写，SQL 中可定位小改写注释。

## 2 分钟安装

1. 进入本地 skills 目录（如不存在先创建）

```bash
mkdir -p ~/.codex/skills
cd ~/.codex/skills
```

2. 克隆仓库

```bash
git clone https://github.com/PG408/skill-hive-spark-sql-optimizer.git hive-spark-sql-optimizer
```

3. 重新打开 Codex 会话（或刷新技能索引）后即可使用。

## 1 分钟开始用（可直接复制）

把下面模板直接发给 Codex，把 `{{...}}` 替换成实际内容：

```text
请使用 hive-spark-sql-optimizer 优化下面 SQL。

背景：
- 目标引擎：Spark 3.2
- 场景：{{ETL 或 Analytics}}
- 主要问题：{{慢/可能有逻辑风险/可读性差}}
- 已知约束：{{不能改口径/不能改输出字段/分区规则等}}

SQL：
{{在这里粘贴 SQL}}

要求：
1) 按标准模板输出诊断和重写结果
2) 请按 major/minor 改写可视化格式输出
3) 如果复杂度高，提供 Main Plan + Backup Plan
4) 给出风险门禁结论和验证步骤（EXPLAIN + 结果对账）
5) Spark 参数仅作为建议，不要写入 SQL 或代码
```

## 你会看到什么输出

简版输出默认按固定 8 段组织：
1. SQL Classification
2. Rewrite Visibility Summary
3. Major Rewrite Map（存在 major rewrite，或门禁降级/拒绝但需给出 proposal 时出现）
4. Key Findings（Correctness / Logic / Performance / Readability）
5. Rewritten SQL（包含 `Sxx` 注释）
6. Risk Gate Result
7. Validation Checklist
8. Optional Spark Environment Suggestions

详细模式会保留更细诊断，并在 `Major Rewrite Map` 中额外给出 `rollback_hint`。

## 如何读懂改写差异

1. 先看 `Rewrite Visibility Summary`
- 如果 `major rewrite count > 0`，应继续阅读 `Major Rewrite Map`。
- 如果 `major rewrite count = 0`，应检查 no-major reason 或 proposal 触发原因。

2. 再看 `Major Rewrite Map`（若存在）
- 用 `change_id`（如 `M01`）定位改写单元。
- 优先关注 `reason`、`risk`、`validation_hook`，判断是否满足业务口径与上线门槛。

3. 最后看 SQL 内小改写注释
- 通过 `-- [Sxx] 中文短句` 直接定位轻量改动。
- 每条注释对应一个小改写单元，可与验证步骤一一对应。

4. 执行最小对账
- 按 `validation_hook` 先做 explain 对比，再做关键指标与边界分区对账。

## 常见错误

1. 仅说“优化一下”但未给业务约束
- 结果容易出现语义风险，至少应补充“是否允许口径变化、是否允许近似计算”。

2. 不提供 SQL 上下文
- 建议提供表粒度、分区字段、主键/去重键、时间范围。

3. 直接把 Spark 参数写进 SQL
- 不应这样做。Spark 参数（如 `spark.driver.memory`）应通过公司内部 UI 手动配置。

## 最佳实践

1. 先请求保守优化版本并完成对账，再请求激进版本。
2. 对高复杂 SQL，要求同时给 Main Plan 与 Backup Plan。
3. 上线前执行输出中的验证计划：`EXPLAIN` 对比 + 结果对账。
