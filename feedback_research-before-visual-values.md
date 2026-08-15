---
name: research-before-visual-values
description: 视觉/感知类参数（色值/间距/配色）不许手拍——先调研定范围，范围内的具体值必须过用户眼睛；研究推荐≠用户舒适
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6835476b-22b2-4b83-a52f-72c1a0baf4b1
  modified: 2026-07-31T03:21:06.202Z
---

视觉/感知类参数（色值、调色板、间距这类用户"看得见、感受得到"的值）不许凭手感拍。luca 2026-07-31 明确批评：「这些符合视觉规范吗？应该不符合。你没有认真调研去做」——我给浅色终端手拍了一套 ANSI 调色板，没有任何依据。

**Why:** 这类参数没有编译器/测试兜底，错了直接落在用户眼睛上；且"看起来合理"的手拍值会连带下游（diff 渲染、对比度）出难看后果。同一 session 内第二课：调研（quick-research，primary source）把 Gruvbox light0 定为推荐起点，用户实看后仍否掉（太黄）——**证据只能框定范围（明度/色相方向/对比度门槛），范围内的具体值是口味题，研究推荐≠用户舒适**。

**How to apply:** ①动视觉参数前先跑轻量调研（官方色值/一手研究/规范），承认证据边界（查不到的如实标"未证实"）；②范围内的候选值用真机截图给用户看着选（AskUserQuestion 或快速迭代循环），不要替用户拍板"最适"；③优先从产品自己的品牌色族延伸取值（本案胜出值即来自 app 壳层暖沙族），比外来官方主题更容易协调；④被打回时改一个值的成本很低，把迭代做便宜比一次做"对"更重要。与 [[feedback_extraction-bar-major-only]] 无关，与 [[feedback-research-before-building]]（造能力前先调研）同族——这条把同一纪律扩展到视觉参数层。
