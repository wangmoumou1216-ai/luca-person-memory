---
name: serial-subagents-default
description: luca 定的 subagent 执行规则——一律串行，不并行（2026-08-08 起对所有模型生效，取代原「opus 可并行」的例外）
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 3bd9eeb7-b7ce-4bfc-90c7-79c9b7b3df01
  modified: 2026-08-08T02:36:23.057Z
---

**subagent 一律串行跑，不并行——不分模型。**

2026-08-08 luca：「下次跑 subagent 的时候，不要并行，串行跑。」当时我正并行派了 3 个 opus
深审 agent（正确性/安全/真跑）。这条**取代**了下面 7-18 那版里「opus 批可并行」的例外——
现在没有例外，fable / opus / haiku 都逐个跑。

**8-12 第二次强调**：「你的 subagent 不要并行跑。串行跑」。说这话时我在飞的只有 1 个 agent
——说明他是在**主动加固这条规则**，不只是纠正当下行为。按重复指示处理：这是硬规则不是当次
提醒，别在「这次只有两个」「这两个互不相干」这类理由下自行开例外。

**Why:**
- 原始动机（2026-07-18 skills 深审 W7）：13 批 fable skeptic 并行三次撞订阅限额，每次损失
  5-7 个在飞 agent 的**整批**工作。并行 fan-out 在限额下是全损模式——撞限时所有在飞 agent
  同时阵亡、整批重跑。
- 2026-08-08 扩面到全模型时 luca 没解释理由，我不臆测。可观察到的额外代价：并行批的
  在飞期间编排者对同一工作区的任何验证都不可信（见 [[feedback_orchestrator-provenance-ledger]]
  的对偶面），串行天然把这个窗口收窄到一个 agent。

**How to apply:**
- 多 subagent 工作一律 for-await 逐个派发，**等前一个回来再派下一个**。不再按模型分组决定串并。
- 串行意味着更慢：派发前先砍掉可有可无的那一路，别用"反正能并行"的心态开三路。
  真需要多视角时，把维度设计得互不重叠，宁可少一路也别凑数。
- 说了"下次"就是从下次起——**已经在飞的那批别砍掉重来**，让它跑完（2026-08-08 luca 用的
  正是"下次"这个措辞）。
- 配额紧张时仍按「判断杠杆 × 错判代价」分配模型：只把最高杠杆的少数给 fable，其余降 opus；
  限额反复撞死（≥3 次）主动提议 fable 全退役换 opus，别等他说（7-18 实例）。

与 [[feedback_subagent-tool-visibility]]（默认前台内联、delegate 须先声明）同族互补。
