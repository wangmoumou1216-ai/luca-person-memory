---
name: feedback_redteam-own-analysis-before-shipping
description: "框架/系统自审分析先红队自己的结论再上报；过度判定\"要现在修的bug/改进点\"是反复出现的失败模式"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 2606afc2-9cfb-4a46-b0e8-eb377fb5ebac
  modified: 2026-08-03T07:11:46.376Z
---

做 luca 框架 / 可观测层这类**自审分析**时，我系统性地把"空状态 / 失配 / 缺口"判成"要现在修的 bug 或改进点"，而多轮证据接地红队**稳定翻案**：2026-06-02 对自己 9 条结论 → 4 推翻 5 修正、0 原样成立；2026-06-01 单轮红队判 11 假 gap，3 轮辩论翻 6；2026-06-11 耗时审计 7 条 → 2 存活 4 修正 1 撤回（C6 方向性错误）。

**模式延伸（2026-06-12 复发确认）：** over-claim 不止发生在"上报的结论"，也发生在**顺手提出的"下一步候选"**——我对 plan-agent 拆分说"可以做，低风险"，取证后发现其模板使用时机=出计划时刻，拆分零收益；对 handoff 分级说"动的是耗时第一名"，实际 §2c-obs/episodic 早已条件化。3 个候选四问取证后仅 1 个成立且需重定性。

**复发（2026-06-16，单 session 内翻 3 次）：** OD headless 出图诊断我连续 over-claim 三个根因——先 `desktopAuthGateActive`（红鲱鱼）、再"额度耗尽必须重登"、再"额度耗尽是计费分流根因"，每次都被用户当场质疑（"你在干什么" /"为什么我现在能用 OD"）才回头。最后真因是**复现保真度**：我手工 `env -i HOME PATH` 剥环境复现，漏了真实系统会传的 `USER`，造出假的 credit 错并据此改了 6 个文件，再全数勘误。教训独立成条 → feedback_reproduce-real-env-not-approximation。

**新方向（2026-06-19，研究情报官补跑 QA）：漏判真违规和过判假 bug 是同一个病——不跑代码。** correctness+redline 两维对抗 QA 中，redline review-agent 凭"工具输出格式与 grounding 计分行正交"**推断**grounding 红线 HELD（8/8 全过）；同时跑的 correctness agent 跑 `groundCheck` 抓到真逃逸——闸门拿混入 read_url/search_web 外部文本的 `observed` 做子串匹配、没用仅计分行白名单 `allowed`，一个 HIGH 红线违规差点以"8/8 HELD"出门。教训：合规/红线审查靠"推断设计"在**两个方向都不可靠**（既会 over-claim 假 bug，也会**漏判真违规**）；同一不变量必须有跑代码的一维，多 agent 评审里"跑了代码的"压"只推理的"。本次也复证我自己的纪律：对 3 个 HIGH 亲自读代码核实后才动手、不盲信 agent。

**复发+新变体（2026-07-10，lucagstack 后置流程评估）：over-claim 的新载体是 subagent 转述。** 评估"后置代码阶段是否面面俱到"时，v1 三处承重论断（"TEST-NNN 从不被执行"、"OpenSpec 根本没有验收能力"、"evals 未接线"）全部建立在探查 subagent 的总结转述上，未直读 plan-agent/quality-gate/orchestrator 原文；用户点名"结论草率"后复审直读，三条全被推翻或证明夸大（DEV 反向覆盖 CRITICAL 门、每 Phase 强制 quality-gate、OpenSpec `/opsx:verify` 都真实存在），连带一条架构建议错位（推荐 fork-only 的 muse-proto-judge 做母版补强）。教训：**subagent 报告只能当线索/索引，承重结论必须自己直读一手文件后才可出口**——subagent"读过"不等于我验证过；越是要下"X 不存在/从不发生"这类全称否定判断，越必须直读。

**复发+升级（2026-08-03，能力可达性治理 plan 被四路红队全判 BLOCKER）：直读一手文件也不够——否定性断言要定向证伪。**
上一条（07-10）的结论是"承重结论必须自己直读一手文件"。这次我**直读了**，三条承重的**否定性断言**仍然全错：
①"handoff-review 要三件套 AND、设计得跑不起来"→ 实际 Phase 0 是多选、场景 B 自动隐藏第三节、单节 BLOCKED
自带「跳过此节继续」出口，今天就能跑；②"ux-audit Module A/C 既有『无法判断』出口"→ `grep -c 无法
module-a-visual.md` = **0**（那是 Module B 独有），而 A 是扣分制 `100-(P0×20+…)`，解门后"看不见"=找不到问题=**100 分**；
③"redteam 产出契约要求一件不会发生的事"→ 落盘契约**我自己几小时前刚改好**（同日 commit），写 plan 时没算进去。
根因一句话：**否定性断言（X 不存在 / 没有出口 / 没有出处）的举证标准高于肯定性断言，而我用了肯定性断言的标准
去支撑它**——通读建立的是"我没读到"，不是"它不存在"；单一工具信号也不行（同轮 `git blame` 陷阱：仓库若是
一次性 import，`^<root-hash>` 是 85–96% 的基准率、零区分度，我却拿"blame 到 baseline"当"无事故出处"的证据，
而"有出处"那一侧我查的是 rules.yaml/正文——**两侧用了两套不同方法**，分类因此不可比）。

