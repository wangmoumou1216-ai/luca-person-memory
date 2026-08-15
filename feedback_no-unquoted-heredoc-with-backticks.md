---
name: no-unquoted-heredoc-with-backticks
description: 含反引号/$()的多行文本别用 unquoted heredoc 喂脚本——shell 会命令替换把命令输出注入进内容，污染目标文件
metadata:
  node_type: memory
  type: feedback
  originSessionId: 0961ecdf-bcba-4f92-a28f-51d34cb10848
  modified: 2026-07-30T02:59:04.463Z
---

要往文件里写含**反引号 `` `...` `` 或 `$(...)`** 的多行文本（尤其技术文档，正文常有 `git grep X`、
`npm run Y` 这类反引号代码片段），**绝不能用 unquoted heredoc**（`<<PYEOF` / `<<EOF`）把它喂给
python/其它脚本——shell 在传给脚本**之前**就对 heredoc 体做命令替换，把 `` `git grep golden` ``
真的执行了，结果（57KB grep 输出）被当字符串注入进你要写的内容，污染目标文件，还得截断重写。

**Why:** 2026-07-28 审计 Round1，我用 `python3 - <<PYEOF ... s.replace("`:74`", ...) ...`
往 PLAN 追加"审计注记"，注记文本里有一句 `` `git grep -ni golden` 在全 range commit 零命中 ``。
heredoc unquoted → shell 执行了 `git grep -ni golden`，把匹配到的 .agents/SKILL.md/CHANGELOG/
framework-audit 等一大堆行注入进 PLAN，注记里所有反引号内容（`report`/`:74`/`:47`）也被替换成空。
返工：head -373 截断污染段 + 改用 Write 一个 .py 文件再 `python3 该文件` 重写干净注记。

**How to apply:**
- 含反引号/`$()`/`$VAR` 的多行内容要写文件 → **Write 一个脚本文件，再 python3/bash 跑它**；
  绝不 inline unquoted heredoc。这也是本仓已有的稳妥姿势（scratchpad 里写 fix_*.py 再跑）。
- 必须用 heredoc 时用**quoted 界定符**（`<<'PYEOF'`）——单引号让 shell 不做任何替换，heredoc 体
  逐字传入。但 quoted heredoc 里就不能用 shell 变量了，权衡。
- 编辑已有文件优先直接用 **Edit 工具**（完全不过 shell，无此风险）；只有生成大段新内容才写脚本文件。
- 同族 shell 语法陷阱：[[feedback_verify-repo-with-git-c-not-cd-chains]]（cd 链传染）——都是
  "shell 在你以为的边界之前就动了手"。写前问一句：这段文本进 shell 会不会被二次解释？

**复发变体（2026-07-30，同根因新载体）：** `node -e "…双引号脚本…"` 注入 renderer 代码，
模板串里的 `$('#btnSidebar')` 被 shell 当 `$(...)` 命令替换吃掉（报 `command not found: #btnSidebar`），
写进生产文件的代码缺了半句——**双引号包裹的 inline 脚本与 unquoted heredoc 同罪**。载体清单更新：
unquoted heredoc / 双引号 `node -e` / 双引号 `python3 -c`，全都会二次解释 `$(`。稳妥姿势不变：
Write 脚本文件再跑，或单引号包裹（内容含单引号时用 quoted heredoc `<<'EOF'` 喂 python）。
本次靠 stderr 的 `command not found` 当场暴露——**注入类写入后必 grep 验证落盘内容**，别只看退出码。
