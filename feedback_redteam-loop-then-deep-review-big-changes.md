---
name: redteam-loop-then-deep-review-big-changes
description: 大/有风险的框架改动——执行前跑红队↔plan 握手循环(迭代到无存活BLOCKER/缺陷MAJOR才动手)，实现后开多个独立 sub-agent 深度对抗 review + mutation 级测试、命中的测试项全解决才算 done
metadata: 
  node_type: memory
  type: feedback
  originSessionId: fff6b43e-1a36-4396-9819-aa8fd7c5feaf
  modified: 2026-08-04T11:29:05.176Z
---

luca 对**大的 / 有风险的框架改动**明确要两道对抗关（2026-07-24 一个 session 内两次指示）：

1. **执行前：红队↔plan 握手循环**——出计划后开红队模式自我对抗；逐轮开独立红队（default-REFUTE、
   冷启动只见计划+真实代码）攻击当前计划，我逐条判 valid 即改计划、invalid 附证据反驳，
   **迭代到某轮无存活 BLOCKER/缺陷类 MAJOR = 握手**，才执行。不是"一次 redteam 就完"，是**循环到收敛**。
2. **实现后：多 sub-agent 深度 review + 深度测试**——开不同独立 sub-agent 各攻一维（如 script/安全、
   test/wiring、docs/plan 一致），**mutation-test 证明测试非 tautology**（把代码改坏看测试是否转红），
   命中的每个测试项/缺口都解决，确认最终态无问题才 done。

**Why:** 本 session 实证有效——4 轮红队把一个 over-built 方案逼成诚实最小方案（v1→v5），反复抓到我把
Claude-纪律/判断 dressed up 成"结构性/规则/最小"（= [[feedback_redteam-own-analysis-before-shipping]] 的
over-claim 病在设计层复发）；实现后 3 个 reviewer 又抓出 CI 门缺口 / 转义覆盖缺口 / 大写 scheme 误拒——
单靠我自审都漏了。对抗 + 独立验证是 de-risk 的承重手段、不是仪式。

**How to apply:** 大/风险改动别走"计划→直接干→宣布做完"。计划后主动进红队循环（平凡任务豁免）；
实现后主动开独立 review sub-agent + mutation 测试，别等 luca 催。与 [[feedback_verify-your-verification]]
（验证者须独立于修复者）、feedback_redteam-socratic-before-plan-entry 同族——本条把"迭代到握手"与
"实现后深审到无问题"补全为显式两关。

强化(2026-07-30 侧栏交付面方案): luca 明确指示**方案级评审同样走握手 loop，且设上限**（「loop
最多两轮，及时收口」）。关键补丁——**握手必须对最终版本闭合**: 第 2 轮 PASS-FINAL 后我又按评审
备忘改了一笔，luca 两次追问「你们最后是否握手」点出缺口→把终版发回评审方拿到显式
CONFIRM-HANDSHAKE 才算数。评审后的任何改动（包括按评审自己的备忘改）都要发回确认，否则
"握手"只覆盖到倒数第二版。