**同 session 内立即复发（同日，写完上面这段之后）：选择性引用同一份文件。** 判 `/auto` 该降级时，
我引 `plan-agent.md:46`「另一半成因是 route-guard 的 HEAVY_ORCHESTRATOR_SKILLS，**已同期修复**」
支持"修过两次仍零使用 → 是能力限制问题"；而**同一文件 28 行后的 `:74` 明写**「PLAN_CHECK 双保险
保留——ROUTE_GUARD_HEAVY_SKILLS 仍升 PLAN_CHECK」，`settings.json:4` 至今仍是 `"auto,muse-loop-orchestrate"`。
红队实跑 route-guard：三条 auto 触发词**每次都被改写成"读 plan-agent.md 输出 Phase 计划"，一次都没出现
"调用 /auto"**——完整机械的零使用解释，与"限制能力"无关。**这不是没读文件，是读了之后停在支持自己的
那一行**；而且我的处置还把"唯一没做过的最小干预（仅从 HEAVY 移除 auto）"和"删掉全部登记面"捆绑，
执行完这个问题就永远不可回答。补第 ④ 条：**引用某一行原文支持自己的论点时，先把它所在的完整段落
（及相邻小节）读完再引，并对该行做一次反向搜索——"有没有另一处推翻它"**；涉及"某机制已修复/已失效"
的断言，一律以**真跑一次**为准，不以文档自述为准（同族：[[verify-runtime-not-spec.md]]）。

**How to apply（三条可操作）：** ①出口前把自己的**否定性断言**单独挑出来列一张清单，每条配一次**定向 grep/命令**
（"没有 X"→ `grep -c X`，"没被引用"→ `grep -rln`，"从没发生"→ 查日志/episodic），拿到 0 或空才算数；
②同一分类的两侧必须用**同一种方法**取证，否则分类不可比（本案正解：两侧都做五源穷尽检索 rules.yaml /
promoted-facts / episodic / framework-audit / skill-invariants，而不是一侧查文档一侧查 blame）；
③把**本 session 自己刚改过的文件**当作已知会失效的记忆——写结论前 `git log --oneline -5` 看一眼今天动过什么。

**Why:** 单轮分析对"自己的结论"缺乏对抗，会 (1) 过度 over-claim 行动项；(2) 忽略目标是被*有意*冻结 / deferred 的（撞 README freeze / test-lock）；(3) 甚至编造不存在的连接（2026-06-02 的 C5 凭空造了一条 gate_findings→propose_semantic 管线，grep 即证伪）。

**How to apply:** 框架自审 / 改进类任务，**上报前先红队自己的结论**（≥1 独立轮，每条攻击带 file:line）；每条"要修"先分 **缺口真 / 解法错 / 时机错**——撞 freeze 或 test-lock 即时机错，不是 bug；动手前确认目标不是 deliberately-frozen（measure-first）。结论常是"先别建，把度量做完"。真要新增能力，只留经对抗筛剩、且非冻结的那一个。
**对"候选建议"用四问门（luca 2026-06-12 明确指示）：** 任何"可以做/建议做"出口前必须依次过——①问题定义是否成立（取证机制事实：触发条件/使用时机/命中率，不是想象）②如何解决 ③这样解决是否有利（收益×命中率 vs 全链路改造成本）④是否影响既有逻辑（红线/有意设计的 commit/架构原则）。"是否是问题"永远先于"怎么修"；没过①不得给"低风险/可以做"的评价。
**合规/红线维度尤其不能只凭推理判 HELD**：对每条不变量配一个跑代码的验证（本次 `groundCheck` 单测当场把"红线 HELD"翻成"真逃逸"）；deferred 的 QA 维度恢复后必须真补跑，不得用"自审兜底"顶替（顶替会以为覆盖了实则没有）。

关联 [[feedback_evidence_grounded_debate]]（每条论点工具验证）、[[feedback_verify-your-verification]]（信绿前确认脚本真考验目标）、[[feedback_systematic-not-whackamole]]。

---

## 并入：feedback_redteam-socratic-before-plan-entry（2026-07-02 治理合并，内容原样保留）

