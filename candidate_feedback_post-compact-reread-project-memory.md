---
name: post-compact-reread-project-memory
description: compact 之后项目本地记忆的注入没了——续跑前先重读一次，否则会花大代价重推早已记过的坑
metadata:
  node_type: memory
  type: feedback
  status: candidate
  signal: 2-recurrence
  originSessionId: 919bccc7-72d9-47ac-8059-5044e28c41c5
  modified: 2026-08-08T02:29:51.727Z
---

**compact 之后，项目本地记忆（`~/Desktop/项目/<name>/.luca/memory/`）的启动注入已经不在
上下文里了。**续跑第一件事应当是重读该项目的 `MEMORY.md` 索引（或跑
`search_memory.py "<本轮任务>"`），否则我会用最贵的方式重新推导早就写过的坑。

**Why:** 2026-08-08 muse session，compact 后续跑，同一轮里踩了**两个自己已经写进项目记忆的坑**：

1. `grep` 对 `renderer/app.js` 静默返回 exit 1（文件含既有 NUL 字节被判为二进制）→ 我据此
   以为 checkpoint 记录的代码"不存在"，差点去重写。而 `sidebar-fswatch-stale-after-sleep.md`
   里白纸黑字写着「读 renderer/app.js 都必须 `grep -a`——否则误报全 0」，还标着「7-15 再咬」。
2. 隔离实例的假 HOME 路径太长导致 unix socket 起不来（macOS sun_path 104B 上限）→ 白跑一轮
   启动。而 `muse-mcp-bridge.md` 的「隔离测试要领」第 1 条就是这个。

两条都不是新知识，是**检索失败**。CLAUDE.md 的记忆协议本来就要求"具体任务优先运行
`search_memory.py`"——我没跑。真正非显然的增量是：**compact 是这个失败的高发点**，因为
project.sh 激活时的一次性注入随上下文一起消失了，而我主观上觉得"这个 session 一直在这个项目里"。

**How to apply:**
- compact / 新 session 续跑 **既有项目**的实操任务时，动手前先 `cat <项目>/.luca/memory/MEMORY.md`
  的索引行（便宜，几百字），命中再读具体文件。
- 判断触发点不是"换项目"，是"**上下文断过**"（compact / resume / 新 session）。
- 顺手校正：记忆条目里的**数量类事实**（工具数、断言数）最容易过期，读到时顺手核一眼再引用。

相关：[[feedback_project-entry-orient]]（进项目先摸结构）、
[[feedback_verify-your-verification]]（本次两个坑的失败形态都是"静默"，与那条同族）。
