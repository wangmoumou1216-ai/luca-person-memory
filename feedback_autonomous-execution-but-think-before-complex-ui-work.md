---
name: autonomous-execution-but-think-before-complex-ui-work
description: "Run autonomously without asking for most decisions, but for complex or UI/design work, think through the complete design up front rather than reacting piecemeal to each round of feedback."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 50abfbd1-8d44-4968-be88-d62fad683cce
  modified: 2026-07-24T04:41:32.032Z
---

Two preferences that combine, not contradict:

1. Default to autonomous execution. Luca said explicitly: "除了必须的需要我决策的，其他的都直接跑，你的决策需要谨慎，如果决策犹豫就调研，研究，开红队对抗，跑测试等等方法辅助你决策。除了必须必须的，不要询问我" (run everything yourself except decisions that truly require me; if uncertain, resolve it via research/testing, not by asking). Ask only when genuinely blocked on a decision only he can make.

2. But for complex or UI/design work specifically, pause and design the complete picture before implementing — don't just react to each screenshot/correction in isolation. During a stretch of UI iteration on todo-capsule's execution-log feature, luca said: "我建议你想好再做，UI上面，也想好了再做" (think it through before doing it — for UI too) after several rounds of build-one-piece → get corrected → rebuild. The pattern that triggered this: I'd implement a narrow interpretation of a request (e.g., tool-call-only execution log), ship it, then get corrected piece by piece (add messages, match CLI rendering, wrap in a card) instead of having asked the clarifying questions or thought through the full shape up front.

**Why:** These aren't in tension — autonomy is about not over-asking for permission on routine decisions; the "think first" note is about not under-designing before starting non-trivial or visual work, where reactive iteration wastes rebuild/rebuild-and-retest cycles and reads as not having listened carefully the first time.

**How to apply:** For routine technical fixes, proceed directly. For UI/design changes or genuinely complex features, take a beat to lay out the complete design (data model, all rendering states, how pieces interact) before writing code — even a few sentences of "here's the full shape I'm about to build" before diving in — rather than shipping a minimal slice and letting correction-rounds fill in the rest.

**复发（2026-07-24，语音胶囊波形，同一产物连吃三轮打回）：** 依次被指出「条太粗不高级」→ 改细
→「又有点奇怪，没有说话的波形震动 / 细了有问题可以粗一点」→ 修仿真+回粗 →「颜色不该是橘色，
用声波该有的颜色」。**每轮我只改被点到的那一个维度**，等下一轮再补下一个——正是本条记忆说的
"切窄片让纠正轮补形"。**判据补充：视觉元素也有"完整形态"清单，动手前要一次列全再做。**
仿真类视觉（波形/图表/进度/粒子）的清单至少含四项：① 几何（粗细/密度/间距/圆角）② **数据真实性**
（驱动它的信号像不像真实物理——本次根因：我用平滑正弦当人声，每根条一样高＝条形码；真人声是
音节爆发+快起慢落+词间空隙+句末静音，跟随要非对称峰值跟随）③ 颜色语义（品牌色≠功能色：橘是
品牌色，用在声波上像仪表盘；声波该用电平表绿）④ 自身微动效（每根条的弹簧跟随/入场揭幕/静默
呼吸——静态元素即使数值在变也显死）。关联 [[feedback_verify-your-verification]]（多态 UI 逐态取证）。
