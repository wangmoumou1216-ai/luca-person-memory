---
name: missing-vote-is-not-a-verdict
description: 评审/表决轮因配额、超时等基建故障缺票时不算完成轮——先补票再出结论；基建故障≠反对票≠轮次完成
metadata:
  type: feedback
---

**多 agent 评审轮里有 agent 因基建故障（配额耗尽/超时/API 错误）没回票时，这一轮不算完成，
不得拿部分票出结论交付。** 缺的是票，不是弃权：基建故障既不是反对票，也不构成轮次完成。

**Why:** 2026-07-16 person 记忆 R4 终审，5 视角红队里 `rt:exec` 撞 session 限额未回票，
我按 4/5 票判了 converged=false 就准备交付终局。luca 打断纠正：「你的第四轮还没做完，你做完。」
限额重置后用 `resumeFromRunId` 缓存回放补跑——已完成的 5 个 agent 零成本回放，只真跑缺的
那一票。补票便宜到没有任何理由不补。**补回的 exec 视角恰好带回了全轮唯一一条沙箱实跑级
新发现**（A3 manifest 监视的是 backup 副本而非 live store，并发改写四门全绿而漏）——
证明缺的那票不是冗余，拿 4/5 交付就会把这个洞当「已验证」放行。

**How to apply:**
- 交付任何多 agent 评审/表决结果前，先看 failures / agents_error：有基建故障缺票 → 轮次未完成。
- 补票路径优先缓存回放（Workflow `resumeFromRunId` / 单独重跑缺票 agent）；等限额重置也比拿残票交付好。
- 例外只有一个：用户明确说「就按现有票数出」。默认永远是补齐。

关联 [[orchestrator-provenance-ledger]]（同场循环的编排纪律）、
[[verify-runtime-not-spec]]（结论必须站在完整观测上）。
