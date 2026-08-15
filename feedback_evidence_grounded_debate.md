---
name: evidence-grounded-debate
description: "When running adversarial debates / red-team reviews, ground every contention in tool-verified evidence, not abstract argument"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a9d38729-9e1d-471e-a6ca-bbdb237df8be
---

When orchestrating adversarial debate or multi-agent red-team review, every contention (attack AND defense) must be backed by a tool-verified fact — read the real file, run a probe, check official docs — not rhetorical argument.

**Why:** During a framework-audit debate the user interrupted to say "在辩论过程中，可以调用tools，去做你的辩论作证。自由发挥，不要干辩论" (use tools to gather evidence for your debate arguments; don't do hollow/dry debate). The evidence-grounded rounds were far more productive — critics empirically reproduced a route-guard misfire, proved `\b` fails on CJK via a Node probe, and verified the Stop-hook `decision:block` contract from official docs, which overturned/corrected several conclusions that pure argument would have left standing.

**How to apply:** Instruct critic/defender subagents to verify claims against real code/docs and cite file:line or probe output. As moderator, verify contested pivotal claims yourself before conceding or defending. Convergence comes from evidence, not from who argues longest. Falsifiable measurements > opinion when a disagreement can't be settled by inspection.
