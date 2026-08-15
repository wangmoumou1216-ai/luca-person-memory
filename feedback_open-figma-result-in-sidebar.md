---
name: open-figma-result-in-sidebar
description: 向 Figma 写入任何内容后必须主动在 luca app 侧栏浏览器打开结果；Figma 仍用跳转页中转保兜底（luca-open.sh 2026-07-24 起已支持 --url 直接推 URL）
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b3d3fee4-1e2c-4814-a115-62c924a73942
  modified: 2026-07-24T09:38:02.538Z
---

**只要向 Figma 写入了内容**（`use_figma` 建/改节点、`create_new_file`、`generate_figma_design`、figma-layer / figma-demo 的任何写入步骤），**完成后必须主动在 luca app 侧栏浏览器打开结果**，不等 luca 开口要。

**Why:** luca 2026-07-22 明确指示：「以后你记住，只要是写入 figma，写入后，你要在你的侧边栏浏览器打开结果。」
背景是我建完 6 个 Figma 帧、写完 spec 和 handoff，却只把 URL 贴在对话里；他连问「你置入到 figma 里面了吗」→「打开浏览器到侧边栏」才看到画布。**写完不看等于没交付** —— 与 [[feedback_deliver-into-daily-driver-app]]（能力必须落到他日常真在用的制品里）、CLAUDE.md 已有的「HTML 产物主动推送预览」是同一条原则的三个面。

**How to apply：** 判据只有一条——**Figma 写完，主动让他在侧栏看到，不等他问**。

**具体怎么开：指针，不在本文件展开**（按 correction-attribution L5「全局记忆只存指向修复的指针，不存已被源头修复覆盖的绕法细节」）：
→ 规则在 `CLAUDE.md`「luca app 集成」节（与「HTML 产物主动推送预览」并列）
→ 操作全文在 `.claude/skill-os/claude-md-appendix.md`「Figma 写入后主动开侧栏看结果」

**残余兜底：** `luca-open.sh --url <url>` 自 2026-07-24 起可直接推 URL 进侧栏（内部写唯一路径 meta-refresh shim 走既有预览管道）；Figma 仍走跳转页中转以保 file-key/node-id 兜底。行为若再变，回上面两处看当前做法，别照记忆里老步骤硬试。

关联 [[feedback_deliver-into-daily-driver-app]]（同源：产出必须真的到达他）、[[feedback_1to1-ui-replication-method]]。
