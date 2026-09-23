# DSH 工作方法论（说明书）

[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

一套在 **DeepSeek Harness（DSH）** 上协作的工程方法论：怎么委派、怎么回写、怎么用技能、怎么避坑、怎么持续进化。

> 一套**开源**的方法论与技能集，`git clone` 即可部署复用。

## 一、解决什么问题

多个 Agent、多个会话之间，怎么**不丢上下文**、怎么**分工**、怎么**验收**、怎么**持续累积**。

核心结论（借鉴 [Cursor Projects](https://cursor.com/blog/projects) 的功能约定，并由本仓库实测印证）：

> **文件才是跨会话记忆，进程内状态不是。** 跨会话连续性靠「交接总控.md」这个文件，不靠进程内的 goal。

## 二、核心纪律（4 条）

1. **委托纪律**（delegate-goal）：委派给子 agent 时，写「Goal 契约 + 硬约束 + 可判定完成标准」，要求「证据型 Receipt」，父线程逐条核验、不采信叙述性"完成"。
2. **回写纪律**：任何补丁/变更做完，必须回写「交接总控.md」的「最近进展」，否则视为未完成。
3. **协调者纯度**：主 agent 只做「定目标 / 拆解 / 委派 / 验收 / 抓假报告」；是否委派看一条判据——执行上下文能否完整写进 Goal 契约，能就委派、不能就自己做。
4. **目标驱动**：长期/跨步骤目标用 goal 工具挂起（**会话内有效**），短任务不必挂。

## 三、6 个技能

| 技能 | 用途 | 调用方式 |
|---|---|---|
| `delegate-goal` | 委托纪律（契约 + 回执 + 风险分级） | 自动 |
| `code-acceptance` | 验收纪律（6 条检查 + 5 栏汇报，防「假全绿」） | 自动 |
| `grill-me` | 面试 / 方案高强度追问 | 手动 |
| `tdd` | 测试优先开发 / 修 bug | 自动 |
| `cordis-plugin-development` | 写 / 改动态 Cordis 插件 | 自动 |
| `editing-cordis-compositions` | 编辑 agent 预设 / Cordis 组合 | 自动 |

> `delegate-goal` 与 `code-acceptance` 是一对：**前者管「怎么把活交出去」，后者管「交回来的活怎么验」。**
> 两者都是第 1 条「委托纪律」的技能化落地（委派端 + 验收端）。

## 四、进化路线（参考 Cursor 的 Projects 功能）

目标：从「响应式 + 手动回写 + 半吊子协调」→「目标驱动 + 上下文自动累积 + 纯协调」。

| 阶段 | 状态 |
|---|---|
| 1 回写机制化 | ✅ 完成 |
| 2 Goal mode | ⚠️ 实测修正：goal 是**会话作用域**，跨会话 resume 不自动发生；跨会话记忆靠文件 |
| 3 协调者纯度 | 进行中（行为项） |
| 4 上下文结构化 | 暂缓（规模不够再拆） |
| 5 订阅 / 主动 | 暂缓 |

## 五、怎么部署到新机器

1. 克隆仓库：`git clone https://github.com/Joewhoa/dsh-methodology.git`。
2. 把 `skills/` 下 6 个文件夹复制到 `~/.dsh/skills/`（Linux/macOS；Windows 为 `C:\Users\<你>\.dsh\skills\`）。
3. 让新机器的 AI 读 `START_HERE.md`（零上下文 AI 也能照做）。
4. 把 `方法论版-交接总控.md` 里的 `<...>` 占位改成实际路径 / 项目。

## 六、仓库内容说明

- `README.md` —— 本说明书。
- `skills/` —— 6 个技能（delegate-goal / code-acceptance / grill-me / tdd / cordis-plugin-development / editing-cordis-compositions），复制到 `~/.dsh/skills/` 即生效。
- `START_HERE.md` —— 零上下文启动提示词，新机器 AI 照做即可。
- `方法论版-交接总控.md` —— 方法论单一真相源（通用模板，`<...>` 处按实际填写）。
- `delegate-goal-契约.md` —— 委托纪律的 Goal 契约 / 回执 / 风险分级模板。
- `LICENSE` —— MIT 协议。
- `THIRD_PARTY_NOTICES.md` —— 第三方来源与许可声明。

## 七、来源与致谢

- `skills/tdd/` 与 `skills/grill-me/` 是 [mattpocock/skills](https://github.com/mattpocock/skills)（MIT，Copyright (c) 2026 Matt Pocock）的中文译本，含少量 DSH 环境适配。
- `skills/delegate-goal/` 的「Goal 契约 + 证据回执 + 风险分级」模型借鉴 [tt-a1i/matt-skills-with-to-goal](https://github.com/tt-a1i/matt-skills-with-to-goal)（MIT），按 DSH 机制重新落地。
- `skills/code-acceptance/` 为**本项目原创**（未借鉴外部实现），依据本仓库第 1 条「委托纪律」的验收端撰写。
- 详细对照见 `THIRD_PARTY_NOTICES.md`。
