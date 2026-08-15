---
name: advisor-fable-broken-upstream
description: advisor 绑 fable 在真实 session 中必挂（上游已证实 bug 三连），fable 本身可达；main=fable 时 advisor 实际全废
metadata: 
  node_type: memory
  type: reference
  originSessionId: 46fb52a9-e6b9-40fa-ba4b-3522084e6a34
---

2026-07-10 实测 + 上游核验（Claude Code 2.1.206，luca 环境）：

- **fable 可达**：Agent tool 传 `model: fable` 的 subagent 实跑 `claude-fable-5`（探针验证）；`model` 参数被 harness 认（haiku 探针验证）。
- **advisor 绑 fable 必挂**，非配置错（`advisorModel: "fable"` 语法合法、Opus 主循环+fable advisor 配对合法、版本 ≥2.1.170）。根因是上游 bug：
  - [#76199](https://github.com/anthropics/claude-code/issues/76199)：fable advisor + transcript 中**任何** tool_use → unavailable（opus advisor 免疫）——真实 session 必有工具调用，故必触发
  - [#67609](https://github.com/anthropics/claude-code/issues/67609)：fable advisor + transcript >~100K tokens → unavailable
  - [#67411](https://github.com/anthropics/claude-code/issues/67411)：一次失败即整 session 闩死（"Do not try to use it again" 是字面义，重试无用，新 session 才复位）
- **配对约束**：advisor 须 ≥ 主循环档。main=fable 时只有 fable 能当 advisor → 叠加上述 bug，**main=fable 的 session 里 advisor 实际全废**，直到上游修复。
- 可用替代：主循环 ≤ opus 时 `advisorModel: opus`（#76199 明确 opus 免疫）；或 spawn `model: fable` subagent 当决策复审官（已验证可靠）。
- 复查时机：上述 issue 关闭后重测 fable advisor。相关：[[verify-runtime-not-spec]]
