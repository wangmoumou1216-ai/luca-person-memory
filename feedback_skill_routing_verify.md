---
name: skill-routing-verify
description: 路由到任何 skill 前先读其 SKILL.md 核对真实职责；复杂任务必须真正产出 Plan Agent 计划并等确认，不能只读 plan-agent.md 就跳进单个 skill
metadata: 
  node_type: memory
  type: feedback
  originSessionId: c579ee85-28b5-49a5-b351-f23eacc35cd8
  modified: 2026-07-28T02:29:54.175Z
---

路由/选 skill 时遵守两条：

1. **route-guard 关键词命中、CLAUDE.md 路由表标签都只是信号，不是决定。** 在调用任何 skill 之前，先读它的 `SKILL.md` 核对实际职责，再判断是否匹配用户真实需求。不要凭关键词命中 + 自己臆想的功能定义就路由。

2. **复杂任务（命中 Plan Agent 4 条件）：Plan Agent 的职责是「产出阶段计划 + 等用户确认」。** 读了 `plan-agent.md` ≠ 做了 Plan Agent。不得读完就合理化成「先跑某个单 skill」直接执行。

**Why:** 2026-05-27 roam-cards 项目，用户给了一个新产品的原始需求。我(a)读了 plan-agent.md 却没产出计划、直接跳进 /idea；(b)选 /idea 的依据只是关键词「最原始的需求」+ route-guard 高置信，而我给 /idea 套的定义（"需求方向确认/逼问形态"）和它 SKILL.md v2.0 的真实职责（"忠实记录者，不延展不推断不做产品判断"，那是 /brainstorm 的活）完全不符。用户连续两次打断纠正。

**How to apply:** 任何 skill 调用前，先 Read 对应 SKILL.md 的职责声明段，确认它做的事正是用户需要的；复杂任务先把 Phase 计划摆出来等确认，别用"读了规划文档"冒充"做了规划"。关联 [[feedback-no-confirmation-loops]]。

3. **（2026-06-12 复发变体）派发 skill 进 headless subagent 前，除职责外还必须扫它的用户参数。** deepresearch 有 Deep/Moderate 档位（SKILL.md 内 AskUserQuestion 步骤），我没读就派进 subagent，headless 环境问不了人、agent 静默自选了 moderate，用户质问"为什么没让我选，不允许静默处理"，整轮研究补跑 Deep 返工。规则：SKILL.md 里凡有 "ask the user"/AskUserQuestion 的参数，派发前先在主会话问用户、把选择注入任务书——选择权是用户的，默认值不是替他做主的许可。机制已固化进 orchestrator.md §2b【用户参数前置收集】+ deepresearch SKILL.md headless fallback（未注入→BLOCKED）。

4. **（2026-07-22 复发变体）harness 的「自主执行」注入只豁免「问不问」，不豁免「有没有计划」。** 本 session 收到 CRM 三列表改造需求（调研→企划→HTML），route-guard 输出 `MULTI`（deepresearch / figma-layer，明写「必须先问用户、禁止自行判断」），任务本身命中 Plan Agent 三条（≥3 文件、阶段依赖、复杂且新颖）。我拿 system prompt 里的 "user is not watching, asking will block the work" + "Do not use deep-research unless requested" 当豁免，直接开跑 ad-hoc WebSearch，被用户连打断三次质问「为什么没有 plan agent？为什么没有那种路由？」。**判据：自主模式禁止的是"停下来等许可"，而产出结构化计划是干活不是提问，零成本永远可做；用户原话里已包含的诉求（"深入的调研"）本身就构成 skill 的 requested 豁免，不能反过来当"那就别走 skill 链"的借口。** 规则：见到自主模式注入与 route-guard 门冲突时，把冲突拆成两问——「要不要问」按上位指令、「要不要有计划」永远是要；MULTI 多候选时若其中一路明显不适用（本例 figma-layer 是写回 Figma），直接声明排除理由并选定，也不是"不问就等于不判"。

5. **（2026-07-28 复发变体，同族第三次）harness 的行为偏好注入不豁免「这题属不属于某 skill」的语义识别。** luca 问「看一下 pi 这个 agent 的框架结构 / 有什么优势能让 lucagstack 借鉴」，route-guard 输出 `STOP`（词表无命中）。我拿 system prompt 的 "Do not use workflows or deep-research unless the user requested it" 当豁免，跳过整个语义评估直接裸奔 WebSearch，被连打断两次质问「为什么你都没有触发到任何 research？」。**判据：那条注入管的是「别自作主张把小事升级成重型编排」，不是「这题不属于 research 类」——豁免范围扩大化，等于用一条窄注入吃掉整层路由。** 与第 4 条同一病根（拿 harness 注入当豁免）：第 4 条豁免掉「有没有计划」，本条豁免掉「属不属于某 skill」。另一条边界：CLAUDE.md「规则优先级体系」第 2 层是 harness 的**安全/工具约束**，**行为偏好类**注入不属于它，不得压掉第 4 层 route-guard 路由决策。**还要记住：STOP + 零复杂度信号 ≠ 无 skill**——本次实测 route-guard 的 7 个复杂度信号全在「构建轴」，研究/认知类请求 complexityScore 恒 0（框架侧缺口另存 semantic 候选），所以研究类请求的语义兜底**在 hook 层完全没有网**，只能靠我自己。

