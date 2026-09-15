# 子 agent 委托纪律（delegate-goal）—— 执行契约

> 本文件是「规划线程」产出的**执行契约**。目标：在一个新开的干净会话里，把它落成一个 DSH skill（`SKILL.md`）。
>
> 来源：借鉴 [tt-a1i/matt-skills-with-to-goal](https://github.com/tt-a1i/matt-skills-with-to-goal) 的 `to-goal` / `spec-executor` 思路，适配到 DSH 的 subagent / subagent_fork / ralph / workflow 机制。

---

## 一、要做什么

创建一个 DSH skill，暂名 `delegate-goal`（或 `agent-delegation`），指导 agent 在**把任务交给子 agent 执行**时遵循结构化契约流程，目的：

1. **减少父 agent 上下文占用**——把规划结果压成可粘贴契约，执行线程不继承父对话。
2. **让结果可验证**——子 agent 回传带证据的 receipt，而不是"做完了"。

## 二、Skill 必须包含的四部分

### 1. 触发条件（何时加载本 skill）

委托子 agent 执行任务，且满足其一：

- 需要隔离上下文（长任务、避免污染父对话）；
- 跨会话 / 跨天 / 跨模型；
- 多切片并行；
- 当前父上下文太脏或太长。

### 2. Goal 契约模板（委托前，父 agent 把任务压成这个）

交付给子 agent 的不是自由 prompt，而是这份结构化契约：

```text
## Goal
<一句话、ticket 级的结果>

## Current state
- 分支 / 基线 HEAD（review fixed point）:
- 脏文件 / 未跟踪文件（要保护，勿动）:
- 已完成的证据:
- 已知缺口:
- 现有失败:

## Execution order
<最短、尊重依赖顺序的执行路径>

## Completion criteria
- [ ] <每条完成标准，必须可独立判定 done/not done，禁用 "looks good">
- [ ] 跑过最小可用验证: <命令>
- [ ] 对照基线跑过评审
- [ ] 仅在全部标准通过 + 用户/上下文授权时提交

## Constraints
- 不 push / 不开 PR / 不 merge / 不改 tracker 状态
- 不碰无关的脏文件或未跟踪文件
- 不提前实现下游 ticket
- 用预定 test seam，不测实现细节

## Context
- 来源 spec / ticket:
- 设计文档:
- 验证入口 / 先看哪些文件:
```

### 3. Receipt 回传模板（子 agent 执行完回传）

```text
EXECUTION RECEIPT
- Conclusion: completed / partially completed / blocked
- 基线 fixed point:
- Acceptance criteria: <逐条 pass/fail + 证据>
- 主要改动 / 改动文件:
- 验证结果:
- 评审发现:
- 未验证 / 未执行项:
- 风险与剩余工作:
- 需要规划线程决策的项:
- 外部副作用（push/部署/数据/真实服务）:
- 最终工作树状态:
```

要求：证据具体（命令、计数、标识、链接），不声称未验证的真实环境/部署；回传前抹掉凭据/token/敏感标识。

### 4. 风险分级（委托时定档，映射到 DSH 执行能力）

- **Capability**: `Lightweight`（机械编辑/小改）| `Standard`（常规功能/bug，默认）| `Advanced`（根因分析/安全/迁移/并发）
- **Intensity**: `Low` | `Medium`（默认）| `High`
- 选**最低够用**的档，不机械上最高档；档位映射到 DSH 的 subagent/workflow `provider`/`model` 覆盖（仅当目标模型已知时点名，否则保持可移植）。

## 三、验收标准（skill 是否做好）

1. skill 能被 DSH 的 `tool-skill` 加载（目录 + `SKILL.md` + frontmatter 正确）。
2. 四部分齐全（触发条件 / Goal 模板 / Receipt 模板 / 风险分级）。
3. 用一份**真实委托**跑通一次：父 agent 产出 Goal → 子 agent（subagent 或 ralph）执行 → 回传 receipt 且证据可验证。

## 四、放置位置（执行时确认）

优先放**用户 skill root**（DSH 的 skill-filesystem 本地发现目录），而不是 shipped preset 的 skills 目录（升级会覆盖）。执行时先用 `cordis_inspect` 或 roster 确认用户 skill 根目录的确切路径，再落盘。

## 五、边界

- 契约（Goal）只读规划产物，不改 tracker、不建分支、不实现。
- 授权是显式的：Goal 的 Constraints 里未授权的 push/PR/merge/外部调用，执行 agent 一律不做。
- `handoff` 不是每次都要：只要上下文已进契约，新线程直接读契约，不重新访谈。
