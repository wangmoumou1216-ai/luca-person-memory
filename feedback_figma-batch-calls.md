---
name: figma-batch-calls
description: Figma MCP 调用要批量化——一个 section 一次 use_figma 脚本+开头预载字体+缓存节点ID+section级验证，消灭试错重试；每call先setCurrentPage；getNodeByIdAsync返null先按name搜不当删除；布局验证后加大批量、收尾单次截图
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4df4af9b-5629-41fe-8c39-2cd84028c9ad
---

luca 观察到我调用 Figma MCP 时次数过多（2026-06-10 提出），要求尽量减少调用次数。

**Why:** 多次调用有三类来源：① Figma 官方 skill 规定的分 section 增量组装 + 写前发现性读取（get_metadata/get_variable_defs），合理保留；② 截图验证环——用户自己要求的 1:1 质量标准，保留但降频；③ 纯浪费——试错式重试（字体没 loadFontAsync 就 setCharacters、节点 ID 过期、枚举值写错）和重复 metadata 查询。③ 可完全消除，② 可降频，只有 ① 是结构性的。

**How to apply:**
- 一个 section 的全部节点创建/属性设置合并进一次 use_figma 脚本，脚本开头一次性 `loadFontAsync` 所有字体
- 首次 get_metadata/get_variable_defs 后把节点 ID 与 token 记在对话里，不重复查询
- 验证按 section 截图一次，不逐元素截图；发现偏差才下钻
- 写脚本前先读 figma-use skill 的失败模式清单，把常见错误在第一次调用就规避掉，而不是靠报错重试
- 图标用 createNodeFromSvg 一次成型，不手画多次调用（见 [[1to1-ui-replication-method]]）

**跨 call 构建的正确性 + 速度（2026-06-17 复盘补充）：**
- 多 call 构建非默认页时，**每个 use_figma 开头先 `await figma.setCurrentPageAsync(targetPage)` 再 getNodeByIdAsync**——页面上下文每 call 重置，对非当前页节点 getNodeByIdAsync 返回 `null`；早期 call 偶然成功会掩盖这点，后面突然失败（本 session 两次失败 call）
- **getNodeByIdAsync 中途返回 `null` ≈ 节点几乎从不是被删**，而是被 re-parent / 看板模板吸收导致 **id 变了**（本 session 2991→2993）：先 `page.findAll(按 name)` 全子树搜确认存在再继续，**别当"丢了"误报给用户**（本 session 因此发了"它消失了"的错误结论）
- **速度**：首 1–2 个 section 小批验证布局 OK 后，把后续多个 section 合进一次 use_figma、收尾只截一次全图，别每 section 一 call 一截图（本 session ~25 次串行远程往返是慢的主因之一；最大主因仍是建错源白做 ~40%，见 [[source-from-open-file-not-name]]）
