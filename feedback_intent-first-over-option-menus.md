---
name: intent-first-over-option-menus
description: 大目录选参数别砍成4格选择题——意图先行开放问+语义匹配+回显确认；候选只用可想象的名字与已知口味圈
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 93706817-59a3-44cb-a3b3-8f723f43bd63
  modified: 2026-08-04T04:04:17.171Z
---

大目录（几十项，如 OD design system 60+）选参数时，把目录砍成 AskUserQuestion 的 4 个格子 = 把我的筛选偏好当成用户的选项空间：用户想要的不在格子里，只能凭记忆手打（2026-08-04 luca 纠正「很多不是我想用的，还得手动打字，不好用」；当场行为证据：无视 4 个候选、在 Other 里点名 notion/github/slack/vercel 四个）。亮全目录也不是解——名字墙是高频固定成本，且 90% 条目凭名字想象不出样式。

**Why:** 选择题结构预设「我出题你答题」，但用户对大目录的真实心智是「我说要什么，你去找」；候选用抽象风格词（Neutral Modern/Enterprise）用户无法想象，知名产品名（Notion 风/GitHub 风）才可想象；单选预设也错——多方案横向对比是 luca 的常态用法。

**How to apply:** ①默认不出选择题：开放一问（点产品名/说感觉/要几个方案都行）→ 语义匹配目录 → 一句话回显映射结果确认（语义匹配可能错，回显是唯一保险）；②若出选择题，候选只从用户已知口味圈出，用可想象的产品名不用抽象词；③目录按需可见：用户问才贴，分组+每行一句调性注释；④用户的选择回写成偏好，下次带默认值。源头已修：open-design SKILL.md Phase 2b（v3.2.0，含偏好行活数据）；同族 [[feedback_skill_routing_verify]]（派发前问用户档位）、[[feedback_verify-params-before-offering-choices]]（选项参数出口前必验）。
