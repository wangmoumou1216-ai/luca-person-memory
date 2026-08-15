---
name: no-auto-edit-global-claude-config
description: 不要自动修改全局 ~/.claude/settings.json 等影响所有 session 的配置；改成给可粘贴片段让用户自己应用
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 79705b29-d520-4e9d-917b-e04b554becdf
---

涉及修改全局 `~/.claude/settings.json`（hooks/permissions/env 等会影响用户**所有** Claude session 的配置）时，**不要直接用脚本自动改**——即使用户先前同意了包含该项的大范围方案。改成：把要加的片段以可复制的形式给用户，由他自己粘贴/确认。

**Why:** 2026-06-01 修 roam/宠物提示器时，用户批准了"连 writer 一起根治"（含改 settings.json），但当我真要用 python 脚本写 `~/.claude/settings.json` 时他当场拒绝了该工具调用。全局配置会即时影响当前正在运行的这个 session，blast radius 大，用户要自己掌控。

**How to apply:** 项目自身代码 / 文档模板可以直接改；一旦动到 `~/.claude/` 下的全局配置，先停下给片段+说明，让用户自行应用，不要 Bash 自动写入。项目本地的 `settings.json`（仓库内）一般可改，但全局家目录配置要先问。
