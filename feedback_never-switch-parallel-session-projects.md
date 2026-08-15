---
name: never-switch-parallel-session-projects
description: 元任务/审计 session 绝不切全局激活项目；并行 session 的项目指针归它们自己
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 3b40122c-0cbe-44d0-a950-43ecaa2ac295
---

2026-07-08 审计 session 中 route-guard 提示「激活项目被并行 session 切到 mobile-list、如非预期请 switch 回」，luca 明确纠正：**不要去别的项目，不允许随意切到并行 session 上去，这是严重问题**。

**Why:** gstack 的激活项目是全局共享软链，任何 session 执行 switch 都会踩掉并行 session 的指针（luca 实测撞过多次，07-08 已上 project-scope-guard 方案A 缓解）。route-guard 的「switch 回」提示对元任务 session 是错误建议——照做反而制造它警告的事故。

**How to apply:** 框架审计/meta 任务不依赖项目软链，对 project.sh 零调用；见到「被切走」告警时只观察记录、不执行 switch；只有用户明确要求时才动项目指针。相关：[[parallel-lucagstack-fork-merge-care]]
