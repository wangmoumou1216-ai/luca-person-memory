---
name: feedback_semantic-not-hardcoded-keywords
description: "用户要语义理解的意图(如单点交接)不要写死关键词表;语义识别放 LLM 层,hook 只做粗网"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fd0a75d0-aa58-478a-8fa9-3758085cb81f
---

当用户期望"按自然语言语义理解意图"时（典型：单点交接「把刚产出的 xx.md 给到 OD」），
**不要把触发措辞写死成关键词表塞进确定性 hook**（route-guard.mjs 只能 substring 匹配，做不到语义）。

**Why:** 用户明确反馈两次——"我的单点是自然语言触发，你不能写死关键词，要结合我的语义"。
route-guard 是 deterministic 子串匹配器，枚举"给到/丢给/发给/交给"这类措辞既不全也违背用户意图。

**How to apply:**
- 语义意图识别放在 **LLM 层**（CLAUDE.md 的 Skill 调用规则 / skill SKILL.md 的判定段），
  用"描述意图 + 非穷举示例 + 识别要素"表达，明确写"按语义判断、不是词表匹配"。
- hook（route-guard / routing-map triggers）只保留**真实产品名/直呼**（如 "Open Design"/"OD生成"）
  作为粗匹配粗网，并注释说明语义识别由 Claude 负责。
- 允许"route-guard 因无关键词输出 STOP，但语义清晰时 Claude 仍按规则识别"——执行前用一句话确认源产物。

关联：[[feedback_skill_routing_verify]]（调 skill 前核对职责）。本次落在 luca_gstack 的
open-design skill（OD 连接器，design_output 主力）embedding。
