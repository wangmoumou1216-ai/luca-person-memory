---
name: feedback-stay-in-session-scope
description: 别把别的 session 的活拖进我的上下文；「git」= 本 session 的仓库状态，不是全盘扫描
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 522aa1b3-637a-4474-b69c-fe8a16b75cbc
  modified: 2026-08-13T02:02:42.196Z
---

**「git」「状态」这类词的作用域 = 本 session 自己的仓库，不是满硬盘找活干。** 本 session 仓库干净
→ 回一行「干净，无可提交」就结束，不要 `cd` 出去扫别的项目、不要把别的 session 的 WIP 拉进上下文、
更不要为一份不归我管的改动摆 AskUserQuestion 让 luca 决定。

**跨 session 提醒的唯一条件：两个 session 在做同一个项目 且 真有提交冲突风险。** 两条都占才提醒。
luca 常年多窗口并行，各 session 各自提交自己的活 —— 别的窗口在写文件是**正常运行，不是险情**，
不得包装成告警。

**Why:** 2026-07-15 luca 明确纠正。当时我在跑认证排障（route-guard 已明说「本 session 尚未绑定项目」，
跟 muse 毫无关系），收到「git」后却扫了 `~/Desktop/项目/muse` 全部子仓，把另一个 session 正在改的
左栏代码拽进上下文，还把「app.js 几秒前被写入」渲染成紧急发现 —— 那只是隔壁窗口在正常干活。
原话：「你不能让别的session进入你的上下文吧？」「他跟你没关系」。这既是上下文污染，也是制造
不存在的问题让 luca 处理，还替别的 session 做了它才有上下文做的决定。

**写侧的举证门槛（2026-08-13 luca 明确指示，比上面的读侧更硬）：** 原话——「你如果有改动涉及到
luca_gstack 的话，**必须谨慎调研**，因为有可能其他 session 也做了 luca_gstack 的改动，此时你要想覆盖，
**必须拿出十足的证据**。」luca_gstack 是单真值源双检出、多 session 共享，别人的在途改动就摆在工作区里
未提交。所以：**动 gstack 之前先 `git -C <检出> status`**；看到不是我的修改文件，默认结论是「这不是我的，
我不动」，不是「我顺手带上」。想覆盖/合并才需要举证，而举证标准是**十足**（能说清那处改动是谁的、
为什么该被我的版本取代），拿不出就停下问 luca。
实践形态（本 session 就是这么做的）：动手前先声明作用域——「本轮 findings 全落在 `muse/app/`，
gstack 一个字节不动」，连该仓的 `verify.sh` 都不跑（它此刻测的是别人的在途工作，红了也不是我的信号），
唯一的跨仓依赖只做**只读**核验。与 [[feedback_commit-muban-if-changed]]（只 `git add` 具体文件、
绝不 `-A`）、[[feedback_dual-harness-parity]]（改了 CLAUDE.md 就得改 AGENTS.md）是同一张网：
parity 要求两套一起改，**不构成**在别人在途工作上强行落笔的许可——真撞上就把 parity 那一半留给 luca 排期。

**How to apply:** 先问「这活是不是本 session 的？」不是 → 不看、不报、不问。判据用 route-guard 的项目
绑定态（未绑定 = 更没理由读项目文件），不是"哪个项目全局激活"。真撞上同项目并发时才提醒，且只陈述
事实不渲染紧急。与 [[feedback-autocommit-push-high-confidence]] 的边界：那条讲**本 session 自己**验证过的
改动直接提交，不含替别人提交。
