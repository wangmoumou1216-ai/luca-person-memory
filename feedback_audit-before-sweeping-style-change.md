---
name: audit-before-sweeping-style-change
description: 批量视觉/颜色改动前先地毯式审计值、分清画布面vs功能色、给用户看清单再精确替换，别误伤按钮标签
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 665ce415-790f-409f-a3f2-1ae04f7cee45
---

要做批量视觉改动（把某类背景/颜色全改）时，别直接动手。luca 会担心"把按钮啥的都改了"——他明确要求先「地毯式查找」再改。

**Why:** 一次"把 agent 灰底改白"的需求，若无脑 find/replace 会连按钮/标签/chip/hover/徽标一起改坏。luca 两次表达对误伤功能元素的担忧，并直接指令先审计。地毯式审计后发现：~100 个 background 值里，大面积画布灰底只有 2 个（`#fbfcff`/`#eff1f3` 在 ~5 个选择器），其余全是功能色——精确锁定后零误伤。

**How to apply:**
1. 先 grep 出全部 `background(-color)` 值按频次列清单，用 awk 把每个目标值映射到它的选择器。
2. 分两类呈现给用户：**要改的画布/表面色**（.main-shell 这类大容器）vs **绝不动的功能色**（按钮/标签/chip/hover/徽标/输入/卡片浅底），让用户确认范围后再动。
3. 用**精确串**替换（如 `background: #fbfcff;` 而非裸 `#fbfcff`），避开 box-shadow/border 里的同色值；逐选择器或按精确串 replace_all，不做裸值全局替换。
4. 改完逐区截图核对：目标面变了、功能元素颜色不变（重点回应"没误伤按钮"）。

这是 [[feedback_cover-enumerated-asks-proportionally]] 与 CLAUDE.md「Surgical Changes」的具体化：sweeping 改动 = 先审计+确认范围+精确替换。

附带教训（同 session，另一条）：shareclawdemo 这类**有发布/重置流程的外部仓库**里，一整个 session 的未提交改动曾被一次「Demo417 发布」git 操作整体覆盖、需全部重做。实质工作后主动提示 luca commit/checkpoint 固化；工作凭空消失时先查 git(log/status/reflog) 定因、别假设是自己改的、并向用户澄清不是我改的。

**2026-07-31 补强（muse 外观模式双评审实证）**：功能色清单的「绝不动」必须包括**压在功能色上面的前景字色**——
它们服务于恒定的功能色底、不跟主题走，批量"色值→主题变量"映射时一并变量化就是回归（本案把橙色主按钮的
深墨字色 sed 成了面板背景变量，浅色档 2.47:1，部署两轮后才被独立评审抓出）。判据：一个色值属于哪组，
看它的**搭档色**跟不跟主题——搭档恒定则自己也恒定。
