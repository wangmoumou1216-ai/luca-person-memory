---
name: parallel-lucagstack-fork-merge-care
description: luca_gstack 双仓已于 2026-07-16 合并为单真值源 main + 双检出；历史 union 纪律对未来任何分支合并仍适用。
metadata: 
  node_type: memory
  type: project
  originSessionId: 92470d04-b15f-4732-9685-5cacc598e3aa
---

**现状（2026-07-16 B2 合并终裁，F6-04 闭环）：** muse fork 已 merge 回母版 main（union，muse 产品线全量保留）。现在 `main` 是唯一真值源；`~/Desktop/luca_gstack`（框架/meta + 记忆权威 store）与 `~/Desktop/项目/muse/lucagstack`（luca app 运行时）是它的两个检出。**框架改动任一检出皆可做（luca 2026-07-16 拍板，含 muse 检出内的自成长/经验修正现场直接动框架）：动手前先 pull、做完立即 commit+push、另一侧开工前 pull**；双端并发由 git 非-FF 拒绝 + tripwire 兜住——**不再是两个分叉仓**。回滚/存档锚点：`pre-merge/master-20260716`、`pre-merge/fork-muse-20260716`、`archive/muse-final-20260716`（均在远端）。风险实验纪律：分支/worktree + 备份 remote，不再开永久 fork。

**Why:** 双仓期同一修复两仓各打一遍复发两例、72h 双仓 57-59 框架 commit、parity 网人力维护（F5/F6 审计实锤）；fork 沙盒使命（muse-loop 隔离孵化，luca 本人确认动机=怕 loop 弄脏母版）已随其成熟完成。

**How to apply:** ① 见到"双仓一致/同步落双仓/fork 专属"字样的旧文档旧记忆——那是 2026-07-16 前的世界，以 CLAUDE.md「单真值源 + 双检出原则」为准。② union 纪律（保双方新增、眼生的新增当对方工作浮出来问、冲突 hunk 归属拿不准问 luca、绝不 force-push 覆盖）对**未来任何分支/实验 worktree 合并回 main** 仍然适用。③ 两检出间同步=git pull/push；落后有 tripwire（verify S23 + session-restore 软提醒）。
