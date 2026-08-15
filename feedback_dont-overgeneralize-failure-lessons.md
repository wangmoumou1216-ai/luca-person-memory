---
name: dont-overgeneralize-failure-lessons
description: 失败教训要归因到真实根因（哪条通道/机制坏了），不得泛化成全称禁令砍掉用户想要的合法选项
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 45c656f8-1c82-4540-966b-f953f88011be
  modified: 2026-07-21T04:10:09.557Z
---

把单一失败模式过度泛化成全称禁令，会砍掉用户明确想要的合法选项，被 luca 直接驳回。

**Why:** 2026-07-21 designer-skills 适配方案。实证发现"三个已装外部知识件全休眠"后，我把教训泛化成「知识类一律不建独立 skill，只做 references 吸收」写进方案总原则。luca 驳回：「我不觉得你没有新增 skill 这个方案是对的」。回查我自己的调研数据就能证伪这条禁令——同批安装的 4 个外部工程 skill（systematic-debugging/tdd 等）走窄词路由，红队裁定"真打通"活跃在用。休眠的真实根因是 **rules.yaml-advisory 这条弱通道**（无路由无门禁→靠自觉→衰减），不是"是个独立 skill"。我把根因（通道弱）错贴到表象（skill 形态）上，禁令看似谨慎实则错杀。

**How to apply:**
- 从失败案例提炼规则时，先问「坏的到底是哪个机制/通道」，规则贴着根因写，不贴表象类别写。
- 写出全称禁令（"一律不/永不/零X"）前，主动找自己证据里的反例——有一个活反例（如工程四件）禁令即不成立，改写成条件判据（本案：使用形态判据——交付物生产者→skill，流程底料→references）。
- 与 [[feedback_redteam-own-analysis-before-shipping]]（over-claim 家族）同源：那条管"说问题存在"的过度声称，本条管"从问题推规则"的过度泛化。
