---
name: feedback_execution_handoff_as_goal
description: "跨 Session 的执行交接，最终必须给一段可直接粘贴的 /goal 指令；把权威、读序、执行法、门、验证和唯一终态都写进指令"
metadata:
  node_type: memory
  type: feedback
  originSessionId: mattpocock-skills-cycle2-2026-08-09
  modified: 2026-08-09
---

当 luca 要把一份最终方案交给**另一个 Session 执行**时，最终交付不能只给普通 prompt、文档链接或“请按 Plan 执行”的一句话；必须给一段**可整段复制、首 token 为 `/goal` 的执行指令**。

这段 `/goal` 至少内联：

- 唯一目标和唯一完成态；
- canonical repo、任务身份与禁止作用域；
- Final Plan / handoff / manifest 的权威顺序和固定读序；
- 第一条确定性 verifier 命令及 PASS token；
- Phase、DEV/TST、独立验证的推进规则；
- 需要真实用户批准的人类门及 exact-payload 约束；
- dirty/untracked、Git 和全局写入纪律；
- 不可漂移的关键裁决、BLOCKED 条件与何时才能把 goal 标 complete。

Why：2026-08-09 mattpocock/skills 自进化最终交接中，初版 `NEXT-SESSION-PROMPT.md` 是普通说明。luca 明确纠正：“最终给另一个 session 的提示词要是一个 `/goal` 指令，并描述清楚如何执行、阅读、做任务。”随后将交接改成单段 `/goal`，并用 final handoff verifier 咬住文档、读序、任务矩阵与终局 token。

边界：只适用于“最终方案交给新 Session 持续执行”的 handoff；普通问答、短任务或当前 Session 直接执行不强制包装成 `/goal`。
