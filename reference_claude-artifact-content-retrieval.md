---
name: claude-artifact-content-retrieval
description: 读取 claude.ai Artifact（含侧栏打开的）内容的实测降级链——WebFetch 会 boot 失败、侧栏面板无 claude.ai 登录态、内容在跨域 iframe 里文本抓不到，最终解法是 Chrome 里撑开 iframe 高度后分段截图
metadata: 
  node_type: memory
  type: reference
  originSessionId: 8c2905ee-8c16-4c1c-8ee4-ca0ae5994d11
---

读取 `claude.ai/code/artifact/{uuid}` 页面内容的实测降级链（2026-07-17，为读侧栏文章试错 4 层才通）：

1. **WebFetch 直读**（官方声称 artifact URL 可走 claude.ai 登录态）→ 实测可能报
   `artifact read failed: incomplete boot response`，重试无效（artifact 需 JS boot，非瞬时故障）。
2. **luca app 侧栏 capture 兜底对 claude.ai 无效**：侧栏浏览器面板**没有 claude.ai 登录态**，
   `luca-sidebar.sh capture` 只能抓到 Sign in 登录墙。登录态只在用户的 Chrome 里。
3. **Chrome（claude-in-chrome）打开原页**，但有三个坑：
   - 内容渲染在**跨域 iframe**（`{uuid}.frame.claudeusercontent.com`）→ get_page_text /
     read_page 无障碍树 / javascript_tool 都够不到 iframe 内部；iframe src 直连 curl 也要会话（只返回 10 字节）。
   - 加载慢：navigate 后页面空白，要 wait ~5s 再截图才有内容。
   - **滚动失效**：外层页不滚（scrollHeight==clientHeight），合成 wheel / PageDown 进不了 iframe 内部滚动条。
4. **破解（通用技法）**：外层 JS 把 iframe 高度撑大 + 祖先链 overflow 放开——
   `ifr.style.setProperty('height','4200px','important')` + 各级祖先 `height:auto; overflow:visible`
   → iframe 内容全展开、外层页变得可滚 → `window.scrollTo` 分段截图 + `zoom` 区域放大读小字。

适用面不限于 artifact：任何"内容锁在跨域 iframe 内部滚动区、文本工具抓不到"的页面都可用第 4 步。
相关：[[claude-source-access]]（读 Claude 自身源码的取证通道）。

**退化触发器（源头修复晋升 L5b，2026-07-17）**：坑②③已修源头——muse `2d3c683`（claude.ai 页签
persist:claude 分区 + capture webFrameMain 逐帧穿透）。**部署已实证**（07-17 复核：app.asar 构建
11:31 晚于 commit 11:29、app 进程在跑）——「重启」前提已满足，剩余唯一门槛 = 侧栏打开一个
`claude.ai/code/artifact/…` 页、跑 `luca-sidebar.sh capture` 看到 `[子帧]` 分节（非登录墙）。
**该实测通过后，本条降为指针**（只留：上游 WebFetch boot 失败事实 + Chrome 撑 iframe 通用兜底 +
指向修复 commit）。