**复发实证 + 触发面扩宽(2026-08-04，luca「不需要review一遍吗」当场拦下我的收工 commit)：本条不限
"框架改动"——凡我写了实质代码要交付（本次=muse app 两个 feature commit，已 dist 部署进他日用 app），
实现后深审就是默认动作，不是等他开口的可选项。** 那次我只跑了自己写的单测 + dev 冒烟就要标"已执行"
收尾；他一句话拦下后派 3 个独立评审官（正确性/安全/运行时真跑），当场翻出**自测结构上测不到**的三类：
① **招牌功能确定性不触发**——节流闸在 tool_use 时先烧掉配额，0.15s 后的 tool_result 被自己饿死；我只
在 Edit 路径手验过（间隔 2.5-3.7s 侥幸通过），Write 路径是死的：**"我验过"常常等于"我只走了侥幸的
那条路径"，覆盖面要按分支枚举不能按印象**。② **核心用法静默吞数据**——切走再切回时在飞 subagent 不
补发分组事件、其后输出全被丢弃；新会话测不出来，只有"已有状态"下才复现。③ **一行畸形输入崩整个
app**（异常穿 setInterval 无人接，真实 294MB 数据零发生 = 廉价保险而非救火，零发生率不是放行理由）。
安全面判 SHIP，说明**三维分派是对的**——不是找个 agent 泛泛看一遍，是按正确性/安全/运行时真跑分维，
且必含真跑维（同族 [[feedback_audit-needs-runtime-partition]]：纯静态视角集体漏运行时缺陷）。
修完还要**变异测试验新断言的牙口**（把修复点逐个改回缺陷态看是否转红）+ **派不知情验证官核修复**
（只给原始 finding、不告诉它怎么改的）。附带一条工程口径：**评审后改了代码，已部署的制品就过期了**
——重新 dist 部署前必须告诉 luca "先别测"，否则他测的是带缺陷的旧包（同族
[[feedback_deliver-into-daily-driver-app]]）。

强化(2026-08-03，luca 明确指示「红队的质疑是需要对抗的，不是一味认同」): **红队是被我审的，
不是审我的**——它的输出是证据材料不是判决书，判据永远是证据强度、不是谁提的（我不因"这是我的
计划"替它辩护，也不因"这是独立红队"照单全收）。三条可操作补丁：
① **default-REFUTE 是故意偏置，天然产 false positive**——默认否定的岗位有交差压力，会把"我看着
不顺眼"包装成"这会导致坏结果 X"；所以派单时就设硬门槛（file:line + 原文引用 + 具体触发场景），
门槛的用途是**让我能逐条验它**。
② **红队复述我自己写进 prompt 的怀疑 ≠ 独立证据**——我在派单时标出的攻击面，红队若只换个说法
还回来、没真跑命令、没一手输出，那是回声不是发现，按"未验证"处理、我自己去验（同族：
[[feedback_redteam-own-analysis-before-shipping]] 的"承重结论不得只建在 subagent 转述上"）。
③ **三分类归档且驳回要留痕**：真缺陷→改 plan 并记谁攻掉的；红队错了→**举证驳回 + 写明驳回理由
和我核验的一手证据**（不是我说了算就算）；真分歧（双方都有据、判断依赖价值取向）→ 停下来升
luca 裁，不自行拍板。

## 索引原文存档（2026-08-15，G5 瘦索引前的完整索引行，逐字保留）

- [大改动:红队循环+实现后深审](feedback_redteam-loop-then-deep-review-big-changes.md) — 大/有风险框架改动执行前跑红队↔plan 握手循环(逐轮独立红队 default-REFUTE 攻击、迭代到无存活BLOCKER/缺陷MAJOR才动手)，实现后开多独立 sub-agent 各攻一维深审+mutation测试(改坏代码看测试转红否)、命中测试项全解决才 done(7-24 两次指示;本session 4轮红队把 over-built 逼成诚实最小 v1→v5、3 reviewer 又各抓一维缺口)；**8-04 复发扩面：不限框架改动——凡写实质代码要交付，实现后深审即默认动作不等他开口(「不需要review一遍吗」当场拦下我的收工)；按正确性/安全/运行时真跑三维分派且必含真跑维，翻出自测结构上测不到的三类：招牌功能确定性不触发(我只走了侥幸路径)、已有状态下才复现的静默吞数据、一行畸形输入崩全app；修完还要变异测试验断言牙口+派不知情验证官核修复；评审后改了码=已部署制品过期，重新部署前须告诉 luca「先别测」**（并 [[feedback_verify-your-verification]]、feedback_redteam-socratic-before-plan-entry、[[feedback_redteam-own-analysis-before-shipping]]、[[feedback_audit-needs-runtime-partition]]）
