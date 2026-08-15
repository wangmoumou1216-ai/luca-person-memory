---
name: feedback_deliver-into-daily-driver-app
description: "给 luca 加的任何能力，默认必须落到他日常真正在用的那份制品里（不是停在 dev 源码/测试实例/提交）；不主动部署 = 对他没意义。"
metadata:
  node_type: memory
  type: feedback
---

luca 明确（2026-07-10，muse X「提取翻译」功能）：**「我所有 app 上面加的能力，肯定都是我想用上的。如果你不安装到我日常应用的 app 上，是不是就没意义了。」** ——他说得对。**交付的定义不是"代码写完 / 测通 / 提交了"，而是"这个能力已经在 luca 日常真正在用的那份制品里可用"。**

**Why:** 能力只活在开发源码 / 测试实例 / git 提交里，luca 用不上——他日常运行的是**打包安装的产物**（如 `/Applications/luca.app`），不是 `~/Desktop/项目/.../app` 的 dev 源。停在"跑 dev 实例测通 + commit"是**半成品交付**，需要他追着说"我肯定跑日常 app"才补最后一步——这一步本该是默认动作。本 session 我就犯了这个缺口。

**How to apply:**
1. 给任何 luca **日常使用的应用/产物**加能力，默认动作里**包含"部署到他日常用的那份制品"**，不等他开口要求。
2. 声明 done 前自问一句：**「这个能力现在在 luca 日常用的东西里，能用吗？」** 不能 → 还没交付完。
3. 形态映射：Electron 桌面 app = `npm run dist` 打包 + 替换安装版（`ditto` 覆盖 `/Applications/xxx.app`，先 `mv` 旧的到 `.bak` 可回滚 + `xattr -cr` 清 quarantine）；网页/服务 = 部署到他实际访问的那份；脚本/CLI = 装到他 PATH 里那份。总原则：落到"他真正会打开/运行的那一份"。
4. 具体到 muse app 的机械步骤见项目本地记忆 deliver-feature-into-installed-luca-app（muse 项目本地记忆，--layer project 可检索）。
