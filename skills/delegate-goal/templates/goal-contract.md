# Goal 契约模板

委派前填写此模板，并将完整契约传给子代理。

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

## 填写规则

- `Goal` 必须是一句话，并描述 ticket 级结果。
- `Current state` 必须固定评审基线，并保护无关脏文件。
- `Execution order` 必须是尊重依赖关系的最短路径。
- `Completion criteria` 每条都必须可独立判定，禁止使用“看起来没问题”等模糊表述。
- `Constraints` 是硬授权边界；子代理不得自行扩权。
- `Context` 只提供执行所需的最少来源材料。
