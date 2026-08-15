---
name: feedback-project-entry-orient
description: 切到任何项目后，先主动摸清结构与内容再做事，别让用户重复解释项目是什么
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 79705b29-d520-4e9d-917b-e04b554becdf
---

切到任何项目后，**立即主动建立对该项目结构和内容的理解**，再回应需求——不要只看 luca_gstack 的 workflow-state 快照就问「你想做什么」。很多项目(如 ai-pet-prompt-structure)是外部开发的真实代码库，luca_gstack workflow-state 全 PENDING 是假象。

**Why:** 用户已两次因为我进了项目却不知道它是什么、找不到需求而明确不满(2026-06-01)。让用户重新解释「这个项目是啥」既浪费他时间，也显得我没进入状态。2026-06-04 又复发一次：切到「快速语音录入」后我只看了 luca_gstack 注入的 `docs/` 软链(空，只有空 handoff/)就断言"项目是空的"，用户立刻反驳"明明应该有内容"——真实内容(HANDOFF.md / README.md / src-tauri/ / src-ui/ 一个完整 Tauri 应用)全在项目根，根本不在 docs/ 下。

**How to apply:** `project.sh switch` 之后，**第一步永远是 `ls` 项目根**(不是 docs/)+ 读关键文档(README / HANDOFF.md / docs/STATUS.md / PROGRESS.md / CLAUDE.md)+ 扫一眼在跑的相关进程，然后用一句话回报「这个项目是 X，结构是 Y，当前状态 Z」，再进入用户的具体诉求。**陷阱：luca_gstack 的 `docs/`、`workflow-state.yaml`、`current-topic.txt` 三个软链是给 skill 产出用的，对手写代码项目本来就空——它们为空 ≠ 项目为空，绝不能据此下"空项目"结论。** 项目专属结构尽量写成 project 记忆，下次进来直接命中。

**升级(2026-06-11, shareclawdemo)：** 任务是**改大型陌生代码库**时，"读完记忆文件/README"不够。用户质疑「你这么快就全都读完了？」并明确要求「框架你一定要理解，不然增删内容会出问题」。正确动作：① 如实区分"逐行读了什么 / 没读什么"，别让"读完了"被理解成通读全貌；② 改动前做**框架级落实**——提取骨架(文件解剖/状态对象/函数清单)、跨文件 diff 验证共享区是否漂移、核对持久化与路由契约，并把验证结果汇报给用户拍板。声称理解 ≠ 验证过的理解。
