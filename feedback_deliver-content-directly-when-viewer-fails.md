---
name: deliver-content-directly-when-viewer-fails
description: 用户看不到我产出的内容（外部 viewer 如 Cursor 预览空白）时，别循环排障那个工具，直接把原文贴进对话——chat 本身就是最可靠的渲染面
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a8fffd95-f1b4-43e0-8e9f-3677b6780791
---

当我产出的交付物（报告/文档）在用户的查看器里显示不出来（如 Cursor markdown 预览空白），用户的目标是「看到内容」，不是「修好那个工具」。别再给排障步骤或提议"压成短文件"——**直接把原文贴进对话**，对话窗口本身就是可靠的渲染面。

**Why:** 2026-06-30 deepresearch 出报告后，Cursor 预览空白，我连给两轮（重开+4步排障+提议压缩）才被用户打断「你直接可以让我能看到你的原文就好，我现在看不到」。我固着在修工具，而可靠的直达通道（chat）一直都在，白费两轮。

**How to apply:** 用户说「看不到/打不开/显示空白」我的产出时——第一反应是把内容**直接贴出来**，而非排查查看器。排障最多附在贴完之后当补充，不要前置、不要拿它替代直接交付。与 [[feedback_subagent-tool-visibility]]（透明度优先、直接 surface）同源；也呼应 [[feedback_no_confirmation_loops]]（执行层别来回绕，直接给结果）。
