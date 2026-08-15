---
name: verify-repo-with-git-c-not-cd-chains
description: "核查\"另一个仓库\"的git状态时用 git -C <绝对路径>，不用 cd A && ... && git status 链——cd 会传染整条链或持久漂移到后续多轮调用，已4次差点把fork/母版状态互相误报"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b0787b44-da7c-434e-a353-fd3ad1c90d76
---

需要核查**另一个仓库**（如母版 luca_gstack）的 git 状态时，用 `git -C /绝对/路径 status`，绝不用 `cd 仓库A && 其他命令 && git status` 这种链——`&&` 链里 cd 生效到最后一条，git status 实际跑在仓库A里。

**Why：** 同一个错误在 2026-07-01/02 的 muse-loop session 里出现3次：前两次真的跑错（把 fork 的未提交改动当成母版状态看），第3次（07-02 前向测试收尾）又写出同样的链、输出显示母版有 `plan-agent.md/route-guard.mjs` 等改动，险些误报"母版被污染"——实际是 fork 的正常未提交改动。每次都要靠事后觉察重查纠正，浪费轮次且有误报风险（如果没觉察，会触发一整轮错误的"母版污染排查"）。

**How to apply：** 跨仓库核查一律 `git -C <绝对路径> status --short`（一条独立命令，无 cd）；同理适用于任何"在另一个目录跑一条只读检查"的场景——要么 `-C`/`--prefix` 类参数，要么单独一次 Bash 调用让 cwd 自动复位，不并进已有 `&&` 链。与 [[feedback_verify-your-verification]] 同源：验证工具本身也要被验证跑对了地方。

**延伸（2026-07-03，同根新变体：不是 && 链，是持久 cwd 跨多轮漂移）：** 全量框架 review 修复阶段，为了在 fork 内提交而 `cd 到 fork 目录 && git commit`；commit 完没有显式 cd 回母版，此后连续好几个独立 Bash 调用（`bash scripts/verify.sh`、`npm run test:routes`、`grep`）全部静默跑在 fork 里而非母版——报出的 FAIL、显示的旧测试名，一度让我误以为"母版的修复丢了/没生效"，差点据此重复排查一轮根本不存在的问题。用 `pwd`+`git branch --show-current` 核实才发现是 shell 持久 cwd 飘到了 fork。**根因和已有教训一致（跨仓核查别让 cwd 隐式传染），触发方式不同**：不是同一条命令链内的 `&&`，而是"因某个必要操作 `cd` 过去之后忘记 cd 回来，后续多条独立命令因此全部默认在错误目录执行"。**补充 How to apply：** 任何时候用 `cd <dir> && <action>` 切换过 cwd 去做一次性操作（如跨仓 commit），操作完成后立刻显式 `cd` 回原工作目录，或紧跟一次 `pwd` 自检再继续；不要假设"只是临时切过去"就不会影响后续调用——shell cwd 是持久状态，不会自动复位。
