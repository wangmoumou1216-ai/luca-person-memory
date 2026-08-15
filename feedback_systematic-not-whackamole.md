---
name: systematic-not-whackamole
description: 有状态系统别打地鼠点修；建模成管线/状态机→枚举失败模式闭合矩阵→区分性用例逐格验→未验标GAP→固化进项目测试框架
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 79705b29-d520-4e9d-917b-e04b554becdf
---

排查有状态系统（提示器/同步/缓存/状态机）的问题，**不要"哪疼治哪"的反应式点修**——那会无限漏网，用户每次都能再找出一个。

**Why:** 2026-06-01~02 修 CLI 提示器，我一直点修(消费者分歧→修、死flag→修、TTL→修/回退、#4空闲误报→修)，每次说"done"用户都能逮到下一个。而且我手写的 bash 验证脚本**四次** harness bug(传函数当命令/目录名带后缀/选了死pid×2)，靠"区分性用例"纪律才没报假绿。

**How to apply（系统性排查五步）:**
1. **建模**：画出阶段管线。提示器=`Writer(事件→flag) → State(flag) → Reader×2(flag→显示) → Clearer(覆盖/抑制/GC/TTL)`。
2. **枚举失败类**：每阶段只有 4 类错——①误报 ②漏报 ③两端不一致 ④清不掉/清错。逐阶段×逐类列格。
3. **闭合矩阵逐格验**：用**真实组件**驱动每格(真 hook-write、真两个 reader 二进制)，期望值要有**区分性**(不能全 idle/全 pass，否则疑似空跑)。
4. **未验明确标 GAP**：驱动不了的格子写出来，**绝不默认"已修"**。本次留 3 GAP(jsonl-watcher 慢工具误报#3、post-approval 窗口、host-app 归属)。
5. **固化**：把矩阵搬进项目**自带测试框架**(bats/pytest)，别再用一次性脚本——脚本本身会 bug。本次新增 `notification_denoise.bats`，全套 36/36。

相关 [[verify-your-verification]]、[[run-tests-before-claiming-done]]。
