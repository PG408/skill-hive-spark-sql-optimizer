# hive-spark-sql-optimizer

面向 Codex 新手的 Hive + Spark 3.2 SQL 优化技能。目标是让你在 3-5 分钟内开始稳定使用。

## 30 秒理解

这个 skill 会按固定流程做 SQL 诊断与改写，优先级是：
1. 正确性
2. 可解释性
3. 性能收益
4. 改写激进程度

它会输出结构化结果，包括问题分类、改写 SQL、风险分级、验证方案。

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

把下面模板直接发给 Codex，把 `{{...}}` 替换成你的内容：

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
2) 给出 Before SQL 和 After SQL
3) 如果复杂度高，提供 Main Plan + Backup Plan
4) 给出风险门禁结论和验证步骤（EXPLAIN + 结果对账）
5) Spark 参数仅作为建议，不要写入 SQL 或代码
```

## 你会看到什么输出

该 skill 会按固定 7 段输出：
1. SQL Classification
2. Findings（Correctness / Logic / Performance / Readability）
3. Rewritten SQL（Before / After）
4. Change Rationale
5. Risk Gate Result
6. Validation Plan
7. Optional Spark Environment Suggestions

## 常见错误

1. 只说“优化一下”但不给业务约束
- 结果容易出现语义风险。至少补充“是否允许口径变化、是否允许近似计算”。

2. 不提供 SQL 上下文
- 建议提供表粒度、分区字段、主键/去重键、时间范围。

3. 直接把 Spark 参数写进 SQL
- 不要这样做。Spark 参数（如 `spark.driver.memory`）应通过公司内部 UI 手动配置。

## 最佳实践

1. 先让 skill 做一次保守优化，确认正确性后再要激进版本。
2. 对高复杂 SQL，要求同时给 Main Plan 与 Backup Plan。
3. 上线前按输出里的验证计划执行：`EXPLAIN` 对比 + 结果对账。
