---
name: feedback_interactive-handoff-not-headless
description: "想要的交互式 UX 先验证目标工具技术上做不做得到；OD 的\"注入UI session让它问你\"实测不可行→最终 headless 一次性"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fd0a75d0-aa58-478a-8fa9-3758085cb81f
---

本想给 Open Design 这类自带 agent 的工具做**交互式交接**：把指令注入 OD 当前 UI session、它在 OD UI 里 ask user input 问用户、用户在 OD 里答/迭代。
**实测发现 API 做不到**：OD 桌面端 UI 只渲染**它自己发起**的 session；外部 `/api/chat` 触发的 run 不在 UI 露出（对话显示 0 消息）；`POST .../messages` 是 **404 只读**；`/api/active` 指向的是文件不是可 post 的活会话。

**最终方案（用户拍板）= headless 一次性出图（不反问），人工判断后置**（落盘后展示给用户看，要改就重跑）。

**Why:** 用户先要 ask-user-input 交互；我来回试了 `/api/runs`、`/api/chat`、messages 端点都注入不进 UI；用户最后说"做不到就回 headless"。我却已先写了一整版"交互优先 v2.0" skill，结果推翻重写 v3.0 headless——白费一轮。

**How to apply:**
- **设计 skill 前，先验证想要的 UX 在目标工具上技术可行**，别围绕一个做不到的交互模型把整套流程写完。
- 接外部 agent 工具：先摸清它「自发起 session vs 外部 API 触发」的边界、消息端点是否可写、动态端口等事实，再定 skill 流程。
- 需求多轮反复时，skill 文件别每轮都改；先把实际流程跑通定稿，再一次性回写（见本次先 v2.0 后 v3.0 的返工）。

**复发 + Claude Design→Code 取源接法（2026-06-24，搬 .dc.html 进 Figma）：** 用户点名的 `claude_design` MCP 这会话没注册、又没法热加载；我没立刻上报，反而去浏览器逆向 claude.ai 内部接口硬取，烧 15+ 调用、被两次打断、Figma 零产出（止损通则见 [[feedback_no_confirmation_loops]]）。这条工作流以后这么接：
- **取源阶梯（便宜→兜底）：** 直接请用户贴 `.dc.html` / 读一次（最快）→ 装 `claude_design` 远程 MCP 走 `get_file`（正路；命令 `claude mcp add --scope user --transport http claude_design https://api.anthropic.com/v1/design/mcp` + 用户 `/design-login` 授权，**均未实测，用前先验证**）→ 本地 `open-design` daemon（仅本地项目）→ 浏览器逆向（**劝退，别默认**）。
- **取源 ≠ 需要 MCP：** 源就是个文件，让用户贴即可；只有"写进用户 Figma"才真需 `figma` MCP（`use_figma`）。别把"用户句子里提了某 MCP"当成"整条链路都得用 MCP"。
- **事实：** `.dc.html` 是 Design Canvas 格式（`<x-dc>`+DCLogic+注入 style），不是普通 HTML；要真实渲染得连项目里的 `support.js` 运行时一起加载。

关联 [[feedback_redteam-own-analysis-before-shipping]]、[[feedback_semantic-not-hardcoded-keywords]]、[[feedback_verify-your-verification]]。
