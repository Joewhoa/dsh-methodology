# Receipt 回传模板

子代理必须返回具体执行回执，不能只用叙述性“完成”消息。

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

## 回传要求

- 证据必须具体：命令、计数、标识符、文件路径、链接或其他可直接核验的产物。
- 未实际运行的环境、部署或真实服务不得声称已验证。
- 回传前移除凭据、token 和敏感标识符。
- 未知或跳过项必须显式标记，不得暗示已经完成。
