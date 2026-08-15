---
name: fable-decision-review
description: "luca 点名\"重大决策/让 fable 看一下\"时的固定动作——spawn fable 复审官并原样亮出裁决,不依赖 advisor(上游坏)"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 46fb52a9-e6b9-40fa-ba4b-3522084e6a34
---

luca 说「这是重大决策」「让 fable 看一下」「fable 复审」这类话时,固定动作:
spawn 一个 `model: fable` 的 subagent 当独立复审官——打包决策上下文(问题、备选方案、
已有证据、约束),让它出独立裁决,然后把裁决**原样**摆给 luca 看,我再给综合意见。

**Why:** 2026-07-10,luca 想要"重大决策用最强模型把关",为此把 advisorModel 绑了 fable,
但 fable advisor 被上游 bug 打死(见 [[advisor-fable-broken-upstream]]),且坏得静默——
他体感"没效果"却无处报错。spawn fable subagent 是本 session 实测验证过的可靠替代
(fable 可达、model 参数被认),且定制上下文天然绕开弄死 advisor 的两个坑
(全量 transcript 带 tool_use / >100K tokens)。

**How to apply:**
- 触发靠 luca 显式点名为主;我在不可逆/高风险决策点可主动**提议**(不擅自 spawn,他的
  自主度哲学是"新任务低自主短皮带")。
- lucagstack 编排内的复审已有 `fable_whitelist` P0/P2 管,本条只管**对话层**的口子,别重复造规则。
- 主循环已是 fable 时,复审官价值 = 独立上下文 + 对抗立场(非"更强模型");luca 若觉得冗余可砍。
- advisor 上游 issue(#76199/#67609/#67411)关闭后,提议把 advisor 捡回来当自动层,本条降级为兜底。
