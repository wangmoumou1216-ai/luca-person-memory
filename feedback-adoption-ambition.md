---
name: feedback-adoption-ambition
description: "能力演进别保守——新增框架/场景/skill + 替换老 skill 都是合法产出,高价值高置信即执行不打折;更一般:手段性约束(硬闸/纪律/预算)是保高置信非否决价值,别当挡箭牌回避净值判断"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a84caa16-23a0-4f8a-9112-f5f043226c16
  modified: 2026-07-22T02:25:12.790Z
---

评估外来能力/做框架演进时,合法产出不止"补强合并"——**①新增框架能力 ②新增场景能力
③新增 skill ④替换老 skill** 四类都被鼓励,只要**覆盖产品设计开发主线且高价值高置信**。
结构化门槛(替换的 Pareto 支配+红队存活+回归证明、propose-only、双仓、承重墙)是用来**保证
"高置信"这半个条件**的,**不是**用来把结果往 leave/merge 更安全方向压。判据满足 → 该替换就
替换、该新增就新增,不因"怕动结构"保守回避。

**Why:** luca 2026-07-11 起两次强调——"只能是提升不能破坏,但高置信高价值的大升级/替换都可以"、
"新增框架能力、场景能力、skill 能力,也可以替换老的 skill"。mattpocock 对标里我一度 0 replace
偏保守,luca 明确纠正要有雄心(2026-07-12,rubric §3.5 落地)。这是对"评估姿态"的行为指示,
generalize 到任何框架演进。

**How to apply:** 评估产出里给每个 APPROVE 项标 capability_type(framework/scene/skill/replace);
对高价值 merge 强制回检"为什么不是 replace"——若答案只是"没敢动"而非"现任某承重轴真赢"则翻
replace 候选交用户。承重墙/propose-only/双仓一字不松,松的是**评估视角不是落地门槛**。

**更一般的母原则(2026-07-22 luca 价值追问确立):** 上面是"能力演进"这一具体场景,底层是通用反射——
**任何手段性约束(硬闸/纪律/预算门)都是服务于价值的手段,不是否决价值的目的**。每次冒出"因为撞了
X 约束所以不做"时,分离两问:①**这事净值正吗**(价值判断) ②**怎么做不撞 X**(实现路径,如扩展已有
机制而非新建);别让第二问的答案冒充第一问。约束只决定"怎么做",不决定"做不做"。
**实例:** 2026-07-21 收口 Pass 我用"默认不新建机器"硬闸把 D3(framework/ 的 Bash 向量防护)裁"不做",
luca 一句"从价值上说正向就做"戳破——三个错误:㈠"0 历史命中=死代码"谬误(保护区价值在**一旦命中
的代价**不在频率) ㈡"撞硬闸"当挡箭牌免掉判断 ㈢没去找不撞闸的实现路径(实际可扩展已有
project-scope-guard,不算新建)。改判后 D3 落地。**这是同族反面:** 不是把失败教训泛化成禁令
([[feedback_dont-overgeneralize-failure-lessons]]),而是把项目纪律绝对化成不做正向事的借口。
关联 [[feedback-orchestration-integration-on-adopt]]、[[feedback-research-before-building]]。
