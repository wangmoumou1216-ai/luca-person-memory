---
name: extraction-bar-major-only
description: 经验提取默认不存，只取重大经验——四信号（明确纠正或指示/二次复发/真实返工或不可逆险情/高成本事实）；对话中途仅信号①即写，其余 session 结束统一裁决
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4df4af9b-5629-41fe-8c39-2cd84028c9ad
---

luca 纠正（2026-06-10）：不是每个问题+解决方案都值得提取成记忆，只提取重大经验。同 session 内 agent 一小时连写 2 条全局 feedback 即此病。

**Why:** 全局记忆每 session 全量注入 context，按对话日志密度存会噪音化、稀释重大教训。

**How to apply:** 默认不存，仅命中四信号才提取：①用户明确纠正或对未来行为明确指示（唯一允许当场直写）②同类问题二次复发（先换词搜前科，首次只记 episodic）③真实返工或不可逆红线险情 ④重获成本高且确定复用。信号②③④的全局写入走 `candidate_feedback_<slug>.md` 冷静期候选（不进索引，digest 裁决后入册）。完整定义与按层分级：luca_gstack 仓 `.claude/skill-os/extraction-bar.md`（唯一真值源）。
