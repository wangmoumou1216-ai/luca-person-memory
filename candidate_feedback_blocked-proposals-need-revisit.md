---
name: candidate_feedback_blocked-proposals-need-revisit
description: 因某个 KILL 假设未验证而搁置的方案不会自己复活，而解锁条件可能早已成立——动子系统前先搜 framework-audit/proposals/，别从零重设计
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 8b0831e2-defb-4aa5-b610-3c1d1373406f
  modified: 2026-08-15T12:53:55.872Z
---

2026-08-15，luca 让我把 person 记忆层上 git。我正要自己设计放置方案时，因为 grep
`autoMemoryDirectory` **偶然**撞见 2026-07-16 的一批文档：

```
2026-07-15-person-memory-fragmentation.md
2026-07-16-person-memory-plan-v1 … v5-exec.md
2026-07-16-person-memory-unification-plan.md   ← 送审稿
2026-07-16-person-memory-unification-REDTEAM.md
2026-07-16-person-memory-consensus-report.md
```

**一整批已完成、已红队、已收敛到明确结论的工作**（§3 逐项评估 P1/P2/P3，建议 P3 独立私有仓，
明确判 P2「把私密数据放进公开仓，逆纹理」为不建议）。它当时卡在
**KILL-1：`autoMemoryDirectory` 真机未验证**，luca 说 review 后再动，然后就搁下了。

**而今天实测：那个 key 早就配在 `.claude/settings.local.json` 里、正在生效。**
解锁条件一个月前就成立了，没有任何东西去复查，整批工作静默沉底。

## 规则

**搁置 ≠ 作废。被 KILL 假设挡住的方案，其解锁条件不会自己来通知你。**

动任何子系统之前，先 `ls framework-audit/proposals/ | grep <关键词>` ——
「我要设计一个 X」之前先问「是不是已经设计过 X 了」。成本是一条命令，
省下的是从零重设计（而且大概率设计得比当时那版差，因为那版经过红队）。

## 与今天建的检测器同病

今天刚给 person / semantic 记忆加了「退化触发器到期复查」——扫记忆里的
「复查时机 / 待验证 / 上游修复后重测」声明并列进 digest。
**同一个病在 proposals 上没人管**：方案里的 `[BLOCKING] A1..An` / KILL assumptions
就是一模一样的「条件满足后该复活」的结构。

**源头修复候选（未做，等 luca 裁）**：把 `check_memory_integrity.py` 的
`check_decay_triggers` 扫描面从 person 目录扩到 `framework-audit/proposals/*.md`，
匹配 `KILL` / `[BLOCKING]` / `未验证` / `待 luca` 等标记 + 文件 mtime 天龄，
到期列进 digest 待裁。约 15 行，与既有扫描共用同一套逻辑。

相关：[[feedback_surface-buried-value-before-deleting]]（埋着的价值要挖出来挂到会被执行的地方）、
[[feedback_verify-params-before-offering-choices]]（同一件事的 7-16 险情：canonical 路径
差点把 person 记忆推上公开 GitHub——那条记忆今天真的救了我一次，我照它跑了可见性三查）。
