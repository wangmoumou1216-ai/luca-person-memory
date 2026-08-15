---
name: audit-needs-runtime-partition
description: 深审/多轮审计必须含一个"真跑运行时"分区——纯静态视角(读码/对抗/新眼)会集体漏掉只在运行时才暴露的崩溃
metadata:
  node_type: memory
  type: feedback
  originSessionId: 0961ecdf-bcba-4f92-a28f-51d34cb10848
  modified: 2026-08-06T08:51:20.482Z
---

对代码做多轮对抗审计时，**至少有一轮要真跑运行时**（在目标环境里 spawn 真实进程、喂真实输入、看
实际 stdout/stderr/退出码/建了哪些文件），不能全是静态视角（读码、跨源对账、门强度 mutation、
"新眼全 diff 对抗"）。**静态分区即便轮换、即便对抗、即便新眼，也会集体漏掉只在运行时才暴露的缺陷。**

**Why:** 2026-07-28 跨-agent 适配的全生命周期审计 loop。Round1-4 用了 5 种静态分区（子系统 /
per-commit / per-类型 / 门强度+覆盖 / 新眼全-diff 对抗），Round4 四个独立静态视角**全部判"干轮"**。
唯独 Round5 换成**运行时端到端 sim**（模拟云端：CLAUDE_PROJECT_DIR 有但 PATH 无 python3，真跑
session-restore）——一跑就抓到一个 MAJOR：daily_governance 的 **detached spawn 没有 'error' 监听器**，
python3 缺失时 ENOENT 走 **async 'error' 事件、外层 try/catch 拦不住** → 未捕获异常、hook 退出 1、
吐整段 Node 崩溃栈盖过干净告警。这是"每次云端 SessionStart 都崩"的用户可见缺陷，4 个静态轮全漏。
根因：静态读码看到 spawn 在 try{} 内就以为被守护，但 async 事件不进 try/catch——只有真跑才现形。

**How to apply:**
- **收敛判据里把"运行时分区"设为必备维度之一**，不是可选。"连续两干轮换分区"若两轮都是静态分区，
  不足以证明收敛——运行时缺陷会同时躲过它们。至少一轮真跑目标环境（云端/headless/异 harness）。
- **运行时 sim 要真造目标环境**：PATH 剥掉某二进制、env 剥掉某变量、cwd 挪到仓外、指向不存在的路径——
  然后 spawn 真 hook/脚本，断言"优雅降级 exit 0 + 失败可见 + 不崩不建幻影树"，而非读码推断。
- **具体技术陷阱（会复发）**：`spawn(detached)` / 任何 child_process 的 ENOENT 走 **async 'error'
  事件**，同步 try/catch **拦不住**——必须显式 `child.on('error', () => {})`。审到 spawn 外部二进制
  的代码，直接查有没有 error 监听器，别信"它在 try 里"。
- **硬化某路径时，找它的姊妹路径**：这次同步 execSync(get_memory) 路径处理了 python3 缺失，却漏了
  detached spawn(daily_governance) 姊妹路径——同一失败模式(python3 缺失)在自己的硬化面内有第二个入口。
  修一处失败分类/降级时，grep 同类调用（execSync/spawn/exec）全部入口，别只修撞见的那一个。
- **判断「静态扫描会在哪儿漏」的可操作判据（2026-08-06 第三次实证，本次是扫描类审计而非读码类）：
  问一句「这个对象的真实值是不是运行时才解析出来的？」——软链目标 / env 展开 / 动态拼接的路径，
  统统是。是 → 静态扫描**结构上**扫不到，必须配运行时探针。**
  实证：另一个 session 系统性扫过「工作根之外的必写路径」（`git ls-files` 过可执行文件、提取
  `$HOME`/`~`/`/Users/luca` 绝对路径），扫出三条并逐条修好、加断言加变异验证——**方法很扎实，
  但仍漏了第四条**：`docs/`·`workflow-state`·`current-topic` 是软链，脚本里只写相对路径，
  绝对路径只在运行时由软链解析才出现。漏的这条影响面最大（所有 skill 产出 + 流程状态静默写失败）。
  我这边只是随手跑了个「沙箱里真写一下」的探针就现形了。
  ⇒ 凡做「扫一类问题」的系统性审计，**扫描结果不等于覆盖**；对运行时才成形的对象补一轮真跑探针，
  成本极低（几行 shell）而收益是整类漏网。
- 同族：[[feedback_redteam-loop-then-deep-review-big-changes]]（大改动多维深审）——本条是它的补充维度：
  多维里**必须有运行时那一维**。feedback_reproduce-real-env-not-approximation（真复现环境）——本条
  把"真复现"从调 bug 扩展到"审计收敛判据"。
