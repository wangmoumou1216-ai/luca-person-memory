---
name: verify-runtime-not-spec
description: "别把\"设计文档规定\"当成\"系统实际行为\"讲，更别对没亲自观测过的运行时行为打包票（\"没问题/扎实/保证\"）"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 46fb52a9-e6b9-40fa-ba4b-3522084e6a34
  modified: 2026-08-05T07:11:19.206Z
---

回答"这个能不能用 / 有没有问题"时，先把结论按证据分三层，别混为一谈：
① **架构事实**（已验证或架构上必然，如"主循环模型框架切不动"）；
② **能力存在**（schema/接口层面有，但没观测过实际生效，如"Agent 工具有 model 参数"）；
③ **纯约定 / 未观测**（只有文档规定应该这样，没有运行时强制，我也没跑过）。
第③层**禁止**用"没问题 / 扎实 / 保证 / 真的能"这类确信词。

**Why:** 2026-07-10，luca 追问模型动态路由。我全程只读了 `model-routing.yaml`、
`plan-agent.md` 等设计文档，就断言"委派给 spawned subagent 层真没问题、扎实"。实际一查
（`settings.json` 无 Agent/Task 的 PreToolUse hook、全仓 grep 无 model 注入、
`daily_governance.check_model_routing` 只核文档↔文档一致性）——整套路由是**纯约定、零运行时强制**，
靠编排 agent 每次手动传参、漏传即静默回退、无任何运行时检查兜底。我把"文档说应该这样"包装成了
"系统就是这样"，还附赠了打包票。**这类"靠自觉、无兜底、静默回退"的东西恰恰最容易悄悄坏**，
而我的错误只因 luca 自己做了实验才暴露——否则我会一直保证下去。

**再现（2026-07-11，同一病根，muse-x-digest）：** 我照 skill 的恢复配方（只拼 `blocks[].text`）取 X 长文，
就写了"完整交付"，还对没读到的内嵌块声明"不影响理解"。luca 一句"你读全了吗"逼我复核——`atomic` 块
`.text` 恒空、真实代码块内容在 `entityMap`，配方静默漏了全文核心的 7 个 /goal·/loop 命令模板。又是
"信 happy-path 配方 = 当已读全"，且**完整性正是该 skill 的头号红线**。返工补齐 + 修了 recovery.md 配方。

**再现（2026-07-21，同一病根的能力评估变体，designer-skills 评估）：** 评外部 skill 库时，我拿
INTEGRATION-MAP"已安装+已打通"的纸面记录当活能力，据此判外部库"与 UI-UX-Pro-Max 冗余"。luca 一句
"我一次也没用过它，我的 flow 和路由映射不到它"驳回整个结论。一查地面真相：它不在路由表、只有 advisory
规则注入、无 gate 无强制——**installed ≠ live**。判"冗余/已覆盖"的基准必须是**真实会被用上的能力**
（有 enforced 缝隙：路由命中 / SKILL.md 强制 load / blocking gate），纸面集成记录只是第②层"能力存在"。
整轮评估按真实运行链重做。

**再现（2026-08-05，同一病根最隐蔽的变体：工具自己的报错也不是运行时真相）：** 迁移 luca_gstack
到 Codex 时要给档位定 reasoning effort。我用一个非法值试探配置，`codex` 报错**明确列出了合法枚举**
（`none/minimal/low/medium/high/xhigh`），我就照它定档，把 mechanical 档定成 `minimal`。
真跑才发现：**模型返回 400** —— "'minimal' is not supported with the 'gpt-5.6-sol-…' model.
Supported values are: 'none','low','medium','high','xhigh','max'"。**配置解析器接受的是超集**，
与模型接受的集合既不相等也不包含（它多 minimal、少 max）。后果：preflight-agent 定档 minimal
会**每次调用都失败**，且失败是静默 falsy，表现为"产出莫名变空"。
**为什么这条最难防**：前几次错在"信文档/信配方/信纸面记录"，还能提醒自己去查；这次的来源是
**被测系统自己吐的错误信息、还主动枚举了合法值**——看起来权威到没有怀疑的理由。但校验层
（config parser / schema 校验 / 类型定义）和执行层（真实模型/服务端）是**两个**系统，
前者的"合法"只保证能通过它自己那关。同轮还有第二例：OpenAI 结构化输出的 strict 要求
（每层 `required` 须列全 properties）在任何配置校验里都看不出来，只有真调用才 400。

**How to apply:**
- **枚举 / 取值范围 / 能力集，一律以"实际调用成功"为准，不以校验层信息为准**——包括工具自己的
  报错、`--help`、类型定义、JSON Schema。它们描述的是"能通过校验"，不是"能跑通"。
  低成本做法：把候选值**逐个真跑一次**（本次两个值各跑一次就定案了）。
- 拿到一份"合法值清单"时问一句：**这是谁在告诉我合法？是最终执行者吗？** 不是 → 只当候选，仍需实跑。
- 把实测结论**连同被证伪的来源**一起钉进真值源（本次在 model-routing.yaml 记
  `effort_rejected_by_model: [minimal]` 并加断言守护），否则下一个人照样会去看那条报错。
- 被问"能不能用/有没有问题"时，若我只读过 spec 没观测过运行时 → 明说"这是文档规定，我没实测过"，
  不下确信结论。
- 有地面真相持有者（用户刚实验过）→ 先问"你观测到什么"对齐，而不是继续从文档推理。
- 能实测就实测（spawn 探针 / 实跑一次 / 读回真实状态），把"我猜"换成"我看到"。
- **声明"完整/全部/无遗漏"前，先枚举源格式的节点类型、逐类确认都解析了**（容器/atomic/by-reference 节点
  常把内容放在旁挂表里，happy-path 抽取会静默丢）；绝不对没亲自读到的内容写"不影响理解"。
- 相关：spec-vs-behavior-gap
