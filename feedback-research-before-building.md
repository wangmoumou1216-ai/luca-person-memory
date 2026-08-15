---
name: feedback-research-before-building
description: "造能力/skill 前先深度思考设计 + 调研 GitHub 现有做法评估借用,再围绕真实目标搭;单一用途能力保持专属不过度通用"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 053ab9b9-73c6-4f07-a983-a1ea4af27eee
---

搭一个新 skill/能力前，不要从"一段提示词"直接开写。先**深度思考这个能力该怎么处理**，
并**调研 GitHub 上同类现有做法、评估哪些适配本场景、哪些可借用**，再**围绕 luca 的真实目标**
去设计。**单一用途的能力保持专属、不过度通用化**（如某个能力只服务某个 app，就 app-only，
不进通用 skill 集、不进用户路由）。

**Why:** luca 明确指示（2026-07-11，muse X-digest skill）——他嫌"一段提示词处理不好"，要求
"深度思考 + 搜 github 评估现有 skill + 围绕我的目标 + 只服务我这个 app"。事实证明调研有高回报：
GitHub 调研直接挖出 FxTwitter/FxEmbed 无 key 恢复通道（解决了拍脑袋方案里的登录墙难题）+
可逐字借用的翻译/按需披露 prompt 模式。

**How to apply:** 收到"做个 skill/能力"类需求 → ①先深度思考设计（不急着写）②spawn 调研
agent 搜 GitHub 同类 prior art、按本场景 fit 评估、标出可借用点与反模式 ③围绕用户真实目标定型
④若只服务某项目/app 则明确 scope 专属、不通用化。参见 [[verify-runtime-not-spec]]。
