---
name: verify-ground-truth-when-outputs-unreliable
description: "怀疑自己幻觉/终端输出不可靠时用独立地面真相通道核实（写文件→Read工具、git ls-remote验push、gh pr list验PR、CI），绝不信\"我已push/建PR/CI失败\"的叙述；受损session不单方面按不可逆框架级操作"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 2c69b9b5-3721-48f8-9777-80c418e679f4
---

2026-07-04 优化 luca_gstack 母版流程，token 上限重开后**严重幻觉**：编造了假 commit 哈希
（6ba9e17/8f3c21e/6001d8b）、假 push、假 PR #5/#6、一整套不存在的"CI 在 S14 失败"排查——
追了十几轮，而当时**根本没 push、没 PR、CI 从没跑过**。根因＝轻信自己叙述的工具结果 +
终端缓冲损坏（输出重复/乱码，连 cat/grep 在终端里都被污染）。luca 因此严重担心文件被污染。

**破幻觉的方法（复用这套）：**
1. 每条命令输出**写文件，用 Read 工具读**——绕开损坏的终端缓冲。
2. 副作用的存在性走**本地幻觉伪造不了的通道**核实：push→`git ls-remote --heads origin <branch>`；
   PR→`gh pr list --head`；CI→`gh pr checks`；已提交状态→`git rev-parse`/`git diff main...HEAD`
   （git 对象/远端 ref/CI 都在本地叙述之外，伪造不了）。
3. 绝不凭叙述报"做完了/已 push/已合并/CI 绿"——每一条都从地面真相重新推导。
4. 抓到任何不一致（哈希 resolve 不了、"push 成功"却无 remote ref）→立即停，切 file+Read 核实，
   重建地面真相再继续，别顺着幻觉往下追。

**受损 session 的治理：** 出过幻觉的 session **不单方面触发不可逆的框架级操作**（合并到 main）。
做法＝把可核实的去险关一个个关掉（实测/红队/独立审计），把最后那一下不可逆触发留给用户或
干净新 session。这不是推卸，是切断"受损判断→不可逆真值"这条链。

**Why:** 幻觉是复发风险、context 重载后尤甚；这次代价＝十几轮凭空 debug + 差点报假完成、
让用户严重担心文件被毁。独立通道核实是唯一能穿透幻觉的东西。
**How to apply:** 见上四步；不可逆外向操作在状态存疑时先做可逆去险、推迟触发。核实文件是否被
污染时：`git diff main...HEAD --diff-filter=D`（查删除）+ 不知情独立 agent 逐信号审计 diff +
真机跑测试，别只凭自己复述。关联 recheck-completeness-claims-against-sources、
[[verify-your-verification]]、[[redteam-own-analysis-before-shipping]]。
