---
name: dont-shrink-existing-logic-without-approval
description: 改造/迁移/适配 luca 的系统时不许缩减任何既有逻辑；撞到平台限制先穷尽配置与适配手段，实在不行提出来让他裁决，绝不自行削减
metadata: 
  node_type: memory
  type: feedback
  originSessionId: e9ed9da7-ac6e-4acf-8833-9f8b8ec59865
  modified: 2026-08-05T01:46:30.749Z
---

luca 2026-08-04 明确指示：**「你不要缩减我现在框架的任何逻辑。有问题你提出来，不要在不经过我的允许缩减」**——
说这句时我正要做的事恰好是缩减：迁移 luca_gstack 到 Codex，为适配 `additionalContext` 的
~2500 token 上限，我计划"在 Codex 下裁剪 session-restore 的启动注入输出"。

**Why:** 迁移/适配场景天然诱导缩减——目标平台总有能力缺口，而"砍掉装不下的那部分"是最省事的收口方式，
还容易被包装成"合理降级"。但那是拿他的既有能力去补我的实现便利。他被拦下的不是判断错误，是**没先穷尽
不缩减的路径**：重新核实后发现 `additionalContextLimit` 是每-hook 可配参数、**设 0 就是完整直传不截断**，
超限也不是丢弃而是存盘+预览——问题根本不存在，裁剪纯属自造。这与 [[feedback-adoption-ambition]] 的母原则
同源：撞约束时分离两问「这事净值正吗 / 怎么做不撞它」，别让第二问冒充第一问。

**How to apply:**
1. **缩减是需要授权的动作，不是实现细节。** 任何"在 X 平台下关掉/裁掉/降级 Y"的念头，先当成待裁决项，
   不当成设计自由度。
2. **先穷尽三层不缩减路径**，顺序固定：① 目标平台有没有配置参数直接解决（本次 `additionalContextLimit: 0`）
   → ② 能不能加**适配层**把差异收敛到一个新文件，让既有代码零改动（本次 `.codex/codex-hook-adapter.mjs`：
   入向 `apply_patch→Write` 归一化 + 出向 `decision:block→continue:false` 翻译，6 个 hook 一个字节没动）
   → ③ 三层都不通，才带着证据提出来让他裁决。
3. **改法优先级：加法 > 翻译 > 改写 > 删减。** 修既有模块时只增字段不改旧值（本次 harness.mjs 保留
   `blockVerb` 等旧字段语义原样，新能力全走新增字段，13 条旧断言全绿）。
4. **留零回归对照断言**证明没动到原路径——不是自称没动，是测出来没动（本次 E1/E2 专测 Claude 直调行为逐字不变）。
5. 真是目标平台的硬限制（本次 `updatedInput` 被 Codex 运行时显式拒绝，openai/codex#18491 OPEN）→
   **如实说明是平台限制不是我的选择**，并让失效方向偏向保住强制（降级为 deny 而非静默放行）。

同族：[[feedback_surface-buried-value-before-deleting]]（删资产前先挖价值）、
[[feedback_dont-constrain-model-capability]]（净效果看撤掉的硬约束是否多于新增）、
[[feedback_disclosure-is-not-remediation]]（"我知道我在缩减"不是缩减的许可证）。
