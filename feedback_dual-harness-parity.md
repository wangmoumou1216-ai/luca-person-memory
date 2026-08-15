---
name: dual-harness-parity
description: 改 luca_gstack 框架面必须 Claude 与 Codex 两套同时适配——CLAUDE.md 改了 AGENTS.md 就要改，hook/工具/纪律同理
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 919bccc7-72d9-47ac-8059-5044e28c41c5
  modified: 2026-08-07T11:45:30.956Z
---

**改框架就是改两套。** luca_gstack 有两份平行契约：`CLAUDE.md`（Claude Code）与 `AGENTS.md`（Codex），
`.claude/settings.json` 与 `.codex/hooks.json` 同理。动其中一边而不动另一边＝只做了一半。

**Why:** 2026-08-07 luca 明确指示：「你要做好 codex 的适配啊。不能只做 claude。**你以后要严格遵守
做框架更改要遵循两套都要适配的纪律**。」当时我的方案里写着「lucagstack 侧：CLAUDE.md 加第 7 个工具」
——AGENTS.md 只字未提。

**更严重的是它暴露了一个已部署的遗留**：同一天我刚做完「让 codex 成为一等 CLI」的整个项目
（app 按 CLI 注入 MCP、codex 六个 `mcp__muse__*` 实测全可见），却**两份契约都还写着 codex 没有该通道**：
- CLAUDE.md：「不可见（终端/降级/**Codex**）→ 走上列脚本」
- AGENTS.md：「agents without it, **including Codex**, use the shell-script paths」

后果是 codex session 会据此以为自己没工具、主动走降级路径——**我把能力做出来了，却让契约告诉它别用**。

**How to apply:**
- 动这些面时，**同一个改动里**两边都改：`CLAUDE.md` ↔ `AGENTS.md`；`.claude/settings.json` ↔
  `.codex/hooks.json`（codex 侧经 `.codex/codex-hook-adapter.mjs`，新 hook 条目还要带
  `additionalContextLimit: 0`，且要跑 `codex-trust-hooks.mjs` 授信，否则 codex **静默跳过**全部 hook）。
- 仓里已有守护，别绕过：`verify.sh` 的 **S29**（`check:agents-parity`）、**S30**（codex 存活性 registry）、
  **C16**（self-model 漂移门）。改完先跑 `bash scripts/verify.sh`。
- **加了能力就要回头改契约里"没有该能力"的旧话**。新增能力时顺手 grep 两份文件里对该能力的既有描述
  （尤其是"Codex 不支持 / 降级 / 不可见"这类），否则能力上线了、文档仍在劝退它。
- 判据不是"我改的是不是 Claude 专属文件"，是"**这条规则/能力对 Codex 档成立吗**"——成立就必须让
  AGENTS.md 也说得出来。

相关：[[feedback-orchestration-integration-on-adopt]]（采纳≠可达，新增能力要给编排层集成五件套）、
[[feedback_dont-shrink-existing-logic-without-approval]]（适配 Codex 时优先加适配层，别裁既有逻辑）。
