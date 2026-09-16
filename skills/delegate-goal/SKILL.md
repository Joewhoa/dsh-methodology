---
name: delegate-goal
description: Use when delegating a task to a subagent, forked subagent, Ralph loop, or workflow and you need a compact goal contract, an evidence-bearing execution receipt, and explicit risk grading.
---

# Delegate Goal

当任务需要进入独立执行线程时，用紧凑、可验证的契约代替自由格式提示。

## 触发条件

委派前出现任一情况时使用本 skill：

- 任务长期运行，或会显著污染父线程上下文。
- 工作可能跨 session、日期、模型或 provider 延续。
- 工作可以拆成多个并行切片。
- 父线程上下文已经过长或噪声过多。

## 使用流程

1. 先选择满足任务所需的最低风险等级。
2. 按 `templates/goal-contract.md` 压缩目标，将完整契约传给子代理，禁止只给松散提示。
3. 明确固定基线、受保护文件、执行顺序、可独立判定的完成标准和授权边界。
4. 要求子代理按 `templates/receipt.md` 回传证据型 receipt；未知或跳过项必须显式标记。
5. 父线程逐条核验证据，不把叙述性“完成”当作验收结果。

## 风险分级

选择足够完成任务的最低等级，不机械升到最高档。

**Capability**

- `Lightweight`：机械编辑或小范围局部修改。
- `Standard`：常规功能或缺陷工作，默认值。
- `Advanced`：根因分析、安全敏感工作、迁移、并发或深度调查。

**Intensity**

- `Low`
- `Medium`：默认值。
- `High`

仅在目标模型已经明确时，才将等级映射到 `subagent`、`subagent_fork`、`ralph` 或 `workflow` 的 provider/model override；重视可移植性时保持契约与模型无关。