---

## 并入：feedback_idea_skill_scope（2026-07-02 治理合并，内容原样保留）

`/idea` is a standalone "忠实记录者" scenario: input = existing raw corpus (会议纪要, 语音转文字, 领导想法), output = faithful structuring with no extension/inference/judgment. Its own SKILL.md states it is "与 /brainstorm 独立关系，不是上下游" — so it is NOT an upstream pipeline step.

Do NOT auto-insert `/idea` as Phase 1 when the user is describing a brand-new product idea verbally and wants it shaped/designed. That is `/brainstorm` territory. The design main-path starts at `/brainstorm`.

**Why:** User explicitly corrected this — "idea 是开完会议、会议语料转需求的单独场景…它不在我开发的主路径当中". I had defaulted to idea→brainstorm→… as a linear pipeline, which is wrong.

**How to apply:** When planning a design/build pipeline for a new idea the user is describing live, omit `/idea`. Only reach for `/idea` when the user hands over a transcript/meeting-notes/raw dump they want organized without judgment. Main design path = /brainstorm → /ux-brainstorm → /design-brief → prototype → tech. See [[skill-routing-verify]].

---

## 并入：feedback_research_default_complex_novel（2026-07-02 治理合并，内容原样保留）

规划复杂任务的编排时，**当任务同时【复杂】(命中 Plan Agent 任一触发条件) 且【新颖】(核心机制/交互无成熟先例) 时，研究阶段是默认步骤，不是可选项。** 研究强度按 fact-gap 自适应：广域多源/学术/技术可行性→deepresearch；先例/竞品/UX/行为设计→ux-research；极窄单点事实→web spike。

**绝不静默跳过研究。** 若判断不需要，必须在 Phase 计划里显式写出跳过理由并交用户确认。

**Why:** 在 roam-cards(漫游知识卡片)编排里，我把研究阶段静默砍了，借口是"核心决策都是用户个人偏好、能直接问 + 省 150K 成本"。用户严厉纠正：这是判断错误。对新颖任务，用户的偏好若没有先例垫底，只是"不知道前人踩过哪些坑"的偏好——研究恰恰是去风险处，不是该省处。我还把"决策能从用户问出来"误当成"不需要外部研究"，并用"合规"(规则允许跳过)给一个偏弱的判断做包装。用户明确：复杂/新颖任务 research 是默认动作。

**How to apply:** 任何复杂+新颖任务做 Plan/编排时，默认排入研究节点；只有非新颖/低不确定性才可跳过，且跳过要摆到台面让用户决定。本规则已固化进 `.claude/agents/plan-agent.md`「研究默认门」、`optional-workflow-graph.yaml` `research_default`、`CLAUDE.md`、`AGENTS.md`。另注意：不要为了 momentum 在用户质询计划时还急着往前赶（写产物/抛选项），先停下把质疑想清楚。See [[skill-routing-verify]] 与 idea-skill-scope。

## 索引原文存档（2026-08-15，G5 瘦索引前的完整索引行，逐字保留）

- [Skill 路由纪律](feedback_skill_routing_verify.md) — 调 skill 前先读其 SKILL.md 核对职责+扫用户参数(有档位/AskUserQuestion 必须派发前问用户，禁止 headless 静默选档，2026-06-12复发)；复杂任务必须真正产出 Plan Agent 计划不能只读文档就跳单 skill；**harness「自主执行」注入只豁免"问不问"不豁免"有没有计划"——出计划是干活不是提问，永远零成本可做(7-22 CRM列表改造被打断三次)**；**同族第三次(7-28)：注入 "Do not use deep-research unless requested" 被我扩大成"这题不属于research类"，STOP下裸奔WebSearch被打断两次——注入管的是"别自作主张升重型编排"不是"别走skill"；且 CLAUDE.md 优先级第2层只含harness的安全/工具约束，行为偏好注入不在其列、不得压掉第4层路由；STOP+零复杂度信号≠无skill(实测route-guard 7个复杂度信号全在构建轴，研究类恒0)**；/idea 只做"已有语料忠实结构化"不是新想法入口，设计主路径从 /brainstorm 起；复杂且新颖任务研究为默认动作，跳过须显式声明并经用户确认（并入 feedback_idea_skill_scope、feedback_research_default_complex_novel）