给 luca_gstack(或其派生 fork)做复杂新增(如"需求→原型 Loop"这类多 skill 架构提案)写计划前，luca 明确要求一套强制流程，不是默认的"3 Explore + 1 Plan + 轻量 AskUserQuestion"：

1. 先开多个 subagent 对**真实代码/机制**做全量落地核验(ground-truth)——提案文档(哪怕文档本身写得像研究报告、带一手学术引用)里"已有 XX 钩子/已有 YY 机制"这类断言必须逐条查实，不能因为论证详尽就当既成事实直接采纳(本次提案 md 就假设了 PostToolUse 钩子已做 FxUI token 强制、FxUI 是 luca_gstack 自带词汇表——这些都需要拿真实文件核验，不能照抄)。
2. 每条具体架构决策(不是笼统 thesis)必须经**独立红队对抗**——专门 refute agent 全力反驳，再由独立 judge 权衡裁决 SURVIVES / NEEDS_USER_INPUT / REJECTED_REDESIGN；判官不能因为草案说得自信就默认 SURVIVES。
3. 红队后仍是真判断分歧(reasonable people 会不同意)的决策，走**苏格拉底追问**(最多约4轮 AskUserQuestion)直接问用户裁定，不得自行拍板。
4. 只有走完①②③、且证据经得起推敲反驳的决策，才能写入最终 plan 文件。

**Why:** luca 明确指示("任何方案你都不能直接做决策，你要调用红队...然后进行苏格拉底询问，最多4轮，以后才能写入你的计划。所有的方案都需要有佐证...经得起推敲和反驳的")；他把提案文档明确定性为"基础需求"(起点/参考)而非可直接执行的规格，防止我把一份写得像研究报告、带论文引用的文档误当"已验证事实"整篇执行。

**How to apply:** 遇到用户给一份带外部引用/学术论据的架构提案文档，要求基于它给项目做落地方案时，默认走这套四步流程；不要因为文档本身论证详尽就跳过对真实代码的核验，也不要把文档的结论当成不需要红队的既定事实。可用 Workflow 工具编排(explore并行→draft决策→pipeline(refute→judge))做红队阶段，苏格拉底阶段回主循环用 AskUserQuestion 逐轮问。

关联 [[feedback_redteam-own-analysis-before-shipping]](红队自己已得出的结论)、feedback_research_default_complex_novel(复杂且新颖任务研究默认动作)、[[feedback_skill_routing_verify]](调用前先核实真实职责/参数)。

---

## 并入：feedback_proactive-checkpoint-before-execution（2026-07-02 治理合并，内容原样保留）

luca_gstack 的 CLAUDE.md 早已文档化 Checkpoint 触发条件（"已启动 ≥2 个重型 Agent"、"即将执行不可逆操作前"），但我在实际执行中不会主动套用自己项目的这条规则——本 session 跑完两轮 Workflow(52 agent、约380万 subagent tokens)验证一套架构后，直接准备转入执行/调 ExitPlanMode，没有主动写任何 checkpoint；luca 明确叫停："在执行前，我要你落几个 md 文件...保障重点项不随 context 压缩丢失，该用的时候知道去哪里找。所以你做好前置准备。然后再开始执行。"

**Why:** 这不是新知识——规则本来就在 CLAUDE.md 里——而是我没有主动执行自己项目已经写明的协议，需要 luca 提醒才做。重型验证阶段(多 Workflow/多 subagent)产出的决策链条一旦只留在对话里，context 压缩后就可能永久丢失，而后续 session 会静默按"已定案"执行,跳过本该被重新审视的部分。

**How to apply:** 一旦某 session 跨过"≥2 个重型 Agent/Workflow 调用"或即将从长研究/验证阶段转入实际执行（尤其是要写代码、建 skill、动不可逆操作前），**主动**(不等提醒)先把关键决策落成持久文件（项目 ARCHITECTURE 类文档 + 项目本地 memory 索引，视 CLAUDE.md 三分表归属而定），再继续执行。适用范围不限于 muse-loop，任何触发 CLAUDE.md 自身 Checkpoint 条件的场景都应如此。

---

## 并入：feedback_recheck-completeness-claims-against-sources（2026-07-10 治理合并，内容原样保留）

---
name: recheck-completeness-claims-against-sources
description: "被问\"是否做完/是否满足/是否引用全了\"这类元问题时，别凭记忆自信回答——先重新核对真实文件/原始来源再答"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b0787b44-da7c-434e-a353-fd3ad1c90d76
---

被问"你都跑完了？"/"只有这几步吗？"/"是不是看了全部该引用的源头才写的？"这类完整性/溯源类元问题时，**不要凭记忆里"我应该已经做了"直接回答**——先重新读真实文件（Read/grep）或重新抓真实原始来源（WebFetch/WebSearch），再回答，哪怕自己感觉很确定。

