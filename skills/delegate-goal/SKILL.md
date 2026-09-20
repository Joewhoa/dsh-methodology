---
name: delegate-goal
description: Use when delegating a task to a subagent, forked subagent, Ralph loop, or workflow and you need a compact goal contract, an evidence-bearing execution receipt, and explicit risk grading.
---

> 来源：本技能的「Goal 契约 + 证据回执 + 风险分级」模型借鉴 [tt-a1i/matt-skills-with-to-goal](https://github.com/tt-a1i/matt-skills-with-to-goal) 的 `to-goal` / `goal-crafter` / `spec-executor`，并按 DSH 的 subagent / subagent_fork / ralph / workflow 机制重新表述与落地；实现为独立撰写。

# Delegate Goal

当任务需要进入独立执行线程时，用紧凑、可验证的契约代替自由格式提示。

## 触发条件

委派前出现任一情况时使用本 skill：

- 任务长期运行，或会显著污染父线程上下文。
- 工作可能跨 session、日期、模型或 provider 延续。（这里的「跨 session」指委派的任务在独立线程/另一会话中执行，不指本会话的记忆延续——记忆延续靠「交接总控.md」文件。）
- 工作可以拆成多个并行切片。
- 父线程上下文已经过长或噪声过多。

## 使用流程

1. 先按「风险分级」从两个维度定档（各取最低够用的一档）。
2. 按 `templates/goal-contract.md` 压缩目标，将完整契约传给子代理，禁止只给松散提示。
3. 明确固定基线、受保护文件、执行顺序、可独立判定的完成标准和授权边界。
4. 要求子代理按 `templates/receipt.md` 回传证据型 receipt；未知或跳过项必须显式标记。
5. 父线程逐条核验证据，不把叙述性“完成”当作验收结果。

## 风险分级

委派前从两个**独立**维度定级，再决定执行能力。两维分开看，不要混成一个「难度」。

**维度 1 · 认知难度（Capability）——任务本身有多难**

- `Lightweight`：机械编辑或小范围局部修改。
- `Standard`：常规功能或缺陷工作，默认值。
- `Advanced`：根因分析、安全敏感、迁移、并发或深度调查。

**维度 2 · 后果严重度（Consequence）——改错了代价多大**

- `Low`：只影响临时/本地产物，出错易回滚。
- `Medium`：默认值；影响本仓库代码或文档，有 git 历史可回退。
- `High`：涉及真实服务、生产数据、密钥、跨仓库或不可逆操作，出错难回滚。

**定档规则**

1. 两维各取**最低够用**的一档，不机械升到最高档。
2. 后果严重度 ≥ 难度时，以后果为准（哪怕机械编辑，碰生产数据也是 High）。
3. 任一维度为 `High`，父线程必须逐条核验 receipt，不采信叙述性「完成」。

**映射到执行能力（可选）**

仅在目标模型明确时，才把「难度 × 后果」组合映射到 `subagent`、`subagent_fork`、`ralph` 或 `workflow` 的 provider/model override（例如 `Advanced × High` → 更强模型 + 更严验收）；重视可移植性时保持契约与模型无关，只写两个维度的档位。
