---
name: feedback_commit-muban-if-changed
description: "任何 session（含 fork session）改动了母版 ~/Desktop/luca_gstack，必须同 session git commit，绝不留 dirty；警惕 MEMORY_ROOT/SESSION_SYNC_BLOCK 终端 env 残留静默重定向写入。"
metadata:
  node_type: memory
  type: feedback
---

luca 在母版（`~/Desktop/luca_gstack`）之外并行运行 fork（如 muse fork `~/Desktop/项目/muse/gstack`）。规则（luca 明确）：**任何我的操作改动了母版，必须同 session 把母版 commit 进 git——不留 dirty/未提交状态。**

**Why:** dirty 母版会在下一次 fork↔母版 合并时被覆盖或丢失（见 [[parallel-lucagstack-fork-merge-care]]）。且 fork session 可能*静默*写母版：终端 env 残留 `MEMORY_ROOT=/Users/luca/Desktop/luca_gstack` 会让 `daily_governance.py`（`ROOT = env MEMORY_ROOT or __file__.parents[2]`）把 episodic/digest 写进母版，即使从 fork 里跑。另实测：`daily_governance.py --json` **不是只读**——`--json` 只改输出格式，仍会 consolidate + 写 digest。写入要用真实证据核实，别假设某个 flag 等于 dry-run。

**How to apply:**
1. 任何可能触及 memory/治理的操作后，跑 `git -C ~/Desktop/luca_gstack status --short`；若是我改的，就 commit（只 add 具体文件、不用 `-A`，避免卷入并行 session 的工作）。
   **共享追加文件（reviews.jsonl/index.jsonl 这类多 session 同写的 jsonl）加一步**：`git add` 之后、
   commit 之前跑 `git diff --cached -- <该文件>` 做**内容级**终检——「先 diff 确认只有我的行、再 add」
   不是原子的，并行 session 在两步之间追加的行会被静默卷入；只查暂存**文件名**查不出这个
   （2026-07-15 自查发现的协议洞，当次未出事但窗口真实存在）。
2. 母版 pre-commit（`scripts/verify.sh` C11 hooks 测试）在 `SESSION_SYNC_BLOCK=0` env 残留下会**假失败**（母版 test-hooks.mjs 缺 fork 的 hermetic env-strip）。用干净 env 提交——`env -u SESSION_SYNC_BLOCK -u MEMORY_ROOT git commit ...`——**不要 `--no-verify`**（门禁仍须真跑真过）。
3. 两个终端 env 残留会污染 session 且只能重启终端/muse app 清除：`SESSION_SYNC_BLOCK=0`（压制 session-sync Stop 捕获）和 `MEMORY_ROOT=母版`（重定向治理写入）。都不在任何配置文件里。
