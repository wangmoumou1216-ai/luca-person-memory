---
name: no-inplace-edit-for-lookups
description: 想"查一下"就用 grep——`sed -i`/`perl -pi` 是破坏性开关，用它做查找会静默改坏文件；批量原地编辑后立刻做语法/差异体检
metadata:
  node_type: memory
  type: feedback
  status: candidate
  signal: 3-rework-nearmiss
  originSessionId: 919bccc7-72d9-47ac-8059-5044e28c41c5
  modified: 2026-08-13T07:28:01.522Z
---

**`-i` 是破坏性开关。要"看一眼有哪些调用点"就用 `grep`，永远不要拿 `sed -i` / `perl -pi` 去顺便查。**

2026-08-13 实撞：我想列出 `codexReadMeta(file)` 的调用点，敲的却是

```bash
perl -pi -e "s/const meta = codexReadMeta\(file\);\n/XX/" cli-providers.js; grep -n ... 
```

结果 grep **一条都没输出**——不是"没有调用点"，是那条 perl 已经把三处调用**全部替换成 `XX` 并把行黏到了下一行**，
文件当场语法坏掉。我差点把这个空 grep 当成"调用点只剩一处"的事实继续往下推。

两个叠加的坑：
1. **命令语义搞反**：脑子里想的是查找，手上写的是替换。`-p` 是"逐行处理并打印"，加 `-i` 就是原地写回。
2. **`perl -p` 的 `$_` 含行尾换行**，所以模式里的 `\n` 会匹配上并把两行合并——即使我"只想改一行"，
   实际后果是把下一行也吞进来。（`grep` 永远不会这样。）

**How to apply:**
- 查找一律 `grep -n` / `rg`。真要批量改，先用 `grep -c <锚点>` 确认命中数**符合预期**再动手。
- 任何批量原地编辑（`sed -i` / `perl -pi` / 脚本重写文件）之后，**下一条命令**就是体检：
  `node --check <file>`（或对应语言的语法检查）+ `git diff --stat`。别等到跑测试才发现。
- **空结果先怀疑装置**：grep 突然一条不命中、命令突然安静，第一反应是"我刚才是不是把输入弄坏了"，
  不是"事实就是零"。这与 [[feedback_verify-your-verification]] 的「全格失败先怀疑共因」同一族。
- 单点、可读回的改动优先用 Edit 工具（它对不上会报错、且必须先读过文件），
  比一条不可见后果的 shell 替换安全得多。

相关：[[feedback_no-unquoted-heredoc-with-backticks]]（另一种 shell 静默污染文件的形态）、
[[feedback_restore-granularity-matches-test-change]]（改坏之后怎么精确还原）。
