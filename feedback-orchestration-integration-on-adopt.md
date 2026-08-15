---
name: feedback-orchestration-integration-on-adopt
description: "新增能力（skill/机制/框架件）必须同时深度规划进使用场景与 workflow——\"不能新增了就完了\""
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a84caa16-23a0-4f8a-9112-f5f043226c16
---

采纳/新增任何能力时，落地不止装+登记：必须给出**编排集成规划**——简单任务怎么触达（路由/
语义兜底）、复杂任务落在哪个节点（Plan Agent/orchestrator/workflow 的位置）、场景适用、
登记动作、可达性验收，五件套齐才算完。

**Why:** luca 2026-07-12 在 mattpocock 对标 GATE-2 明确指示："你要把新增的 skill 怎么运用到
我的复杂任务、简单任务的 workflow 里面，这个编排层要深度的做规划。不能新增了就完了，要完美
落入我的使用场景和流程中。"——这是把 FM-11 的"采纳≠可达"从路由层扩展到编排层。

**How to apply:** 任何采纳/新建能力的交付物里包含编排集成节（参照 lucagstack
framework-audit/mattpocock-benchmark-2026-07/ORCHESTRATION-INTEGRATION.md 的五件套模式）；
落地验收含编排层 FM-11：一句自然语言实测简单路径 + 权威文件 grep 复杂路径位点。
关联 [[feedback-research-before-building]]。
