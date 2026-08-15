---
name: source-from-open-file-not-name
description: "OD/Figma 等多文件工具里，源产物按\"用户当前打开/active context\"定位，别从目标名反推项目"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6bd007e4-b052-4905-b569-fd35e2acbdf5
---

用户说「我在 OD 生成了个 mobile html，写进 Figma 这个地址」时，我从**目标 Figma 文件名**（"速记汇报版本"）反推 OD 源项目，命中了同名的 `suji-biz-insight`，照它的 index.html 建了 ~7 个 use_figma 节点；用户打断纠正：他要的是另一个项目 `会议总结一页纸 / mobile.html`（当时正开在 OD 里）。删档返工重建。

**Why:** 目标（写到哪）和源（拿哪个产物）是两件事；目标文件名和某个 OD 项目同名是巧合，不是源的真值。而且一个 OD 项目里常有多个 html（index/v1/claude/mobile…），名字相近，靠猜必错。`get_active_context` 失效（>5min）时更要小心，不能用名字脑补。

**How to apply:** 多文件设计工具（OD/Figma/Canva…）取"用户刚生成/正打开的那个产物"时：① 先用 active context / 用户截图里高亮的 tab 锁定项目+文件；② active 失效或同项目多候选 html 时，**一句话报出我要用的具体文件名让用户确认**（"用 会议总结一页纸/mobile.html，对吗？"）再动手；③ 绝不用目标地址的名字反推源项目。与 [[feedback_skill_routing_verify]]（动手前核对+扫参数）、feedback_reproduce-real-env-not-approximation（用真实而非近似）同源。