**Why：** 在同一个 session（2026-07-01/02，muse-loop 需求→原型 Loop 建设）里，这个模式连续复现了 5 次以上，每次凭自信回答都被查出真实缺口：
1. "都跑完了？" → 先答"是"，实际从未端到端跑过一次
2. "只有012三步吗" → 先答"是"，实际漏了整个 `traceability.md` 文件
3. "constitution.md 来自 Kiro steering files" → 实际字面命名是 Spec Kit 的，不是 Kiro 的（真查文档才发现）
4. "EARS 语法采纳了" → 实际只用了4种模板里的1种（事件驱动 WHEN...SHALL），因为3个测试样例恰好都是这类，不是真的验证过其余3种不需要
5. "tasks.md 的依赖并发机制已被 task-plan 覆盖" → 实际 task-plan 只有一句自由文本"依赖"字段，跟 Kiro 真实的"依赖图→分组并发执行波次"完全不是一回事

每次都是**重新去读文件/抓原始文档之后才发现真实差距**，纯粹靠"我记得我做过/检查过"全部落空。

**How to apply：** 遇到用户问"完整性/覆盖率/溯源"类问题（"做完了吗"、"满足诉求了吗"、"引用全了吗"、"覆盖了吗"）——尤其是被同一个用户在同一session里反复这样追问时——把这当成"必须重新核验"的触发信号，而不是"复述总结"的信号。跟 [[feedback_verify-your-verification]] 是同一条"验证铁律"精神的不同应用面：那条针对"跑测试套件验证修复"，这条针对"重查真实文件/原始来源验证事实性完整性声明"，两者都拒绝"凭印象/凭记忆"作为答案依据。

---

**8-13 补一面：独立评审官的 finding 也要拆成「现象」和「归因」分别验——现象常真，归因常错。**
同一份报告里两次实测反证：① 它报「会话面板 108ms 卡主进程」（现象真），归因说是我某行改动让计数器
永不归零——实测本机匹配会话只有 13 个、那个分支根本走不到，**改前一样慢**；照它的归因去修等于修错地方，
真根因是每次刷新都重读 200KB 的 `session_meta` 首行，得加缓存。② 它报某个变异「逃逸了、是测试缺口」，
实测那是**等价变异**（`break`→`continue` 两种写法打开的文件完全相同），去构造一条能"抓住"它的断言
就是在造假断言——正确做法是改去守那行的**真实**性质（拿掉过滤会认领 spawn 之前的老会话）。
**判据**：「它测到的现象我能复现吗」和「它说的因果我能独立验证吗」是**两个**问题。
只验了现象就照它的归因动手，最典型的后果是修了个没病的地方，还把那个错归因写进注释当事实。
不采纳时要给出**反证数据**，不是"我觉得不是"。

## 索引原文存档（2026-08-15，G5 瘦索引前的完整索引行，逐字保留）

- [证据+红队自审](feedback_redteam-own-analysis-before-shipping.md) — 对抗辩论/红队评审每条论点都要工具验证不空辩；框架自审我系统性over-claim"要现在修的bug"，上报前先红队自己结论(分缺口真/解法错/时机错)，2026三次红队各翻4/9·6/11·5/7；**候选建议同样会over-claim**——"可做/低风险"出口前必过四问门，"是否是问题"先于"怎么修"(6-12复发)；**全局"都解决了"声明前先枚举审计维度逐维过；系统已flag的信号(启动digest/治理告警)不得当"待裁决"放过——已flag即是问题(6-24)**；带外部引用的架构提案入计划前必须①subagent全量核验真实代码②每条决策独立红队refute+judge③真分歧走AskUserQuestion苏格拉底裁定(luca明确指示)；**承重结论(尤其全称否定"X不存在/从不")不得只建在subagent转述上，必须直读一手文件(7-10流程评估v1三处承重论断全因未直读被推翻，用户点名"结论草率")；8-03 升级——直读也不够：否定性断言("没有出口/没有出处")举证标准高于肯定性断言，每条须配定向grep拿到0才算数，且同一分类两侧必须同法取证(一侧查文档一侧查git blame=不可比；blame在一次性import的仓库里是85-96%基准率零区分度)，本session自己刚改过的文件当已失效记忆先git log看一眼**；≥2重型Agent/Workflow后转执行前主动落盘Checkpoint不等提醒(6-1)（并入 [[feedback_evidence_grounded_debate]]、feedback_redteam-socratic-before-plan-entry、feedback_proactive-checkpoint-before-execution、feedback_recheck-completeness-claims-against-sources：被问"做完了吗/引用全了吗"这类完整性元问题先重查真实文件/原始来源再答，别凭记忆(7-2 同session复现5次)）
