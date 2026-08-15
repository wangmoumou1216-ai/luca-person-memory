---
name: autocommit-push-high-confidence
description: 高置信改动默认主动 commit+push 到该仓既有 remote/branch，不等 luca 逐次开口（2026-07-11 明确指示；remote 名每次现查别背）
metadata:
  node_type: memory
  type: feedback
---

luca 明确指示（2026-07-11）：**以后高置信度的改动，都提交、都 push**——不需要他逐次说"提交"/"push"。

**Why:** 本 session 他连发三条"先提交"→"那两个文件提交"→"push"，全是本可默认的动作；留 dirty/未推状态还有 fork↔母版合并覆盖风险（见 [[feedback_commit-muban-if-changed]]）。

**How to apply:**
1. **高置信 = 验证已过 + 方向已获授权**：pre-commit/verify.sh/parity 绿、改动落在用户批准的计划或明确请求范围内 → 做完直接 commit（外科式 add 具体文件，不 `-A`）+ push 到该仓**既有** remote/branch。
   **别背 remote 名，每次现查**（2026-08-08 实测被旧记忆坑过一次）：
   `git -C <repo> rev-parse --abbrev-ref --symbolic-full-name @{u}` 一条命令拿到真实 tracking。
   已知反例：`~/Desktop/项目/muse/lucagstack` 检出的 remote 叫 **upstream/backup，根本没有 origin**
   （tracking = `upstream/main`）；muse 仓主干是 **refactor/claude-code-kernel 不是 main**。
   旧记忆里"母版 origin main / fork backup muse"那套是双仓时期的说法，2026-07-16 单真值源合并后已失效。
2. **不属于高置信、仍要先问**：实验性/探索性改动、force-push/改历史、新建 remote 或换分支、用户明说"先别提交"、以及我自己拿不准该不该保留的改动。
3. push 前照旧检查 remote 是否有新提交（有则 rebase）；commit message 沿用仓库既有惯例与 trailer。
