---
name: restore-granularity-matches-test-change
description: mutation/hook 测试后的还原动作粒度必须≤测试改动本身——git checkout 整文件会冲掉同文件其他未提交工作
metadata: 
  node_type: memory
  type: feedback
  signal: ③真实返工（2026-07-29 claude5-unhobble：commit-msg hook 测试收尾 git checkout -- CLAUDE.md，把同文件未提交的 5 条指针改造一并冲回 HEAD 态，S31 校验器抓到后全部重做）
    originSessionId: 9f426dac-c8b7-4338-88da-f9d088b9fb30
  modified: 2026-07-29T05:29:38.595Z
---

在文件上做临时改动测试（mutation 探针、hook 触发试验）后，还原动作的粒度必须与测试改动
同粒度：测试前先 `cp <file> /tmp/xx.bak`、测试后 `mv` 回来，或精确逆向该行编辑。
**绝不用 `git checkout -- <file>` / `git restore` 整文件还原**——它恢复到的是 HEAD/index
态，会连带冲掉同文件里**其他未提交的正经工作**（测试改动和在途工作常在同一文件上）。

**Why:** 2026-07-29 执行 claude5-unhobble 时，commit-msg hook 三态测试在 CLAUDE.md 上
append 探针字节，收尾 `git reset + git checkout -- CLAUDE.md` 把刚做完、尚未提交的 5 条
appendix 指针改造整体冲掉；靠新装的 S31 奇偶校验器变红才发现，重做全部改造。

**How to apply:** 测试涉及会被改动的文件 → 开测前 `cp` 备份该文件，收尾用备份精确还原；
或把测试排在该文件的在途工作提交之后。含 [[feedback_verify-your-verification]] 同族。
