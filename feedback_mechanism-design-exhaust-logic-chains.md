---
name: mechanism-design-exhaust-logic-chains
description: 框架/机制类设计（骨架）必须穷尽「场景维度矩阵」并给出深度确认的胜出方案——不能只回答用户举的那个例子，也不能罗列选项让用户挑
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 8c2905ee-8c16-4c1c-8ee4-ca0ae5994d11
---

luca 对机制/框架类设计（他称「骨架」）的验收标准远高于普通功能修复（「A 只是问题，B 是骨架……
它才是核心所在」，2026-07-17 源头修复晋升机制评审时打回首版计划）。

**Why：** 机制一旦立法就约束今后所有 session 的行为；漏掉一条逻辑链（如「在项目里发现框架根因，
是跳出项目去修吗？」）就是把未定义行为埋进宪法。他举例子只是抽查，期待的是我已把例子所在的
整个维度空间铺满。

**How to apply：** 设计机制时——① 找出决定行为的正交维度（如 根因层 × 执行上下文），做成**处置
矩阵把每格定死**，用户的例子只是矩阵中一格；② 每个关键选择列**败选方案与败因**，交付的是深度
确认后的胜出方案，不是选项菜单；③ 与既有纪律逐条对账（never-switch/单真值源/处置默认/预算门），
新机制是它们的泛化而非平行体系；④ 举一反三是验收线：被问到矩阵内任何一格都必须已有答案。
相关：[[feedback_premise_first_deep_eval]]、[[feedback_autonomous-execution-but-think-before-complex-ui-work]]。
