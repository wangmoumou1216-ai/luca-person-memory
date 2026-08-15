---
name: feedback_route-around-others-inflight-work
description: 被另一个 agent/session 的在途未提交工作挡住时，绕行方案是我的活不是 luca 的——报阻塞可以，但必须同时带来一个可执行且可证不干扰的路径
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 8b0831e2-defb-4aa5-b610-3c1d1373406f
  modified: 2026-08-15T07:54:01.137Z
---

2026-08-15，记忆治理执行到「两检出对齐」时撞上：muse 检出有 341 行未提交改动（9 个文件），
且待入的 7 条 commit 与其中 7 个文件重叠，`git pull` 必冲突。我查清了事实、报告给 luca、
然后**把处置决定推给了他**（"需要你定一件事：那 341 行和那 7 条 commit 是同一件在途工作吗"）。

luca 的回应是纠正：

> 「你说的那个工作区的工作是不是 codex 正在执行的一个工作。如果是的话，他确实还没有执行完，
> 也没办法现在收尾。**那你要想一个万全的方案，来去执行。**」

## 规则

**别人的在途工作是障碍物，不是许可我停下的理由。** 报阻塞是对的，但报完必须同时给出
**一条可执行、且能证明不干扰对方的路径**——找路是我的活。
（与 [[feedback_stay_in_session_scope]] 不冲突：那条说的是**不替它做提交决定**，
本条说的是**绕开它继续干自己的**。两条合起来 = 不碰它，也不停。）

## 万全绕行的配方（本次实测有效）

1. **先把"哪些文件真有冲突"变成事实**，别用"这个仓在动"一刀切：
   逐个文件查 `git diff --name-only`（脏集）与 `git diff --name-only HEAD..@{u}`（待入集），
   取交集。本次实测：我要改的 `memory/scripts/*.py` **脏:0 待入:0**，
   整个剩余计划与争议集零交集——障碍其实只挡住了一小块。
2. **收工作面**：把还需要碰争议文件的部分改设计绕掉（本次把「抬 MAX_PREVIEW」换成
   「子块重排」，`session-restore.mjs` 就完全不用动了），实在绕不掉的推迟并明说。
3. **worktree off upstream/main** 做提交（`git worktree add <path> -b <branch> upstream/main`），
   **不在对方的工作树上移动 HEAD、不 commit、不切分支**。
   worktree 里跑 git 一律 `env -u GIT_DIR -u GIT_WORK_TREE -u GIT_INDEX_FILE`
   （见 [[verify-runtime-not-spec]] 同族：继承的 GIT_* 会让 `git -C` 也落到父仓）。
4. **push 到新分支不推 main**：`git push upstream HEAD:refs/heads/<branch>`——
   落后主干十几条也能推，零干扰，对方随时可合。
5. **开工前立基线、收工后逐条比对**：把对方脏文件的 md5 清单与 HEAD 存下来，
   收尾时 `diff` 证明"我没碰"。**是测出没碰，不是自称没碰。**

## 别背的东西

remote 名现查（本仓叫 `upstream` 不叫 `origin`，我第一次就写错了——同
[[feedback_autocommit-push-high-confidence]]）。
