---
name: symptom-first-before-acting
description: 没见过症状不许动手——尤其不做破坏性动作；诊断成立的最低标准是能解释症状的全部特征
metadata:
  type: feedback
---

拿不到症状（截图 / 原文 / 报错）就不开查、更不动手，**尤其不做破坏性动作**。

**Why:** 2026-07-15 我从头到尾没问过「跳到 api」具体长什么样，就靠一句文档措辞推出「ant profile
盖过 Max 登录」的故事，并据此执行了 `ant auth logout` —— 删掉的是本来就不参与认证的东西，问题一个
没解决。该结论事后被 telemetry 实证推翻（6 条 `tengu_wif_implicit_profile_skipped_stored_login`
证明 CLI 明确跳过了 profile，机制方向是反的）。这不是运气不好，是方法论失败：三处硬伤当时全摆在
眼前而我绕开了 —— ① 纯文档零运行时 ② 过期 25h 的 token 解释不了「每次」这种长期症状 ③ 我用来
证伪 `opus[1m]` 的那条证据同时也在证伪我自己的假设，那一刻就该判死，我却继续推荐了 logout。
把「文档规定」当「实际行为」讲，还据此动了手（参见 [[feedback_verify-with-real-evidence-before-reporting]]）。

**How to apply:** 排障第一句永远是「你看到的具体是什么？截图 / 原文 / 报错」。诊断成立的最低标准
＝能解释症状的**全部**特征，有一条解释不了就不成立（本案「每次都发生」这条，过期 token 根本解释
不了，我却没让它否决假设）。任何一条证据同时在证伪自己的假设 → 立刻停下重判，别把已经开始的叙事
推下去。

**已证伪·别再往这查：** ant profile 不会盖过 Max 登录（telemetry 实证）。唯一残存线索是
`.credentials.json` 的 `claudeAiOauth` 缺 `scopes` → `VG(undefined)=false` → 跳过分支不触发 →
profile 反而会赢；下次带症状再看，别当结论。

**本案未结（间歇性）：** 启动显示「api」而非 max，非必现 → 静态配置错误已排除，指向时变因素。
嫌疑 A（已被动消除，观察期）＝ scopes 缺失路径，`ant auth logout` 已删 profile credentials，
若此后不再出现即是它。嫌疑 B ＝ extra usage 溢出（`hasExtraUsageEnabled` + `opus[1m]`/xhigh 高耗，
与「间歇性」高度吻合）。**下次出现即执行：** ① 截图「api」所在的确切界面 ② 记时间 ③ 记从哪开的
session ④ 立即看 claude.ai 用量页有无 extra usage 扣费记录。拿到一次现场即可结案。
