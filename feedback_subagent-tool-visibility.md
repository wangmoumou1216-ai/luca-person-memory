---
name: subagent-tool-visibility
description: luca 要 subagent/workflow 工具调用可见；默认前台内联执行，delegate 必先声明+/workflows，透明度优先于 ultracode 并行
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 430a2cf1-1a06-4f5b-bbb9-b9032eb1cfea
---

luca 明确要：subagent（Agent 工具）与 workflow（Workflow 工具）的**内部工具调用对他可见**，不要藏在 subagent 里只回最终结果。

**约束（为什么不能直接给）：** harness 把 subagent/workflow 跑在隔离上下文，内部工具调用**不进主 transcript**——我拿不到、也塞不进主流，只能拿最终返回。改不了平台。

**How to apply：**
- **默认前台 / 主会话内联执行**：用我自己的 Bash/Read/Grep/Edit 逐条调用做活，每条对他可见。这是默认动作。
- 只在**并行 / 规模确有价值**（多 agent 对抗 review、大 fan-out、跨多文件扫描）时才用 subagent/Workflow，且**事先一句声明**（"启动 N agent 的 workflow 做 X"）让他能 `/workflows` 看实时进度；跑完 relay 关键结果。
- 与 **ultracode**"每个任务都上 workflow"冲突时，**偏向可见的前台执行**——透明度优先于并行省时。

**Why：** 他要能看到 agent loop 怎么跑（同源诉求：这次还专门给研究情报官 app 建了 ReAct 玻璃盒）。看不到推理/工具/停因 = 黑盒，他不接受。

关联 [[no-confirmation-loops]]（都是"让 luca 留在回路里"的工作方式）。
