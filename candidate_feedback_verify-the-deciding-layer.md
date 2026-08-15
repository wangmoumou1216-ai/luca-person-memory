---
name: verify-the-deciding-layer
description: "验证前先问「最终结果由哪一层决定」——变异测试有三种恒绿形态，改产出内容要改落盘模版而非执行指令，两者都是验错了层"
metadata:
  node_type: memory
  type: feedback
  signal: 2,3,4
  status: candidate
  originSessionId: 6835476b-22b2-4b83-a52f-72c1a0baf4b1
  modified: 2026-08-03T11:00:38.561Z
---

2026-08-03 评审资产整合，同一 session 内**四次**验错层，三次是变异测试自己无效、一次是改错了层：

## A. 变异测试的三种恒绿形态（我三次都踩了，全是红队才发现）

1. **`while read` / 管道子 shell 吞掉 exit** —— `grep ... | while read p; do ... exit 1; done`
   的 `exit 1` 只结束子 shell，外层仍 0。**用 `for p in $(...)` 或 python，别用管道进 while。**
2. **变异逃出了检查的正则** —— 我把 `module-a-visual.md` 改成 `module-a-visualXX.md` 期待转红，
   而检查的字符类是 `[a-z0-9-]`，**大写 XX 让整条路径不再被提取**，检查根本没看到它。
   **变异必须落在被测正则的捕获范围内**；先确认「变异后的内容确实还会被扫到」再看结果。
3. **空输入恒绿** —— `for p in $(grep ...)`，grep 无输出时循环体一次都不执行 → exit 0。
   于是「**删掉整张被守护的表**」这个最该转红的变异反而通过。
   **凡是"遍历命中项逐个校验"的检查，必须额外断言命中数量下限**，否则它守不住"目标整个消失"。

**通用判据**：写完变异测试先问一句——「**如果被守护的东西整个不见了，这个检查会不会转红？**」
不会 → 它守的是内容不是存在性，补一条数量/存在性断言。

## B. 改产出内容时，要改的是"落盘决定层"不是"执行指令层"

同 session 的 BLOCKER：我把量化门与 Slop 分级加进了 `design-brief/SKILL.md` 的 Phase 4 正文，
**没碰 `design-brief/SCHEMA.md`**。而 SKILL.md 自己写着「读取 SCHEMA.md 作为**字段级模版**」——
Phase 4 辛苦数出来的个数，写盘时按旧模版填、**全部消失**。改动在最后一步被自己的模版抹掉。

**操作**：往一个流程里加产出字段前，先 grep 该 skill 有没有独立的 SCHEMA / template /
output-templates 文件，以及正文里有没有「以 X 为模版/为准」的句子。**有则两处同改**，
且优先改模版（模版是落盘真值源，正文是执行提示）。

## 复发实证（写完本条数小时内，同 session）

往 `fixtures.jsonl` 加条目时发明了词表外的 `expected` 值（`insight_synthesis:dispatch` /
`no-review-hint`），而 `eval_routing.py:26` 头注就是**声明式词表**——决定 judge 口径的层是
那张词表，不是我觉得"形状像"的类比（我仿了 `review:dispatch` 的形状，但那是登记过的分流
形态，不是自造格式的许可）。**往任何有契约/词表/schema 的系统写东西，先读它的声明层。**

## 共同根因

四次都是**验证/修改的对象不是真正决定结果的那一层**。落盘由模版决定不由正文决定；
检查是否有效由 exit code 的传播路径决定不由循环体里写了什么决定。
**动手前先追一遍「最终结果由谁决定」，再决定改哪里、验哪里。**

关联 [[feedback_verify-your-verification]]（信绿前确认脚本真考验目标——本条是它的三个具体失效形态）、
[[feedback_extracted-module-must-verify-wiring]]（模块+单测全绿≠生效，要核调用方真 import）。
