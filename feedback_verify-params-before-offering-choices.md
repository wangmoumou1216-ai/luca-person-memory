---
name: verify-params-before-offering-choices
description: 给用户做决策的选项里的具体参数（路径/命令/值）是承诺物不是草图——出选项前必须验其后果
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b3d3fee4-1e2c-4814-a115-62c924a73942
  modified: 2026-07-23T04:21:12.610Z
---

**AskUserQuestion 的 option / preview 里写的每个具体参数（路径、命令、值），出口前必须先验证
照它执行会发生什么。** 那不是示意图，是用户会照此拍板、后续步骤会当既定前提的承诺物。

**Why:** 2026-07-16 person 记忆统一方案，我在选项预览里写死 canonical 路径
`~/Desktop/luca_gstack/memory/person`。luca 据此选了「方案 A」。**该路径位于
`wangmoumou1216-ai/luca_gstack` —— 一个 PUBLIC 仓 —— 且 `memory/` 被 git 跟踪（36 文件，
远端 origin/main 上可直接 `git cat-file` 读出内容）。照此执行 = 把 person 记忆
（个人偏好、宠物名、全部对 AI 的行为纠正）推上公开 GitHub。** 我是在执行前 preflight
自查隐私面时才发现的，此时 luca 的决策已经基于错误信息做出。

危害不只是"路径要改"：**用户的决策已被污染**。事后我单方面换路径 = 替他改了他做过的决定；
不换 = 按错的执行。两条都不对，只能回头承认并重新给他选 —— 这个代价本可以在出选项前
花 10 秒（`gh repo view --json visibility` + `git ls-files`）避免。

**How to apply:** 出选项 / 出计划 / 出命令前，对每个具体参数问一句
**「照这个执行会发生什么，我验过吗？」**。路径类固定三查：① 它在不在某个 git 仓内
（`git -C <dir> rev-parse --show-toplevel`）② 那个仓是不是 public（`gh repo view --json visibility`）
③ 目标是否被跟踪（`git ls-files`）。同族：[[feedback_symptom-first-before-acting]]（没见过症状不许动手）、
[[verify-runtime-not-spec]]（别把文档规定当实际行为）——三条共享同一个根：**承重的东西，出口前自己验一遍。**

**复发注记（2026-07-22，作用域漏了 push）：** 本条的「路径三查」我一直只在**出选项前**用，没意识到
**`git push` 是同一个出口**——推之前我只验了 remote 存在、没验可见性，推完 luca 问「你推送哪里了」我才去查，
才报出「这仓是 PUBLIC」。这次内容本身没问题（改的是本就公开的框架文件、无新增敏感信息，luca 也确认 public 是预期），
**但发现时机错了：可见性应该在按 push 之前就核并主动告知，而不是被问了才查。** 规则扩容：
**三查的触发点不止「出选项前」，而是任何"内容离开本机 / 进入他人可见面"的出口——push、发 PR、Artifact 发布、
分享链接、贴进外部工具都算。** push 前固定动作：`git remote -v`（推去哪）+ `gh repo view --json visibility`（谁能看到）
+ 推完 `git ls-remote origin` 问远端确认真落地（不信本地缓存）。同族 [[feedback_verify-with-real-evidence-before-reporting]]。

**复发注记（2026-07-16 当日、同 session、本条写下后不到 6 小时）：** AskUserQuestion 预览里
给 luca 的 SC-005 reject 命令写成 `review_candidates.py --reject`——该脚本根本没有 `--reject`
（真身在 `consolidate_memory.py`）。交接前跑 `--help` 才验出。命令与路径同权：
**给用户粘贴执行的每条命令，出口前先 `--help`/dry-run 验它存在且语义如所述。**

**复发注记（2026-07-23，写进计划的目标值同权）：** mattpocock 更新对标里我把「ack 推进至
ed37663c（repo HEAD）」写进靶子 §3.8——fable 判官核了 `daily_governance.py:450` 才发现 watcher
的比较键是 **path 域** 最后触碰 commit、明文不用 repo HEAD（防 monorepo 假漂移）：填 HEAD 等于
没填，下轮照样告警。是判官抓的不是我自己验的。规则扩容第三面：**「参数」不止选项和命令，还包括
写进计划/共识/登记文件的目标值——出口前先读它的消费机制（比较逻辑/校验器）确认按此值真的生效。**
本例 10 秒验法：拿既有生效值（697d4ce9）反推比较语义，或直接读 watcher 比较行。
